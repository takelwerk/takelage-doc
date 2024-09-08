# takelage-doc: tau docker

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
  - [docker](#docker)
- [Usage tau docker container](#container)
  - [tau login](#login)
  - [tau list](#list)
  - [tau prune](#prune)
  - [tau clean](#clean)
  - [tau command](#command)
  - [tau daemon](#daemon)
- [Usage tau docker image](#image)
  - [tau update](#update)

<a name="introduction"/>

# Introduction

<a name="docker"/>

## docker Container Virtualizer

takelage's main target platform is docker.
The takelage development environment itself is a docker image.
This guide focuses on the takelage docker image
and the takelage docker containers created with `tau`.

`tau` is the 
[takelage cli](https://github.com/takelwerk/takelage-cli)
which is distributed as a 
[ruby gem](https://rubygems.org/gems/takelage).
The command line interface should be installed on the
host running the takelage development environment
and is also part of the takelage docker image.

<a name="container"/>

# Usage tau docker container

<a name="login"/>

## tau login: create and log in to a takelage container

`tau login` which is an alias for `tau docker container login`
creates a docker container from the takelage docker image
specific to the current working directory on the host
if no such container exists yet.
If a takelage docker container for the current working directory
already exists then `tau login` will log in to that container.

```bash
tau login
```

`tau login` will do a lot of background work and from time
to time there might be some problems. In these cases run
`tau` in debug mode so that you see what's going on:

```bash
tau login -l debug
```

`tau login` will create a docker network for each
takelage container it creates.

You cannot log in to a takelage container
from within a takelage container (“matryoshka test”).

<a name="list"/>

## tau list: list takelage containers

`tau list` which is an alias for `tau docker container list`
will output an ansible style YAML inventory of all running
takelage containers.
The containers will be listed 
either as active (i.e. there is an active login shell)
or as orphaned (i.e. there is no active login shell).
The orphaned containers can be removed with `tau prune`.

```bash
tau list
```

or even shorter:

```bash
tau ls
```

<a name="prune"/>

## tau prune: delete orphaned takelage containers

`tau prune` which is an alias for `tau docker container prune`
will remove those takelage containers 
(and the corresponding docker networks and mutagen forwards)
which have no active shell.

```bash
tau prune
```

This command can be run from within a takelage container
as you have an active login shell in that container.

<a name="clean"/>

## tau clean: delete all takelage containers

`tau clean` which is an alias for `tau docker container clean`
will remove all takelage containers
(and the corresponding docker networks and mutagen forwards).

```bash
tau clean
```

This command can only be run on the host
but not from within a takelage container.

<a name="command"/>

## tau docker container command: run a command in a takelage container

`tau docker container command` will run a command in the
takelage container of the current working directory on the host.
If no such container exists it will create one.

```bash
tau docker container command 'ls -la /project'
```

<a name="daemon"/>

## tau docker container daemon: start a takelage container in the background

`tau docker container daemon` starts a takelage container for the
current working directory if it does not exist yet
without logging in to the container.

```bash
tau docker container daemon
```

<a name="image"/>

# Usage tau docker image

<a name="update"/>

## tau update: get the latest takelage docker image

`tau update` which is an alias for `tau docker image update`
will check for a new 
[takelage docker image](https://hub.docker.com/r/takelwerk/takelage/tags)
on dockerhub.com and download it.

```bash
tau update
```
