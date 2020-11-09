# takelage-doc: workflow

[Table of contents](../../README.md)

## Overview 

- [Workflow](#workflow)
  1 [Develop provisioning scripts](#develop)
  1 [Create image](#create)
  1 [Test image](#test)
  1 [Deploy image](#deploy)

<a name="workflow"/>

# takelage DevOps Workflow

The [takelage devops workflow](../general/introduction.md)
generally consists of these basic steps:

1. Develop ansible roles and project with 
[molecule](../tools/molecule.md)
1. Create image with 
[packer](../tools/packer.md)
1. Test image with 
[molecule](../tools/molecule.md)
1. Deploy image to a registry

This is only a rough blueprint as different projects
require different steps. 
One step that is often needed is running the image
locally to develop software in the image.
Let's have a look at each of the basic steps.

<a name="develop"/>

## Develop provisioning scripts

Before building an image with packer we need to
develop the ansible scripts used by packer.
For this, we use molecule and docker to provide
a development environment.
One big advantage is that one molecule environment
behaves like any other molecule environment.

When we run `molecule converge` (or the
correspoding [rake](../tools/rake.md) task)
a new docker container is created and
our ansible scripts are run.
If they fail, we can fix the bugs and run
`molecule converge` until we have *converged*
to a final state which is idempotent.
Idempotence in this case means that running
ansible twice is the same as running it once.

We can introspect the machine whenever
necessary with a `molecule login`.
This action generally has no counterpart in
traditional software testing.

In addition, we write python tests with pytest
for our ansible scripts. They check, if the state
of the virtual machine is correct.
Of course, we can write those tests first and
run them with `molecule verify` after an initial
`molecule converge`. 
That's test-driven development of virtual hardware.

The tests we write for ansible roles can be viewed
as unit tests as they test the building blocks of
our project.
Running all role tests together can be viewed as
integration tests.
And we can also write tests for the whole project
(in *ansible/molecule/default/tests*) which
can be viewed as system tests.

<a name="create"/>

## Create image

When our ansible scripts are ready for prime time
we use packer to create a machine image with these
scripts.

The image creation is highly dependent on the target platform.

<a name="test"/>

## Test image

After we have created the final image we can use
this image as our molecule base image
(by creating another molecule scenario in
*ansible/molecule/image*)
to start and then test the image.

With even more scenarios we can run our image
locally and tunnel ports for testing.
Or we may debug it by starting a shell like
`/bin/bash`.
instead of the usual start command like
`/lib/systemd/systemd`.

<a name="deploy"/>

## Deploy image

The deployment is highly dependent on the target platform.

In case of docker, deploying an image means
tagging and then pushing it to a registry.
