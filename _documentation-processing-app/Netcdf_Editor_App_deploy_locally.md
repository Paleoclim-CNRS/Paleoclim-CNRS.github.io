---
title: Deploy locally
author: Anthony
toc: true
toc_sticky: true
excerpt: All the steps to install and be able to use the application locally
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

Deploying the code locally from your computer can be useful to test some features and making sure all the bugs are fixed before pushing your changes to the Github repository.

# Preliminary steps

- Create a GitHub account 
- Install [Docker Desktop](https://docs.docker.com/desktop/)

- Once your done with the steps above, you will need to clone locally the GitHub repository ([documentation](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository)). 
   ```
   git clone https://github.com/Paleoclim-CNRS/netcdf_editor_app.git
   ```
   or
   ```
   git clone git@github.com:Paleoclim-CNRS/netcdf_editor_app.git
   ```

<div class="alert light-info">
  <b>Wondering which in you should choose ?</b><br>
  Use <b>SSH</b> as a more secure option and <b>HTTPS</b> for basic password-based Git usage.
  You can find more information <a href="https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories">here</a>.
</div>
   

   If you plan to go for ssh, copy your ssh key to github first (follow this [documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)).

   At this step, you should have a copy of the Netcdf Editor Application on you local computer.

- Move in the repository `cd netcdf-editor-app`

- Make sure your repository is ready

  Fetch latest changes:
  ```
  git fetch
  ```
  If you have done any modification in the repository, you can undo it by using (restore the state of the local repository to match the state of the remote branch):
  ```
  git reset --hard @{u}
  ```

# Single Page Web App

- Change into the Single page web app directory: 
  ```
  cd Single_Page_WebApp
  ```
- Build the image:
  ```
  docker build -f single_page_webapp.Dockerfile -t netcdf_editor_single_page . --no-cache
  ```
  The `--no-cache` means everything gets rebuilt
- Start the container:
  ```
  docker run -d -p 8080:8080 netcdf_editor_single_page 
  ```
  - `-d` means detached mode so once the containers all start the command line is returned to you
  - `-p` is for port selection
    <div class="alert light-info">
    The first <b>8080</b> is the port on the local machine you can change this to whatever you want.<br>
    The second <b>8080</b> is the entry port for the application. If you change it, modify the dockerfile <code>Single_Page_WebApp/single_page_webapp.Dockerfile</code> under <code>ENTRYPOINT</code> option <code>--port</code> as well.
- Connect to the container via `http://localhost:8080` (the **8080** refering to the first **8080** in command above)

# Multi Page Web App

- Make sure nothing is running:
  ```
  docker-compose -f docker-compose.yaml --env-file ./config/.env.prod down
  ```
- Delete the docker volume (If there has been a database upgrade)
  - List volumes:
    ```
    docker volume ls 
    ```
  - Remove associated volume:
    ```
    docker volume rm netcdf_editor_app_instance_storage
    ```
    or remove all volumes of stopped containers:
    ```
    docker volume prune
    ```

You can deploy the application in 2 different ways:

- **PRODUCTION** mode
  <div class='alert light-info'>
    Designed for development. It uses images sent to DockerHub to deploy the containers. Modifications of you local code won't be visible on the deployed application.
  </div>
  - Run
    ```
    docker-compose -f docker-compose.yaml --env-file ./config/.env.prod up --build -d --scale python_worker=2
    ```
  - Connect to the application via `http://localhost:43829` (the port is defined in `config/.env.prod` under `NGINX_PORT`)

- **DEVELOPMENT** mode 
  <div class='alert light-info'>
    Designed for development. Creates images from local dockerfiles and deploys containers from them - allows to see changes made in the code straight away without having to relaunch containers.<br>If you want to know more about this, check <a href="#use-development-containers">here</a>.
  </div>
  - If this is the first time running the database then you will need to initialize it with the following commands
    - Get command line access to the flask container:
      ```
      docker exec -it netcdf_editor_app-flask_app-1 /bin/bash
      ```
      initialize database:
      ```
      python -m flask init-db
      ``` 
    - or use the one liner command:
      ```
      docker exec -it netcdf_editor_app_flask_app_1 python -m flask init-db
      ```
    
    If you don't do this you might get a `sqlite3.OperationalError` error when running the application
  - Run:
    ```
    docker-compose -f docker-compose.dev.yaml --env-file ./config/.env.dev up --build -d --scale python_worker=2
    ```
  - Connect to the application via `http://localhost:5000` (the port is defined in `config/.env.dev` under `NGINX_PORT`)
