---
title: Netcdf Editor Application Maintenance (old)
author: Wesley
toc: true
toc_sticky: true
excerpt: General information for working and ensuring the application is deployed correctly
---

# Introduction

The IPSL Boundary Condtion Editor is developped on [GitHub](https://github.com/Paleoclim-CNRS/netcdf_editor_app) and automatically deployed to [https://climate_sim.osupytheas.fr](https://climate_sim.osupytheas.fr).

## CICD
The automated workflow is the following:
1. Commit Changes to the github repository
1. When there is a change on `main` (either direct commit or a pull request) [GitHub actions](https://github.com/Paleoclim-CNRS/netcdf_editor_app/tree/main/.github/workflows) are triggered:
    - Python tests: ensure the code works
    - Build Docker Images
1. The GitHub Action for building the Docker images, builds the images (the app is composed of a stack of images see: [Doc](https://paleoclim-cnrs.github.io/netcdf_editor_app/multi#architecture)) and then pushes the images (updates the images) to:
    - [DockerHub](https://hub.docker.com/u/ceregecl) with the tag `latest`. This means that anyone can easily install the application (but there is a limit on the number of image pulls on dockerhub free 200?).
    - [OSU Infrastructure](https://docker.osupytheas.fr) at registry.osupytheas.fr (you need an osu account to access this) with the tag `latest` and a tag with the timestamp (previous versions are stored here). This is the preferred place to get the images as there is no limiting and is on local infrastructure.
1. On the OSU infrastructure (contact Julien Lecubin) where the app is deployed to [Watch Tower](https://containrrr.dev/watchtower/) is used. This tool monitors (certain) images every 5 minutes and looks to see if a new version is availble. If it is the case it will automatically download it, stop the previous container using the given image and redeploy a new container using the new image with the same configuration as the previous container.

# Redeploying a container

In certain cases it has been observed that containers can get into inconsistent states and needs restarting. This has been observed for the `nginx` container (noteablly when containers are redeployed see [CICD](#cicd) in different passes of watchtower, nginx builds quickly compared to other images and it may occur that not all images are redeployed at the same time) and the `panel` container (when a error occurs, the container remains useable for new websites but provides strange results).

## Instructions

1. Navigate to [Portainer](https://portainer.osupytheas.fr)
1. Use your OSU login details.
1. Click on the `dev` endpoint
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-home-old.png" alt="Portainer Home" %}
1. Click on stacks
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-dev-old.png" alt="Portainer Dev Endpoint" %}
1. Click on climsim
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-stacks-old.png" alt="Portainer Stacks" %}
1. Scroll down and check the container(s) that are not working
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-climsim-old.png" alt="Portainer Climsim Stack" %}
    <div class="alert alert-info">
        Pro Tip: If you are unsure you can access the logs of each container by clicking on the first icon under the quick actions for each container.
        <figure class style="justify-content: center;">
            <img src="/assets/images/climsim_maintenance/portainer-quick-actions-old.png" style="height:200px; width:auto">
        </figure>
        <ul>
            <li>
                The Second icon gives access to information about the Docker container (don't often need to see this)
            </li>
            <li>
                The Third icon shows the resource usage of the container
            </li>
            <li>
                The Fourth icon allows access to a shell inside the container. (VERY USEFUL!!!!)
            </li>
        </ul>
    </div>
1. Click `Restart` to restart the container.
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-restart-old.png" alt="Portainer Restart" %}

# Redeploying the Stack

This can be necessary after a power cut for example or if you accidentally removed a container.

## Instructions

1. Navigate to [Portainer](https://portainer.osupytheas.fr)
1. Use your OSU login details.
1. Click on the `dev` endpoint
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-home-old.png" alt="Portainer Home" %}
1. Click on Stacks in the sidebar.
    <figure class style="justify-content: center;">
        <img src="/assets/images/climsim_maintenance/portainer-sidebar-old.png" style="height: 400px;width: auto;">
    </figure>
1. Click on climsim
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-stacks-old.png" alt="Portainer Stacks" %}
1. Click on editor
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-editor-old.png" alt="Portainer Editor" %}
1. Change any code needs FYI you can look at the [docker-compose](https://github.com/Paleoclim-CNRS/netcdf_editor_app/blob/main/docker-compose.yaml)
    or the base config here:
    ```
    version: '2'
    services:
    nginx:
        image: registry.osupytheas.fr:443/netcdf_editor_nginx
        restart: on-failure
        ports:
        - 5556:80
        depends_on: 
        - flask_app
        - panel_app
        networks:
        - climsim

    message_broker:
        image: rabbitmq:3-management
        expose:
        - 5672
        - 15672
        networks:
        - climsim
    
    message_dispatcher:
        image: registry.osupytheas.fr:443/netcdf_editor_message_dispatcher
        restart: on-failure
        environment: 
        - BROKER_HOSTNAME=message_broker
        depends_on: 
        - message_broker
        networks:
        - climsim

    python_worker:
        image: registry.osupytheas.fr:443/netcdf_editor_python_worker
        restart: on-failure
        environment: 
        - BROKER_HOSTNAME=message_broker
        depends_on: 
        - message_broker
        - message_dispatcher
        volumes:
        - instance_storage:/usr/src/app/instance
        networks:
        - climsim
        
    flask_app:
        image: registry.osupytheas.fr:443/netcdf_editor_flask_app
        environment: 
        - BROKER_HOSTNAME=message_broker
        - FLASK_APP=/usr/src/app/flask_app/climate_simulation_platform
        - FLASK_ENV=production
        - FLASK_RUN_HOST=0.0.0.0
        - FLASK_RUN_PORT=5000
        expose:
        - 5000
        depends_on: 
        - message_broker
        entrypoint: gunicorn --bind 0.0.0.0:5000 "climate_simulation_platform:create_app()"
        # entrypoint: python -m flask run
        volumes:
        - instance_storage:/usr/src/app/instance
        networks:
        - climsim
    
    panel_app:
        image: registry.osupytheas.fr:443/netcdf_editor_panel_app
        environment: 
        - BROKER_HOSTNAME=message_broker
        - BOKEH_RESOURCES=cdn 
        - BOKEH_ALLOW_WS_ORIGIN=climate_sim.osupytheas.fr
        expose: 
        - 5006
        depends_on: 
        - message_broker
        volumes:
        - instance_storage:/usr/src/app/instance
        networks:
        - climsim

    mosaic_worker:
        image: registry.osupytheas.fr:443/netcdf_editor_mosaic_worker
        restart: on-failure
        environment: 
        - BROKER_HOSTNAME=message_broker
        depends_on: 
        - message_broker
        - message_dispatcher
        volumes:
        - instance_storage:/usr/src/app/instance
        networks:
        - climsim
        
    watchtower-climsim:
        image: v2tec/watchtower
        restart: always
        labels:
        - "com.centurylinklabs.watchtower.enable=true"
        environment:
        - TZ=Europe/Paris
        - WATCHTOWER_NOTIFICATIONS_LEVEL=info
        - WATCHTOWER_NOTIFICATIONS=email
        - WATCHTOWER_NOTIFICATION_EMAIL_FROM=watchtower@osupytheas.fr
        - WATCHTOWER_NOTIFICATION_EMAIL_TO=banfield@cerege.fr
        - WATCHTOWER_NOTIFICATION_EMAIL_SERVER=smtp.osupytheas.fr
        volumes:
        - /mnt/srvstk1d/SIP/docker/conf/watchtower-wesley/config.json:/config.json
        - /var/run/docker.sock:/var/run/docker.sock 
        command: v2tec/watchtower climsim_message_broker_1 climsim_nginx_1 climsim_python_worker_1 climsim_panel_app_1 climsim_flask_app_1 netcdf_editor_app_1 climsim_message_dispatcher_1
        networks:
        - climsim

    volumes:
    instance_storage:
    
    networks:
    climsim:
  ```
1. Click Update Stack
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-update-stack-old.png" alt="Portainer Update Stack" %}

## Volume Failure
<div class="alert alert-danger">
Only do this if you get: "Failure volume "instance_storage" ..."
</div>

1. Navigate to Volumes
    {% include figure image_path="/assets/images/climsim_maintenance/portainer-dev-volumes-old.png" alt="Portainer Dev Endpoint" %}
1. Search through for `climsim_instance_storage` if it exists check it and delete it. 
    <div class="alert alert-info">
        <p>
            Pro Tip: DO NOT DELETE <code class="language-plaintext">INSTANCE STORAGE</code> unless you want to delete the database used by the app
        </p>
    </div>
