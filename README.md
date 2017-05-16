# intro-to-docker

This is an introductory workshop to Docker. It is meant to be suited to everyone: from beginners to intermediate users.


## Motivation

Let's say we want to deploy our own instance of [Rocket.chat](https://rocket.chat/) ([demo](https://demo.rocket.chat/home)).

We can do it by running these two commands:

```
$ sudo docker run \
  --name=mongo \
  --net=host \
  -v /etc/localtime:/etc/localtime:ro \
  mongo:3.4

$ sudo docker run \
  --name=rocket \
  --net=host \
  -e MONGO_URL=mongodb://0.0.0.0:27017 \
  -v /etc/localtime:/etc/localtime:ro \
  rocket.chat
```

Rocket.chat is now running inside docker containers and we can check it out:

```
$ xdg-open http://localhost:3000
```

Let's see how would [the manual installation](https://rocket.chat/docs/installation/manual-installation/centos) look like.


## Setup

CentOS users are advised to use `yum` package manager, Fedora users should run `dnf`.

So let's install docker:

```
$ sudo yum install docker
```

And let's start it:

```
$ sudo systemctl start docker
```

Is it running?

```
$ systemctl status docker
```

Is it really running?

```
$ sudo docker version
```

Why `sudo`?

```
$ ls -lha /run/docker.sock

$ sudo docker run -ti -v /:/hostfs registry.fedoraproject.org/fedora:26 bash
```


## Let's explore

We can run bash inside a container:

```
$ sudo docker exec -ti rocket bash

$ cat /etc/os-release
$ cat /etc/issue
$ ps aux
```

Let's go back to host.

How does the container look?

```
$ htop
```

What are the technologies containers are using?

```
$ sudo docker run -ti --rm registry.fedoraproject.org/fedora:26 bash

$ dnf install -y procps-ng iproute

$ ls -lha /proc/self/ns/
$ mount
$ ps aux
$ hostname
$ ip a
```


## Tell me more about docker

Docker is a platform to manage complete lifecycle of applications including
building, packaging, running, deploying, scaling. Docker uses linux containers
for running applications and its own format to distribute the applications.

Docker tool is using client & server architecture:

![Docker architecture](http://nordicapis.com/wp-content/uploads/Docker-API-infographic-container-devops-nordic-apis.png)

Source: http://nordicapis.com/api-driven-devops-spotlight-on-docker/


### Images

Container images are a method to distribute applications. They are being built
using docker, then uploaded to docker registries so they can be distrbuted.

Here's [the rocket.chat image](https://hub.docker.com/r/_/rocket.chat/) inside
Docker Hub, the registry provided by Docker Inc.

Images are composed of metadata and layered filesystem trees. These filesystem
trees contain your service or application plus all its dependencies.

Huh, what?

Okay, let's take it step by step.

First we need to download the image from the registry:

```
$ sudo docker pull fedora:25
```

or

```
$ sudo docker pull registry.fedoraproject.org/fedora:26
```

Let's inspect the filesystem tree

```
$ sudo docker create --name image-content fedora:25 false && \
  mkdir -p ./image && \
  sudo docker export image-content | tar -x -C ./image

$ ls -lha ./image
```

But you said layered!

![Layers](https://docs.docker.com/engine/userguide/storagedriver/images/container-layers.jpg)

Source: https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/

```
$ mkdir -p layered-image && \
  sudo docker save rocket.chat | tar -x -C ./layered-image
$ mc ./layered-image
```

I hope it's clear that images are not containers.


### Containers

Containers are made of a writable layer on top of an image. Once you start a
container, it's also a traditional linux process.

Let's create a container:

```
$ sudo docker create -ti --name=toy fedora:25 bash
```

Is it running?

```
$ sudo docker inspect toy
```

```
$ sudo docker start toy
```

```
$ sudo docker attach toy
```

Let's change something!

```
$ echo "Just testing." >/fun
```

What about the writable layer thingy?

```
$ sudo docker diff toy
```


## Next steps

 * [Managing containers using ansible](https://github.com/pschiffe/ansible-docker)
 * [Advanced container deep-dive](https://tomastomecek.github.io/devconf-container-roadshow-2017/#/)
