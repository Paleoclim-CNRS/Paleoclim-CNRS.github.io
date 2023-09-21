---
title: Troubleshooting
author: Anthony
toc: true
toc_sticky: true
excerpt: Listing of potential issues which could be encountered.
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


This is not a comprehensive list of possible issues which can be encountered and even less so for the possible solves. It is only based on what I have experienced.

<div class="alert alert-info">
The following issues are splitted into two sections indicating the level of permission required to solve it:
<li>Admin (you will need admin permission to access the <b>Portainer</b> interface)</li>
<li>User (you just need to have a user account on the application)</li>
</div>

If you are a user of the application and face an issue you cannot sort out either because it is not listed below or because you don't have admin permission, please contact Yannick Donnadieu (donnadieu@cerege.fr), Sebastien Nguyen (sebastien.nguyen@lsce.ipsl.fr) or report an issue on [GitHub](https://github.com/Paleoclim-CNRS/netcdf_editor_app/issues/new/choose).


# User

## Modifications done in Panel steps are not validating upon save

When save button for one of these steps (**internal oceans** or **diffusive passages**) user is redirected to main application page but bathymetry/topography modifications are not saved. For some (unknown) reasons this issue is encountered with the **Firefox browser**.

<div class="alert alert-success">
    <b>Solving</b>:<br>
    Switch to <b>Google Chrome</b> or <b>Safari</b>.
</div>


# Admin

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

## Nginx error
After a stack redeployment you can get a **nginx error** when trying to reach the app.

It has been observed that containers can get into inconsistent states and need restarting. This has been observed for the `nginx` container (noteablly when containers are redeployed in different passes of watchtower, nginx builds quicker than other images and it may occur that not all images are redeployed at the same time) and the `panel` container (when a error occurs, the container remains useable for new websites but provides strange results).

<div class="alert alert-success">
    <b>Solving</b>:<br>
    Restart <code>nginx</code> container first and then <code>Panel</code> container (see <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#managing-containers">Managing containers</a>)
</div>

## Containers are not redeployed automatically
It might happen that some containers are not redeployed when new images are pushed to the **osupytheas regisry** (registry.osupytheas.fr). If this occurs, **Watchtower** is probably the cause. Here is a non-exhaustive list of possible problems:

- Watchtower container is not running: It appears this container stop sometimes randomly.
  <div class="alert alert-success">
      <b>Solving</b>:<br>
      <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#redeploying-the-stack">Redeploying the stack</a> 
  </div>
  Watchtower container will be redeployed and at the same time, all the other containers will be redeployed using the latest image.

- Watchtower is not looking at the right container names: It might happens that overtime, **Portainer**/**Docker** update can alter the way the containers are named.
  <div class="alert alert-success">
    <b>Solving</b>:<br>
    Access to the docker-compose (<b>step 3</b> of <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#redeploying-the-stack">Redeploying the Stack</a>). Check inside the <code>watchtower-climsim</code> section the <code>command</code> line:<br>
    <p align="center"><img src="/assets/images/climsim/portainer-docker-compose.png" style="width: 600px; margin-top: 10px;"></p>
    All the names provided here have to match the ones of the containers deployed in <b>Containers</b> section (<b>step 6</b> of <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#get-access-to-the-containers">Get access to the containers</a>) below:
    <p align="center"><img src="/assets/images/climsim/portainer-climsim-watchtower-issue.png" style="width: 600px; margin-top: 10px;"></p>
    If it is not the case, modify the <code>command</code> line for <code>watchtower-climsim</code> section in the docker-compose consequently.
  </div>

## Regrid or Routing or MOSAIC or MOSAIX step is not validating upon save
It might happen one of this step status remains red even after saving it.

The web application page is being refreshed every 10 seconds. When saving a step, sometimes the main app page is reached just before the process starts or completes. In this case the indicator status will remain red for 10 secondes before turning blue or green. Just wait at least for 10 seconds to make sure it updates.

If it doesn't, check below.

<div class="alert alert-success">
    <b>Solving</b>:<br>
    If a file is not provided in the right format or for some other various reasons a process can be stuck in the <b>python</b> or <b>MOSAC</b>/<b>MOSAIX</b> queue. In this case, this specific process won't complete and all the next launched processes for all users will remain blocked and won't process either.
    <br>
    To solve this, access the console of the <code>climsim_message_broker_1</code> container and check if there is any process showing (see <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#process-queue-management">Process queue management</a>).<br>
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
    You will need first to identify the images version which are functionnal on the (<a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#registryosupytheasfr">Osupytheas registry</a>). You can associate the version of an image with its tag which is the <b>GitHub commit number</b>. Generally you will want to take images with the same tag to avoid any issues.<br><br>
    Once you have the tag (GitHub commit number), connect to Portainer and reach the <b>docker-compose</b> (Step 3 of <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#redeploying-the-stack">Redeploying the Stack</a>) of the application you are interested in (either <b>climsim</b> = <em>Multi Page</em> or <b>netcdf</b> = <em>Single Page</em>).<br><br>
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
    Then you can Redeploy the stack (step 4 of <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_App_maintenance/#redeploying-the-stack">Redeploying the Stack</a>)
</div>

<div class="alert alert-info">
    If you want Portainer to redeploy the latest images available, just remove the <code>:$(tag)</code> for the concerned <code>images:</code> lines in the <b>docker-compose</b>.
</div>
