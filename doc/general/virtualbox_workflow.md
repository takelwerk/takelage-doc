# takelage-doc: general/virtualbox_workflow

[Table of contents](../../README.md)

## Takelage VirtualBox Workflow

takelage helps developing, producing and testing docker images.
But docker images use container virtualization 
and sometimes you want paravirtualization.
Takelage can use the packer vagrant virtualbox build module
to create vagrant boxes.

### takelage vagrant base box

takelage docker images are based on docker base images.
takelage vagrant virtualbox images are based on
Debian netinst.iso files instead.
The 
[takelage-vbox-takelbase](https://github.com/takelwerk/takelage-vbox-takelbase)
project uses packer to install Debian headlessly using a preseed file.
It then creates the
[takelwerk/takelbase]()


### produce and test

To produce and test a docker image
takelage will first use packer and then molecule.
Producing a docker image means creating the production-ready docker image.
Testing a docker image means 
checking that a docker container based on that image works as expected.

A standard work pattern is this:

1. packer creates a docker image 
   by provisioning a docker base image with ansible scripts.
1. molecule creates a docker container from that docker base image.
1. molecule verifies the docker container by running pytest scripts 
   which have access to the ansible variables.

## Examples

As the takelage docker workflow is based on
ansible, pytest, molecule and packer which
are very flexible tools the workflow itself
becomes incredibly flexible, too.
Here are two examples which are quite different:

The above outlined work patterns are used iteratively.
We start with a base image, for example `debian/stable-slim`.
Then we use the above outlined workflow to create a new base image,
for example `takelwerk/takelbase` which has python and systemd installed.
Then we use the same pattern to develop our project 
based on that new base image.

We can use any kind of base image, for example the base image
that Google provides for its 
[App Engine](https://cloud.google.com/appengine/).
We can provision Google's base image which is an ancient Ubuntu box
with our ansible scripts and verify the result with our pytest scripts.
Afterwards, we can run an app engine based on that new docker image.
