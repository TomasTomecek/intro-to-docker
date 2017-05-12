# intro-to-docker

This is an introductory workshop to Docker aimed to be taught at universities.


## Motivation

Let's say we want to deploy our own instance of [Rocket.chat](https://rocket.chat/) ([demo](https://demo.rocket.chat/home)).

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

```
$ xdg-open http://localhost:3000
```

Let's see how [the manual installation](https://rocket.chat/docs/installation/manual-installation/centos ) looks like.


## Setup

CentOS users are advised to use `yum` package manager, Fedora users should run `dnf`.

So let's install docker

```
$ yum install docker
```

And let's start it:

```
$ systemctl start docker
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
$ docker exec -ti rocket bash
```

How does the container look?

```
$ htop
```

What are the technologies containers are using?

```
$ docker run -ti --name=toy registry.fedoraproject.org/fedora:26 bash

$ dnf install -y procps-ng iproute

$ ls -lha /proc/self/ns/
$ mount
$ ps aux
$ hostname
$ ip a
```


## Tell me more about docker


### Images

Images are composed of metadata and layered filesystem trees. These filesystem
trees contain your service or application plus all its dependencies.

Huh, what?

Okay, let's take it step by step.

First we need to download the image from remote registry (by default it's hub.docker.com):

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

```
$ mkdir -p layered-image && \
  sudo docker save rocket.chat | tar -x -C ./layered-image
$ mc ./layered-image
```

I hope it's clear that images are not containers.


### Containers

Containers are made of a writable layer on top of an image. Once you start a container, it's also a traditional linux process.

TBD


## Next steps

 * [Managing containers using ansible](https://github.com/pschiffe/ansible-docker)
