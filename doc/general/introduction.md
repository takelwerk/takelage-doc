# takelage-doc: introduction

[Table of contents](../../README.md)

## Takelage DevOps Workflow

The takelage devops workflow uses
[packer](https://packer.io/docs/builders/index.html), 
[ansible](https://docs.ansible.com/) and 
[molecule](https://molecule.readthedocs.io/)
to test-driven create machine images from code.
Although the target platforms are only limited 
by the packer builders the main target platform
of takelage are 
[docker containers](https://www.docker.com/resources/what-container).

Docker Containers can replace traditional VMs in the Cloud on 
[Google Compute Engine](https://cloud.google.com/compute/docs/containers),
using the
[Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html)
or running as
[Azure Container Instances](https://azure.microsoft.com/en-us/services/container-instances/).
But the most important virtualization technology is
Google's [Kubernetes](https://kubernetes.io/).
Kubernetes has gained wide-spread adoption in the DevOps community
and can be considered the default virtualization
environment to run docker containers.

Docker containers are spawned from docker images and the
most common way to build docker images are
[Dockerfiles](https://docs.docker.com/engine/reference/builder/).
Dockerfiles are written in a domain-specific language that uses 
standard bash commands as its core. 
As one-container-one-process is a recommended design pattern
for dockerized applications it makes sense to create really simple 
Dockerfiles. So what‘s wrong with Dockerfiles?

Here are some answers:

- Dockerfiles cannot be tested easily. Bash has never been good at that.
- Dockerfiles produce large images. Those that do not are full of logical AND
  operators which makes testing even more cumbersome.
- Dockerfiles are not reusable. The use of Dockerfiles promotes copy and paste
  which violates the 
  [DRY](https://en.wikipedia.org/wiki/Don't_repeat_yourself)
  principle.
- Dockerfiles can only produce docker images and target no other platform. 

This is why we propose an alternative workflow to build docker images:

- Build the images with packer.
- Write the provisioning code in ansible.
- Use ansible roles to reuse code and share them with 
  [bit](https://bit.dev) between 
  [git](https://git-scm.com) repositories.
- Use a molecule development environment.
- Test the code with 
  [pytest](https://docs.pytest.org/en/latest/), 
  [testinfra](https://testinfra.readthedocs.io/en/latest/) and 
  [takeltest](https://github.com/geospin-takelage/takelage-var).

We know that this is an opionated proposal.
We release our software under the GPL so feel free
to propose changes, report bugs or reuse all or parts of it.
This is only one way to build virtual machines that
works for us. So we want share and it we hope you like it.
