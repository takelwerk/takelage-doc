# takelage-doc: docker

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [docker](#docker)
- [Usage tau docker container](#container)
  - [tau login](#login)

<a name="introduction"/>

# Introduction

<a name="docker"/>

## docker Container Virtualizer

takelage's main target platform is docker.
The takelage development environment itself is a docker image.
This guide focuses on the takelage docker image
and the takelage docker containers created with `tau`.

`tau` is the 
[takelage cli](https://github.com/geospin-takelage/takelage-cli)
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
specific for the current working directory of the host
if it does not exist yet.
If a takelage docker container for the current working directory
already exists `tau login` will log in to that container.

```bash
tau login
```
