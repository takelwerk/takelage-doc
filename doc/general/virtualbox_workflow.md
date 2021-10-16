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
[takelwerk/takelbase](https://app.vagrantup.com/takelwerk/boxes/takelbase)
vagrant base box.

### takelage vagrant project

We use the takelbase box as base for takelage vagrant virtualbox projects.
The virtual machine is started and provisioned by packer 
using the same ansible scripts we use for docker. 
Finally, a vm snapshot is saved as a vagrant box and uploaded 
to HasiCorp's vagrantup.com server.

### Example

The 
[takelage-pad](https://github.com/takelwerk/takelage-pad)
project is an example for the takelage vbox workflow.
We use 
[takelwerk/takelpad](https://hub.docker.com/r/takelwerk/takelpad)
to develop and test the project. We create the docker container on a
[debian build server](https://github.com/takelwerk/takelage-pad/actions/workflows/build_test_deploy_project_on_push.yml)
and upload the docker image to dockerhub.
The
[takelwerk/takelpad](https://app.vagrantup.com/takelwerk/boxes/takelpad)
vagrant virtualbox machine image is then created
on a 
[macos build server](https://github.com/takelwerk/takelage-pad/actions/workflows/build_deploy_project_vbox_on_push.yml)
and uploaded to vagrantup.
