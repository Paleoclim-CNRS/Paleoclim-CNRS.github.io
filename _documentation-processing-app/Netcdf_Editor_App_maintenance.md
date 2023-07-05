---
title: Maintenance
author: Anthony
toc: true
toc_sticky: true
excerpt: General information for working and ensuring the application is deployed correctly
---

<style>
    .initial-content div {border-radius: 10px; margin-bottom: 15px;}
    pre {background-color:#fafafa; border-radius: 7px;}
    code {background-color:#fafafa; color:#002d46; font-size:medium; padding: 2px 0px; border-radius: 4px;}
    img {border-radius: 10px;}
    div pre {margin:0px;}
    li {margin-left:30px;}

    .general {background-color:#f9f9f9; padding: 15px; color:#002d46;}
    .error {background-color:#ff4816; color:#ffffff; padding: 20px; border-radius: 7px;}
    .light-info {background-color:#cfd9d9; color:#002d46; border-radius: 7px; padding: 20px;}
    .section {background-color:#4b78a1;padding: 15px;}
    .sub-section {background-color:#b9bdbd;padding: 3px 0px;}

    .alert-info {background-color:#7bb7db; color:#002d46; padding: 20px}
    .alert-warning {background-color:#f3c778; color:#002d46; padding: 20px}
    .alert-success { padding: 20px}

    .alert-warning code, .alert-info code, .light-info code, .alert-success code, .error code {padding:0px 5px;}
    .alert-info a {color:#245b9c}
    .alert-info a:hover {color:#1c4576}
    .alert-info a:active {color:#472197}
    .light-info a {color:#0d4268}
    .light-info a:hover {color:#13629b}
    .light-info a:active {color:#472197}
    .alert-warning a {color:#815518}
    .alert-warning a:hover {color:#37240a}
    .alert-warning a:active {color:#472197}
</style>

The IPSL Boundary Condtion Editor is developped on [GitHub](https://github.com/Paleoclim-CNRS/netcdf_editor_app) and automatically deployed to [https://climate_sim.osupytheas.fr](https://climate_sim.osupytheas.fr).

# General Maintenance

## Get access to the containers
1. Navigate to [Portainer](https://portainer.osupytheas.fr)
1. Use your OSU login details.
1. Click on **dev**:
    ![Portainer home](/assets/images/climsim/portainer-home.png)
1. Click on **Stacks**:
    ![Portainer dev](/assets/images/climsim/portainer-dev.png)
1. At this step, you can either clic on the:
   - **netcdf** stack name which is the *Single Page*
   - **climsim** stack name which is the *Multi Page*
   <div class="alert alert-info">
    For the rest of the section we will use the <b>climsim</b> stack but it is identical for the <b>netcdf</b> stack except there will be only one container.
   </div>
   Click on **climsim**:
    ![Portainer stacks](/assets/images/climsim/portainer-stacks.png)
1. Stack details and list of containers will be displayed:
    ![Portainer Containers](/assets/images/climsim/portainer-climsim-stack.png)

1. For each container, you have access to several options under the **Quick Actions** column:
    ![Portainer Quick Action](/assets/images/climsim/portainer-quick-actions.png)
    1. <p><img src="/assets/images/climsim/portainer-quick-actions-log.png" style="height:40px;"> log of the container</p>
    1. <p><img src="/assets/images/climsim/portainer-quick-actions-infos.png" style="height:40px;"> informations about the container</p>
    1. <p><img src="/assets/images/climsim/portainer-quick-actions-ressources.png" style="height:40px;"> resource usage of the container</p>
    1. <p><img src="/assets/images/climsim/portainer-quick-actions-console.png" style="height:40px;"> console inside the container</p>
    
## Managing containers

You can perfom several actions over one or multiple containers.

For this, select them by ticking the box on the left side and then selecting the action you want to perform.
![Portainer Restart](/assets/images/climsim/portainer-restart.png)

<div class="alert alert-info">
What you might need the most will be the <img src="/assets/images/climsim/portainer-container-restart.png" style=""> action to redeploy a container in case it doesn't work properly.
</div>

## Redeploying the Stack

This can be necessary after a power cut for example or if you accidentally removed a container.

1. Once you are on the stack details and container list page (**step 6** of [Get access to the containers](#get-access-to-the-containers))
1. Click on **Editor**:
    ![Portainer Editor](/assets/images/climsim/portainer-editor.png)

1. The docker-compose will be displayed here (You might need to do some changes in it):
    ![Portainer Update Stack](/assets/images/climsim/portainer-climsim-editor.png)

1. Click **Update the stack** (this will repull all the images and redeploy all containers):
    ![Portainer Update Stack](/assets/images/climsim/portainer-update-stack.png)


## Database management
At some point you might need to reach the application database. The steps below describe how get access and somme useful commands.

Access to the console of `climsim_flask_app_1` container (**step 7** of [Get access to the containers](#get-access-to-the-containers)).
Once in the console use the commands
```
apt-get update
apt-get install sqlite3
```

Go to the instance folder:
```
cd /usr/src/app/instance
```

You can access the db by using the command:
```
sqlite3 netcdf_editor.sqlite
```

You can list the tables available in the db with:
```
.tables
```
To display all the content of table, use:
```
select * from [TABLE];
```
With `[TABLE]` the table name you want to look at.

Other commands can be found on the [official Sqlite website](https://www.sqlite.org/cli.html).

## Process queue management

Access to the console of `climsim_message_broker_1` container (**step 7** of [Get access to the containers](#get-access-to-the-containers)).
Once in the console use the following commands to list the available queues:
```
rabbitmqadmin list queues
```

To see the processes in a specific queue, use:
```
rabbitmqadmin get queue=[QUEUE_NAME] count=40 
```
`[QUEUE_NAME]` can be found using command to list queues available.

More information can be found on the [offcial rabbitmq website](https://www.rabbitmq.com/management-cli.html).

# Images hosting

<div class="alert light-info">
    You may need to read more about the <a href="[doc-#underTheHood]()">deployment workflow</a> as it is complementary to this section.
</div>

Each time new images are generated by Github actions, they are pushed to the **Osupytheas** registry and **DockerHub**.

## DockerHub

Images are pushed to **DockerHub** (https://hub.docker.com/) with only a tag `latest`.


GitHub action is able to push images to **DockerHub** through a **DockerHub** account which is a member of the **CEREGE-CL** organization.

(Thanks to this [line](https://github.com/Paleoclim-CNRS/netcdf_editor_app/blob/ed842e0c1e92e897b90d74bb30f84f226df800e7/.github/workflows/docker-image.yml#L28) for all the different elements (message_dispatcher, nginx, flask_app, ...)

In order to work this way, the DockerHub account credentials must be provided in the [GitHub Actions Secrets](https://github.com/Paleoclim-CNRS/netcdf_editor_app/settings/secrets/actions). The two concerned secrets are `DOCKER_USER` and `DOCKER_PASSWORD`.

If you want to modify them, clic on update secret button:
![Portainer Editor](/assets/images/climsim/update-secret.png)

<div class="alert alert-info">
    Instead of providing the account password, an access token in DockerHub can be used. It works like a password but you can have many tokens and revoke access to each one at any time. For this, connect to your DockerHub account and go into your <a href='/assets/images/climsim/Docker-token.png'>Account Settings</a> and into the <a href='/assets/images/climsim/Docker-token-2.png'>Security tab</a>.<br>
    From there, you can generate a new access token and provide it for the <code>DOCKER_PASSWORD</code> secret in GitHub.
</div>

<div class="alert alert-warning">
    Do not modify the <code>DOCKER_USER_OSU</code> and the <code>DOCKER_PASSWORD_OSU</code> secrets unless requested by the <b>OSUPYTHEAS</b>.
</div>

<div class="alert light-info">
    For now:<br>
    <code>DOCKER_USER</code> = <b>anthog</b><br>
    <code>DOCKER_PASSWORD</code> = <b>anthog Access token</b>
</div>

## registry.osupytheas.fr

<div class="alert alert-warning">
    You must be connected to internet via ethernet cable at CEREGE or at the **vpn.osupytheas.fr** in order to reach the registry.
</div>

Images are pushed to the **Osupytheas** registry (https://docker.osupytheas.fr/) with a tag `latest` and a tag with the *GitHub commit number*.

When you reach the registry page, you can look for the `netcdf_editor_...` images:

![[Visual explanation]](/assets/images/climsim/registry-osu-main.png)

Clic on one of the images to get more information:

![[Visual explanation]](/assets/images/climsim/registry-osu.png)

# Troubleshooting

## Volume failure
After a stack redeployment you can get a **Failure volume** error.

<div class="alert alert-warning">
    Only do this if you get the following error:<br><code>Failure volume "instance_storage" ...</code><br>
    Keep in mind this will remove the database, all the users of the app will loose their data. 
</div>

<div class="alert alert-success">
    <b>Solving</b>:<br>
    Navigate to <b>Volumes</b>:<br>
    <p align="center"><img src="/assets/images/climsim/portainer-volume.png" style="width: 500px;  margin-top: 10px;"></p><br>
    Search through for <code>climsim_instance_storage</code> if it exists check it and delete it:<br>
    <p align="center"><img src="/assets/images/climsim/portainer-instance-storage.png" style="width: 600px; margin-top: 10px;"></p>    
</div>

## Nginx issue
After a stack redeployment you can get a **nginx error** when trying to reach the app.

It has been observed that containers can get into inconsistent states and need restarting. This has been observed for the `nginx` container (noteablly when containers are redeployed in different passes of watchtower, nginx builds quicker than other images and it may occur that not all images are redeployed at the same time) and the `panel` container (when a error occurs, the container remains useable for new websites but provides strange results).

<div class="alert alert-success">
    <b>Solving</b>:<br>
    Restart <code>nginx</code> container first and then <code>Panel</code> container (see <a href="#managing-containers">Redeploy a container</a>)
</div>

## Containers are not redeployed automatically
It might happen that some containers are not redeployed when new images are pushed to the **osupytheas regisry** (registry.osupytheas.fr). If this occurs, **Watchtower** is probably the cause. Here is a non-exhaustive list of possible problems:

- Watchtower container is not running: It appears this container stop sometimes randomly.
  <div class="alert alert-success">
      <b>Solving</b>:<br>
      <a href="#redeploying-the-stack">Redeploy the stack</a> 
  </div>
  Watchtower container will be redeployed and at the same time, all the other containers will be redeployed using the latest image.

- Watchtower is not looking at the right container names: It might happens that overtime, **Portainer**/**Docker** update, can alter the way the containers are named.
  <div class="alert alert-success">
    <b>Solving</b>:<br>
    Access to the docker-compose (<b>step 3</b> of <a href="#redeploying-the-stack">Redeploying the Stack</a>). Check inside the <code>watchtower-climsim</code> section the <code>command</code> line:<br>
    <p align="center"><img src="/assets/images/climsim/portainer-docker-compose.png" style="width: 600px; margin-top: 10px;"></p>
    All the names provided here have to match the ones of the containers deployed in <b>Containers</b> section below:
    <p align="center"><img src="/assets/images/climsim/portainer-climsim-watchtower-issue.png" style="width: 600px; margin-top: 10px;"></p>
  </div>

## Regrid or Routing or MOSAIC or MOSAIX step is not validating upon save
It might happen one of this step status remains red even after saving it.

The web application page is being refreshed every 10 seconds. When saving a step, sometimes the main app page is reached just before the process starts or completes. In this case the indicator status will remain red for 10 secondes before turning blue or green. Just wait at least for 10 seconds to make sure it updates.

If it doesn't, check below.

<div class="alert alert-success">
    <b>Solving</b>:<br>
    If a file is not provided in the right format or for some other various reasons a process can be stuck in the <b>python</b> or <b>MOSAC</b>/<b>MOSAIX</b> queue. In this case, this specific process won't complete and all the next launched processes for all users will remain blocked and won't process either.
    <br>
    To solve this, access the console of the <code>climsim_message_broker_1</code> container and check if there is any process showing (see <a href="#process-queue-management">Process queue management</a>).<br>
    Processes should generally be treated quickly (few seconds) so if you see them in the queue after this amount of time, it is likely they are stuck. In this case, you can either:<br>
    <li>delete the first process pending (potentially blocking the other processes):<br>
    <code>rabbitmqadmin get queue=[QUEUE_NAME] ackmode=ack_requeue_false</code></li>
    <li>or delete all the processes by using:<br>
    <code>rabbitmqadmin delete queue name=[QUEUE_NAME]</code></li>
</div>


## The app isn't working anymore and you want to deploy an old but functionnal version instead

It might happen with time, the applications can stop working because of package version that could change and break up everything. In this case you may want to deploy an older version of the applications which are working, the time that you can fix the issue.

<div class="alert alert-success">
    <b>Solving</b>:<br>
    You will need first to identify the images version which are functionnal on the (<a href="#registryosupytheasfr">Osupytheas registry</a>). You can associate the version of an image with its tag which is the <b>GitHub commit number</b>. Generally you will want to take images with the same tag to avoid any issues.<br><br>
    Once you have the tag (GitHub commit number), connect to Portainer and reach the <b>docker-compose</b> (Step 3 of <a href="#redeploying-the-stack">Redeploying the Stack</a>) of the application you are interested in (either <b>climsim</b> = <em>Multi Page</em> or <b>netcdf</b> = <em>Single Page</em>).<br><br>
    Modify it by adding the <code>:$(tag)</code> at the end of the <code>images:</code> line:
    <img src="/assets/images/climsim/modify-image-tag.png"><br><br>
    Normally you will add it only for following images:
        <li>nginx</li>
        <li>message_dispatcher</li>
        <li>python_workerS (there are several of them)</li>
        <li>flask_app</li>
        <li>panel_app</li>
        <li>mosaic_worker</li>
        <li>mosaix_worker</li>
    <br>
    but not for:
    <li>message broker</li>
    <li>watchtower-climsim</li>
    <br>
    Then you can Redeploy the stack (step 4 of <a href="#redeploying-the-stack">Redeploying the Stack</a>)
</div>

<div class="alert alert-info">
    If you want Portainer to redeploy the latest images available, just remove the <code>:$(tag)</code> for the concerned <code>images:</code> lines in the <b>docker-compose</b>.
</div>
