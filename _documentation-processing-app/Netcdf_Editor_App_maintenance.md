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
    You may need to read more about the <a href="http://127.0.0.1:4000/documentation-processing-app/Netcdf_Editor_App_under_the_hood/#deployment-workflow-cicd">deployment workflow</a> as it is complementary to this section.
</div>

Each time new images are generated by Github actions, they are pushed to the **Osupytheas** registry and **DockerHub**.

On **GitHub**, the file responsible for the creation of the images and their deployment (to **DockerHub** and **Osupytheas registry**) is [docker-image.yml](https://github.com/Paleoclim-CNRS/netcdf_editor_app/blob/ed842e0c1e92e897b90d74bb30f84f226df800e7/.github/workflows/docker-image.yml).

## DockerHub

Images are pushed to **DockerHub** (https://hub.docker.com/) with only a tag `latest`.

**GitHub action** is able to push images to **DockerHub** thanks to a **DockerHub** account which is a member of the [CEREGE-CL](https://hub.docker.com/orgs/ceregecl/members) **DockerHub** organization (See the several command lines `docker login -u $DOCKER_USER -p $DOCKER_PASSWORD` in [docker-image.yml](https://github.com/Paleoclim-CNRS/netcdf_editor_app/blob/ed842e0c1e92e897b90d74bb30f84f226df800e7/.github/workflows/docker-image.yml)).

In order to work this way, the **DockerHub** account credentials must be provided in the [GitHub Actions Secrets](https://github.com/Paleoclim-CNRS/netcdf_editor_app/settings/secrets/actions). The two concerned secrets are `DOCKER_USER` and `DOCKER_PASSWORD`.

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
    You must be connected to internet via ethernet cable at CEREGE or at the <b>vpn.osupytheas.fr</b> in order to reach the registry.
</div>

Images are pushed to the **Osupytheas** registry (https://docker.osupytheas.fr/) with a tag `latest` and a tag with the *GitHub commit number*.

When you reach the registry page, you can look for the `netcdf_editor_...` images:

![[Visual explanation]](/assets/images/climsim/registry-osu-main.png)

Clic on one of the images to get more information:

![[Visual explanation]](/assets/images/climsim/registry-osu.png)
