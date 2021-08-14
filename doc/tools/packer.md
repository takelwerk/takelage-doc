# takelage-doc: tools/packer

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [packer](#packer)

<a name="introduction"/>

# Introduction

<a name="packer"/>

## packer Image Factory

[packer](https://www.packer.io) by
[HashiCorp](https://www.hashicorp.com/about)
is the engine of takelage. It is an amazing tool.

packer is distributed as a binary with a command line interface.
takelage uses 
[rake to wrap](../tools/rake.md#image)
complex packer commands.

At the time of writing in late 2020
packer is still mostly configured by [JSON template](https://github.com/takelwerk/takelage-dev/blob/master/packer/templates/docker/takelbase/project/build_from_base.json)
files.
But starting from version 
[1.5.0](https://www.hashicorp.com/blog/announcing-hashicorp-packer-1-5-with-hcl2-support)
packer can finally read 
[HCL2](https://www.packer.io/guides/hcl)
files.

packer is very flexible and versatile.
It can create images for a variety of 
[target platforms](https://www.packer.io/docs/builders)
using all major 
[provisioning tools](https://www.packer.io/docs/provisioners).

takelage is developed with docker in mind
but it works just as well for 
[vagrant](https://github.com/takelwerk/takelage-pad)
controlling 
[VirtualBox](https://www.virtualbox.org/)
(and probably most other target platforms).

takelage uses ansible as packer's provisioner.
