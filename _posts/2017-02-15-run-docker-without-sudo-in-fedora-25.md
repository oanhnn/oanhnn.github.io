---
title:    Run docker without sudo in Fedora 25
layout:   post
category: tutorial-tips
tags:     [skill, dev-ops]
feature:  /assets/img/crontab.png
---

I was setup new computer in my company, i installed Fedora 25 OS for it.
After i installed `docker` following [the Docker document][docs.docker.com],
I has a problem about run `docker` without `sudo` (reasons are explained
on the Docker document page linked here, I’m not going to repeat them).

<!--more-->

So I googled a bit more: [developer.fedoraproject.org][developer.fedoraproject.org]

According to developer.fedoraproject.org, you’ll have to run the following two commands
in order to get `docker` executed without `sudo`.
Basically you’ll add a docker -group and add yourself to it. Both two sites gave a solution,
the docs.docker.com -instructions did not actually work. :smile:

```
$ sudo groupadd docker && sudo gpasswd -a ${USER} docker && sudo systemctl restart docker
$ newgrp docker
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/

```
Now, `docker` was run without `sudo`.


[docs.docker.com]:             https://docs.docker.com/engine/installation/linux/fedora/
[developer.fedoraproject.org]: https://developer.fedoraproject.org/tools/docker/docker-installation.html
