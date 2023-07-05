---
title: Under the Hood
author: Anthony
toc: true
toc_sticky: true
excerpt: Application functionning under the hood
---

<style>
    .initial-content div {border-radius: 10px; margin-bottom: 15px;}
    pre {background-color:#fafafa; border-radius: 7px;}
    code {background-color:#fafafa; color:#002d46; font-size:medium; padding: 2px 0px; border-radius: 4px;}
    img {border-radius: 10px;}
    div pre {margin:0px;}

    .general {background-color:#f9f9f9; padding: 15px; color:#002d46;}
    .error {background-color:#ff4816; color:#ffffff; padding: 20px; border-radius: 7px;}
    .light-info {background-color:#cfd9d9; color:#002d46; border-radius: 7px; padding: 20px;}
    .section {background-color:#4b78a1;padding: 15px;}
    .sub-section {background-color:#b9bdbd;padding: 3px 0px;}

    .alert-info {background-color:#a4d7ec; color:#002d46; padding: 20px}
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

# Netcdf Editor Application architecture

When talking about the Netcdf Editor Application, it is composed of 2 underlying applications :

- **Single Page Web App** - for editing netcdf files
- **Multi Page Web App** - for preparing boundary conditions for CM5A2 Earth System Model used for deep time climate simulations.

## Deployment workflow (CICD)

CICD stands for Continuous Integration and Continuous Deployment

Here is a scheme representing the workflow:

<div class="alert alert-info">
I retranscribed it as accurately as I could though I'm not an expert of those technologies. This scheme can give at least a grasp of how the platforms and elements are chained together.
</div>

![Deployment Arch](/assets/images/climsim/climsim-deployment-arch.png)
[[Full screen]](/assets/images/climsim/climsim-deployment-arch.png)


The automated workflow is the following:

1. When there is a change on the `main` branch (either by direct **commit** or a **pull request**) [GitHub actions](https://github.com/Paleoclim-CNRS/netcdf_editor_app/tree/main/.github/workflows) are triggered:
    - Python tests: ensure the code works
    - Build Docker Images
1. The GitHub Action for building the Docker images, builds the images and then pushes the images (updates the images) to:
    - [DockerHub](https://hub.docker.com/u/ceregecl) with the tag `latest`. This means that anyone can easily install the application (but there is a limit on the number of image pulls on dockerhub free 200?).
      <div class="alert light-info">
        If you want more information about the dockerHub images management, check <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_Application_Maintenance/#dockerhub">DockerHub section</a> in maintenance documentation.
      </div>
    - [OSU Infrastructure](https://docker.osupytheas.fr) at registry.osupytheas.fr (you need an osu account to access this) with the tag `latest` and a tag with the *GitHub commit number* (previous versions are stored here).
    This is the preferred place to get the images as there is no limiting and is on local infrastructure.
      <div class="alert light-info">
        If you want more information about the registry images management, check <a href="https://paleoclim-cnrs.github.io/documentation-processing-app/Netcdf_Editor_Application_Maintenance/#registryosupytheasfr">registry.osupytheas.fr</a> in maintenance documentation..
      </div>
1. On the OSU infrastructure (referent: Julien Lecubin) where the app is deployed to, [WatchTower](https://containrrr.dev/watchtower/) is used. This tool monitors (certain) images every 5 minutes and looks to see if a new version is available. If it is the case it will automatically download it, stop the previous container using the given image and redeploy a new container using the new image with the same configuration as the previous container.

## Single Page Web App

The **Single Page** is composed of a [Panel](http://panel.holoviz.org/) framework which is a dashboarding technology for python. It allows to interact with netcdf files with ease without caring of the code necessary to load, vizualize and modify the data. This application has been reused for the **Multi Page** (check [here](#panel) for more details).

## Multi Page Web App

### Database

This is not a service (container by itself) but shared (via a volume) between the different containers. The Data base currently holds 4 tables:
- User: Stores username + password
- data_files this is the base table that is used for url endpoints on each upload a new id is given. It stores the uploded filename as well as some basic metadata info mainly the longitude and latitutde coordinates.
- revisions: Holds the different revision data and file types that have been seen. Each time a processing step is finished a new file is saved. The file has a file type (these can be seen in assets) and a revision number. We store filepath (all files are saved in the `instance` folder) rather than the netcdf file in a binary format -> this means we can accidentally delete files and become out of sync.
- steps: used to store the different processing steps and the options that were used to run the task.

### Flask

<p align="center">
    <img src="/assets/images/climsim/flask.webp" style="width:300px;"/>
</p>

[Flask](https://flask.palletsprojects.com/en/1.1.x/) is a python library that is used to create multipage web apps. It handles the routing (different endpoints) and requests. Being written in python it can execute python code before rendering HTML templates written in `jinja` template language. 

The main goal of this container is to display the different webpages, do simple computations used for visualisations and handle requests.

### Panel

<p align="center">
    <img src='/assets/images/climsim/Panel_2.png' style="width:300px;">
</p>

[Panel](http://panel.holoviz.org/) is the tool that is used to create the **Single Page**. It is useful for creating rich in page interactivity and processing easily.

The **Single Page** has been slightly refactored and then reused notably for:
- Internal Oceans
- Passage Problems
- Sub basins
- Complex Viewer (The complex viewer is a port of the **Single Page** and can be used to quickly and easily modify any file)

The source code for these fields are in `Multi_Page_WebApp/python_tools/climpy/interactive`

<div class='alert alert-info'>
    Pro Tip: If you want to modify a file simply upload as usual (the workflow doesn't expect anything in particular for Raw files) and then you can edit it.
    <img src="/assets/images/climsim/complex_viewer_button.png"/>
    After saving, the revision count will grow by one and you can download the file.
</div>

### Nginx

Nginx is a reverse proxy this is used to dispatch requests between flask and panel containers.

This is also the only entry point (open endpoint) to the app as the containers "collaborate" on an internal network. 

We have also:
- Boosted the timeout to 1d → This is to support the case of blocking a webpage during a long process
- Set upload size to 200Mb (this can be made bigger if needed)

### RabbitMQ

[Rabbit](https://www.rabbitmq.com) is a messaging broker, we can setup multiple queue which store "tasks" from the UI (flask app or other) then interprete these. 

Being the message broker this is where all the messages are stored and where the consumers connect to.

<div class='alert alert-info'>
    For more information see <a href='#messaging'> Messaging</a>.
</div>

### Message Dispatcher

In messaging terms this is a consumer. To decouple the UI from the implementation we use this service. For example the interface may say something like `regrid this` the message dispatcher interprets this message then dispatches it to the correct queue, this also means we can change backend calculations seamlessly from the frontend, in this example regridding is carried out in python so the dispatcher will send the appropriate message to the python task queue. __This is very important because it allows executing code in different environments, Python, Fortran ....__

Another key feature of the message dispatcher is that it receives `*.done` messages, effectively the processing jobs saying I have finished processing this task. With these messages it looks in `constants.py` to see if this step invalidates other steps and sends the message to the appropriate queue. This is very important because it means __we can create processing pipeline and dynamically update files when previous steps are carried out!__

<div class='alert alert-info'>
    For more information see <a href='#messaging'> Messaging</a>.
</div>

### Python Worker

The python worker holds a collection of algorithms written in Python. The algorithms can be found in `Multi_Page_WebApp/netcd_editor_app/utils` and have been decoupled from the interface meaning they can be executed separetly (outside of the App).

Routines include:
- geothermal heatflow calculations according to Fred Fluteau script
- PFT file creation
- Routing file calculations following a modification of Jean-Baptise Ladant's code
- ahmcoeff (used to augment ocean flow)

We can easily scale workers with `--scale python_worker=X` to create multiple instances and carry out compute in parallel.

### Messaging

It was decided to use a messaging queue to facilitate (processing) communications between containers.

The basic workflow is:
1. UI sends a message to carry out a task.
1. Message Dispatcher receives the message and sends it to the correct task queue currently:
    - Python
    - Fortran
    - Panel
1. A worker picks up the task and carrys out the process
1. When done the worker sends a message to the dispactcher saying it has finished
1. The dispatcher looks to see if any other steps depend on this step and send a message -> Go to step 2
1. Processing stops when all steps have finished and the dispatcher does not send out any new messages

Advantages of this implementations are:
- Pipeline calculations (see above)
- Steps can be run in parallel
- Steps can be run in different enviroments -> a consumer can be written in any language we simply need to tell the dispatcher that these messages should be sent to a given queue

# GitHub side

## GitHub actions
GitHub actions are automated workflows which are defined by files located [here](https://github.com/Paleoclim-CNRS/netcdf_editor_app/tree/main/.github/workflows) on GitHub website.
- `docker-images.yml` is the workflow which produces the images ans push them to the **Osupytheas registry** and **DockerHub**.
- `python-app-flask-test.yml` are python tests to make sure implementations will not break up the application.

# Docker side

The Docker technology is used for the deployment of the application. It is a platform allowing to create and manage containers which are self-contained elements gathering the source code as well as the necessary libraries. Thanks to the standardized format of the containers, it is very convenient to launch and manage the application.

- The single app is deployed in one Panel container.

- As the Multi app is more complicated, it is deployed through severel containers, one for each main element of the app:
  - Message_broker
  - Message_dispacher
  - Ngnix
  - Panel
  - Flask
  - Panel
  - Python_worker
  - MOSAIC
  - MOSAIX

As shown in the scheme [here](/assets/images/climsim/climsim-deployment-arch.png) a dockerFile is used to create docker images of the application. Theses images are then used to build the containers.

If you want more informations on how it works, check the [official docker documentation](https://docs.docker.com/).

## Useful commands

- Relaunching containers from scratch (stop and remove ALL containers, delete them, remove images and relaunch the application in dev mode)

  Delete all containers and images:
  ```
  docker kill $(docker ps -q) && docker rm $(docker ps -qa) && docker rmi $(docker images -q)
  ```

## Use development containers
Direct feedback of your code modification on the locally deployed application can useful when you want to add features or fix bugs. This way you only need to refresh the internet page to see code changes instead of having to delete and redeploy the container.

In the application, this is made possible by the specific usage of the <code>volume</code> option for the different containers in the <code>docker-compose.dev.yaml</code>. Example for the <b>Flask</b> container:
<pre><code>volumes:
  - ./Multi_Page_WebApp:/usr/src/app</code></pre>
Here, the container won't be a "self-contained-closed-box", your host directory on your local machine (<code>./Multi_Page_WebApp</code>) will be mounted as a volume for the container (at this location <code>/usr/src/app</code>) meaning the code inside the container reflects any changes that are made on your local machine.

# Python side

Important paths and files :

- Main Python script:
  ```
  /.../netcdf_editor_app/Multi_Page_WebApp/flask_app/climate_simulation_platform/app.py
  ```

- database operations (save, edit nc file, etc...):
  ```
  /.../netcdf_editor_app/Multi_Page_WebApp/flask_app/climate_simulation_platform/db.py
  ```

- Python code related to processings (coefs computation, routing):
  ```
  /.../netcdf_editor_app/Multi_Page_WebApp/python_tools/climpy/bc/ipsl
  ```

- Python code related to data visualization (Panel):
  ```
  /.../netcdf_editor_app/Multi_Page_WebApp/python_tools/climpy/interactive
  ```


# Data location 

There are two important locations for data:
- On **Osupytheas server** → `/mnt/srvstk1d/docker/data/climatesim` is the *Docker volume* on which are stored data
- On **Flask container** → `/usr/src/app/instance` is the mounting point of the *Docker volume*

>Therefore accessing to `/usr/src/app/instance` on **Flask container** allows to see what is inside `/mnt/srvstk1d/docker/data/climatesim` on **Osupytheas server**

After the loss of database Julien dedicated a new space on **Osupytheas server** for data to be stored: `/mnt/srvstk1d/docker/data/climatesim` 
Wesley updated the Docker-compose of Portainer by (end of the file):
```
volumes:
  instance_storage:
    driver: local
    driver_opts:
      o: bind
      type: none
      device: /mnt/srvstk1d/docker/data/climatesim
```
