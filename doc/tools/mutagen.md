# takelage-doc: tools/mutagen

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [mutagen](#mutagen)
- [Usage tau mutagen socket](#socket)
  - [tau mutagen socket check](#socketcheck)
  - [tau mutagen socket create](#socketcreate)
  - [tau mutagen socket list](#socketlist)
  - [tau mutagen socket terminate](#socketterminate)

<a name="introduction"/>

# Introduction

<a name="mutagen"/>

## mutagen Forwarding and Syncing Tool

[mutagen](https://mutagen.io/) solves three different problems
that occur when you use a command and control server like `takelage`.

### Socket Forwarding

It's fairly common for linux command line tools 
to use unix sockets for client server communication.
One of those commands is `docker`. 
It communicates of the socket file _/var/run/docker.sock_.
As we want a seamless experience it should not matter
whether you call `docker` on your host machine or
inside a `takelage` container.

But `docker` is a special case as `takelage` is itself
a `docker` container. It is easy to mount 
the docker socket file as a volume inside the 
container (see 
[`cmd_docker_container_create](https://github.com/geospin-takelage/takelage-cli/blob/main/lib/takelage/default.yml)).
Unfortunately, due to limits of the docker
implementation mounting socket files of other
programs does not yield satisfactory results.

`takelage` uses `mutagen` to solve this problems.
When you create a new takelage container with
`tau login` a couple of unix sockets 
will be forwarded to the container.
Currently, these are the `gnupg` agent sockets
_gpg_ and _gpg-ssh_ as well as the _mutagen_ daemon socket.

So `docker`, `mutagen`, `gpg` and `ssh` should behave
the same regardless if your use them inside or outside
of a `takelage` container.

### Port Forwarding

`mutagen` can forward arbitrary ports between
your host and docker containers.
Best of all, it does so while the containers are running.
So if you need a connection to some container in
one of your scripts or if you just want to have a look
on that port with your browser 
then remember that there is `mutagen`.

### Data Synchronization

[`docker volumes`](https://docs.docker.com/storage/volumes/)
are nicely integrated but their performance is abyssal.
It might be ok if you want to lint your source code 
but it might not be ok if you want to sync your source code 
to your locally running production container.
If your tests run 20x slower in your container than on
your host then try syncing them with `mutagen`.


<a name="socket"/>

# Usage tau mutagen socket

<a name="socketcheck"/>

### tau mutagen socket check

`tau mutagen socket check [SOCKET]`
will return `0` if a takelage socket
with that name exists and `1` otherwise.

<a name="socketcreate"/>

### tau mutagen socket create

`tau mutagen socket create [NAME] [IN] [OUT]`
creates a socket for the current container
with a name appended to the hash identifier
of the container. 
It first expects the path to the socket file in the container 
and then the path to the socket file on the host.

<a name="socketlist"/>

### tau mutagen socket list

`tau mutagen socket list` 
prints the list of active mutagen forward sessions 
for takelage sockets (which have the label
_type: takelage-socket_ tag).

<a name="socketterminate"/>

### tau mutagen socket terminate

`tau mutagen socket terminate `
prints the list of active mutagen forward sessions
for takelage sockets (which have the label
_type: takelage-socket_ tag).

