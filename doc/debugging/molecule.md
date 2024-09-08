# takelage-doc: debugging/molecule.md

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
- [Ansible](#ansible)
  - [Logging in](#log_in_ansible)
- [Pytest](#pytest)
  - [Logging in](#log_in_python)
  - [Isolating pytests](#isolating_pytests)
  - [Ansible variables](#ansible_variables)

<a name="introduction"/>

# Introduction

You have two different possibilities how to create
a docker container with takelage:

1. `rake ansible:molecule:converge`
2. `rake images:project:build && rake images:project:molecule:converge`

The first option uses molecule to create a container from
a base image. Then it provisions this container with ansible.
Use this method to debug your [ansible](#ansible) scripts.

The second option uses packer and ansible to first build an image.
Then is uses molecule to create a container from this image.
Use this method to debug your [pytests](#pytest).

<a name="Ansible"/>

# Ansible

<a name="log_in_ansible"/>

## Log in

When `rake ansible:molecule:converge` fails you can run
`rake ansible:molecule:login` to log in to the container.
You can often run the failing command interactively and
reproduce the error in a disposable environment.

You should do this in a second terminal window.
Go into your project directory on your host machine
and use `tau login` to get a second shell in the
same takelage container.

Fix your ansible scripts and run
`rake ansible:molecule:converge`
in the first terminal window again.

<a name="pytest">

# Pytest

<a name="isolating_pytests"/>

## Log in

After a `rake images:project:molecule:converge` 
and a failing `rake images:project:molecule:verify`
you can run `rake images:project:molecule:login` 
to log in to the container.
You can then inspect the test environment to debug your pytests.

## Isolating pytests

When debugging a problem we always want to narrow down the amount of code we have to consider as much as possible. This is how you run only one pytest:

Mark the pytest function you want to run:

```python
import pytest

@pytest.mark.run_only_this_test
def test_my_python_test():
```

Then add an option to your `molecule.yml` file to run only the marked tests:

```yaml
verifier:
  name: testinfra
  options:
    m: run_only_this_test
```

<a name="ansible_variables"/>

## Ansible variables

You find the ansible testvars which have been gathered during your last
`molecule verify` run in `~/.cache/molecule/<project>/<scenario>/env/extravars`.
