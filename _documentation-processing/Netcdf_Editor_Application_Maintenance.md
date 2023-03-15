---
title: Netcdf Editor Application Maintenance
author: Anthony
toc: true
toc_sticky: true
excerpt: General information for working and ensuring the application is deployed correctly
---

<style>
    div {border-radius: 10px; margin: 10px 0px}
    pre {background-color:#fafafa; padding:15px 0px; padding-left:15px;}
    code {background-color:#fafafa; color:#8c1a48; font-size:small; padding: 2px 5px; border-radius: 4px;}
    img {border-radius: 10px;}
</style>

# Introduction

The IPSL Boundary Condtion Editor is developped on [GitHub](https://github.com/Paleoclim-CNRS/netcdf_editor_app) and automatically deployed to [https://climate_sim.osupytheas.fr](https://climate_sim.osupytheas.fr).

## CICD (Continuous Integration and Continuous Deployment)
The automated workflow is the following:
1. Commit Changes to the github repository
1. When there is a change on `main` (either direct commit or a pull request) [GitHub actions](https://github.com/Paleoclim-CNRS/netcdf_editor_app/tree/main/.github/workflows) are triggered:
    - Python tests: ensure the code works
    - Build Docker Images
1. The GitHub Action for building the Docker images, builds the images (the app is composed of a stack of images see: [Doc](https://paleoclim-cnrs.github.io/netcdf_editor_app/multi#architecture)) and then pushes the images (updates the images) to:
    - [DockerHub](https://hub.docker.com/u/ceregecl) with the tag `latest`. This means that anyone can easily install the application (but there is a limit on the number of image pulls on dockerhub free 200?).
    - [OSU Infrastructure](https://docker.osupytheas.fr) at registry.osupytheas.fr (you need an osu account to access this) with the tag `latest` and a tag with the *GitHub commit number* (previous versions are stored here) - [[Visual explanation]](/assets/images/climsim_maintenance/registry-osu.png). 
    This is the preferred place to get the images as there is no limiting and is on local infrastructure.
1. On the OSU infrastructure (contact Julien Lecubin) where the app is deployed to, [WatchTower](https://containrrr.dev/watchtower/) is used. This tool monitors (certain) images every 5 minutes and looks to see if a new version is available. If it is the case it will automatically download it, stop the previous container using the given image and redeploy a new container using the new image with the same configuration as the previous container.

Here is a scheme representing the workflow:
![Deployment Arch](/assets/images/climsim_maintenance/climsim-deployment-arch.png)
[[Full screen]](/assets/images/climsim_maintenance/climsim-deployment-arch.png)

# General Maintenance

## Get access to the containers
1. Navigate to [Portainer](https://portainer.osupytheas.fr)
1. Use your OSU login details.
1. Click on **dev**:
    ![Portainer home](/assets/images/climsim_maintenance/portainer-home.png)
1. Click on **Stacks**:
    ![Portainer dev](/assets/images/climsim_maintenance/portainer-dev.png)
1. Click on **climsim**:
    ![Portainer stacks](/assets/images/climsim_maintenance/portainer-stacks.png)
1. Stack details and list of containers will be displayed:
    ![Portainer Containers](/assets/images/climsim_maintenance/portainer-climsim-stack.png)

1. For each container, you have access to several options under the **Quick Actions** column:
    ![Portainer Quick Action](/assets/images/climsim_maintenance/portainer-quick-actions.png)
    1. <p><img src="/assets/images/climsim_maintenance/portainer-quick-actions-log.png" style="height:40px;"> log of the container</p>
    1. <p><img src="/assets/images/climsim_maintenance/portainer-quick-actions-infos.png" style="height:40px;"> informations about the container</p>
    1. <p><img src="/assets/images/climsim_maintenance/portainer-quick-actions-ressources.png" style="height:40px;"> resource usage of the container</p>
    1. <p><img src="/assets/images/climsim_maintenance/portainer-quick-actions-console.png" style="height:40px;"> console inside the container</p>
    
## Managing containers

You can perfom several actions over one or multiple containers.

For this, select them by ticking the box on the left side and then selecting the action you want to perform.
![Portainer Restart](/assets/images/climsim_maintenance/portainer-restart.png)

<div class="alert alert-info">
What you might need the most will be the <img src="/assets/images/climsim_maintenance/portainer-container-restart.png" style=""> action to redeploy a container in case it doesn't work properly.
</div>

## Redeploying the Stack

This can be necessary after a power cut for example or if you accidentally removed a container.

1. Once you are on the stack details and container list page (**step 6** of [Get access to the containers](#get-access-to-the-containers))
1. Click on **Editor**:
    ![Portainer Editor](/assets/images/climsim_maintenance/portainer-editor.png)

1. The docker-compose will be displayed here (You might need to do some changes in it):
    ![Portainer Update Stack](/assets/images/climsim_maintenance/portainer-climsim-editor.png)

1. Click **Update the stack** (this will repull all the images and redeploy all containers):
    ![Portainer Update Stack](/assets/images/climsim_maintenance/portainer-update-stack.png)

# Troubleshooting

## Volume failure
After a stack redeployment you can get a **Failure volume** error.

<div class="alert alert-warning">
    Only do this if you get the following error:<br><code>Failure volume "instance_storage" ...</code><br>
    Keep in mind this will remove the database, all the users of the app will loose their data. 
</div>

<div class="alert alert-success">
    Navigate to <b>Volumes</b>:<br>
    <img src="/assets/images/climsim_maintenance/portainer-volume.png" style="width: 400px;  margin: 10px 0px; align:center;"><br>
    Search through for <code>climsim_instance_storage</code> if it exists check it and delete it:<br>
    <img src="/assets/images/climsim_maintenance/portainer-instance-storage.png" style=" margin: 10px 0px;">    
</div>

## Nginx issue
After a stack redeployment you can get a **nginx error** when trying to reach the app.

It has been observed that containers can get into inconsistent states and need restarting. This has been observed for the `nginx` container (noteablly when containers are redeployed in different passes of watchtower, nginx builds quicker than other images and it may occur that not all images are redeployed at the same time) and the `panel` container (when a error occurs, the container remains useable for new websites but provides strange results).

<div class="alert alert-success">
    <b>Solve</b>: Restart <code>nginx</code> container first and then <code>Panel</code> container (see <a href="#managing-containers">Redeploy a container</a>)
</div>

## Containers are not redeployed automatically
It might happen that some containers are not redeployed when new images are pushed to the **osupytheas regisry** (registry.osupytheas.fr). If this occurs, **Watchtower** is probably the cause. Here is a non-exhaustive list of possible problems:

- Watchtower container is not running: It appears this container stop sometimes randomly.
  <div class="alert alert-success">
      <b>Solve</b>: <a href="#redeploying-the-stack">Redeploy the stack</a> 
  </div>
  Watchtower container will be redeployed and at the same time, all the other containers will be redeployed using the latest image.

- Watchtower is not looking at the right container names: It might happens that overtime, **Portainer**/**Docker** update, can alter the way the containers are named.
  <div class="alert alert-success">
      <b>Solve</b>: Access to the docker-compose (<b>step 3</b> of <a href="#redeploying-the-stack">Redeploying the Stack</a>). Check inside the <code>watchtower-climsim</code> section the <code>command</code> line: <img src="/assets/images/climsim_maintenance/portainer-docker-compose.png" style="margin: 10px 0px;"> 
      All the names provided here have to match the ones of the containers deployed in <b>Containers</b> section below:
       <img src="/assets/images/climsim_maintenance/portainer-climsim-watchtower-issue.png" style="margin: 10px 0px;">
  </div>
