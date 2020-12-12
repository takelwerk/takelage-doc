# takelage-doc: docker_workflow

[Table of contents](../../README.md)

## Takelage Docker Workflow

takelage helps developing, producing and testing docker images.

# develop

To develop a docker image
takelage will use molecule as local docker hypervisor.
Developing a docker image means writing the ansible scripts
to provision a docker base container 
and writing the pytest scripts to verifiy a running container.

A standard work pattern is this:

1. molecule creates a docker container from a docker base image.
1. molecule provisions the docker container by running ansible scripts.
1. molecule verifies the container by running pytest scripts.

# produce and test

To produce and test a docker image
takelage will first use packer and then molecule.
Producing a docker image means creating the production-ready docker image.
Testing a docker image means 
checking that a docker container based on that image works as expected.

A standard work pattern is this:

1. packer creates a docker image 
   by provisioning a docker base image with our ansible scripts.
1. molecule creates a docker container from that docker base image.
1. molecule verifies the docker container by running our pytest scripts.

# Examples

As the takelage docker workflow is based on
ansible, pytest, molecule and packer which
are very flexible tools the workflow itself
becomes incredibly flexible, too.
Here are two examples which are quite different:

The above outlined work patterns are used iteratively.
We start with a base image, for example `debian/stable-slim`.
Then we use the above outlined workflow to create a new base image,
for example `takelage/takelbase` which has python and systemd installed.
Then we use the same pattern to develop our project 
based on that new base image.

We can use any kind of base image, for example the base image
that Google provides for its 
[App Engine](https://cloud.google.com/appengine/).
We can provision Google's base image which is an ancient Ubuntu box
with our ansible scripts and verify the result with our pytest scripts.
Afterwards, we can run an app engine based on that new docker image.
