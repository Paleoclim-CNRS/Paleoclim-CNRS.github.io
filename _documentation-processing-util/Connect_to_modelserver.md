---
author: Anthony
title: How to access to Modelserver
excerpt: Set up your access to Modelserver via proxyjump with Rubicon
toc: true
---

# Overview

To have access to *modelserver* you need to connect via ssh with port forwarding (using *ssh.osupytheas* as gateway server).

**Requirements**<br><br>You need an **OSU Pytheas account** in order to be able to connect to *ssh.osupytheas* and *modelserver*.
{: .notice--info}


# Set up the SSH config file
*If you want more informations about what is done in this step check out this [doc](https://paleoclim-cnrs.github.io/documentation-website/ssh/#ssh-config).*

- If the `~/.ssh/config` doesn't exist you have to create one (`vi ~/.ssh/config`, `nano ~/.ssh/config`, ...) and add the [default options](https://paleoclim-cnrs.github.io/documentation-website/ssh/#default-options) on top of it.

- Then append to the `~/.ssh/config` file (make sure to replace `[OSUPYTHEAS_USERNAME]` by your osupytheas username):
  ```
  Host osupytheas
    Hostname ssh.osupytheas.fr
    User [OSUPYTHEAS_USERNAME]

  Host modelserver
    Hostname modelserver.cerege.fr
    User [OSUPYTHEAS_USERNAME]
    ForwardX11 yes
    ProxyJump osupytheas
    LocalForward 8111 127.0.0.1:8080
    LocalForward 8222 127.0.0.1:8888
    LocalForward 9999 127.0.0.1:8000
    LocalForward 9998 127.0.0.1:43829
    LocalForward 8788 127.0.0.1:8787
    LocalForward 8090 127.0.0.1:8090
  ```

- Now you should be able to connect to *modelserver* via the command line:
  ```
  ssh modelserver
  ```

  When you will connect for the first time, you will get something like:
  ```
  The authenticity of host 'rubicon.cerege.fr (193.49.98.9)' can't be established.
  RSA key fingerprint is XXXXXXXXXXXXXXXXXX.
  Are you sure you want to continue connecting (yes/no/[fingerprint])?
  ```
  - Confirm you want to continue to connect by entering `yes`.

  - Then you might be prompted to enter your `[OSUPYTHEAS_USERNAME]@ssh.osupytheas.fr`'s password.

  - Follow the two last steps a second time for your authentication to *modelserver* and you should land on the server properly.

# Copy your SSH key to the servers (not mandatory)

*If you want more informations about what is done in this step check out this [doc](https://paleoclim-cnrs.github.io/documentation-website/ssh/#copy-public-key-to-server).*

In order to avoid to enter your password each time you connect, you can copy your SSH key to the server.

**Requirements**<br><br>Make sure you already have generated a **ssh key pair** located in `~/.ssh/`. If not check this [GitHub Documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) to create one.
{: .notice--info}

* Copy your local computer's SSH key to *modelserver*'s `authorized_keys` file:
  ```
  ssh-copy-id -o ProxyJump=[OSUPYTHEAS_USERNAME]@ssh.osupytheas.fr [OSUPYTHEAS_USERNAME]@modelserver.cerege.fr
  ```
  Your *modelserver* password should be requested once again to authenticate and you will be good to go.
