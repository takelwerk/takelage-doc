# takelage-doc: debugging/molecule.md

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
- [Techniques](#techniques)
  - [Logging in](#logging_in)
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

The second option uses packer and ansible to build first build
an image. Then is uses molecule to create a container from this image.

When we want to debug our ansible scripts or our python tests
we use the first method.

<a name="techniques"/>

# Techniques

<a name="logging in"/>

## Logging in

When `rake ansible:molecule:converge` fails you can use
`rake ansible:molecule:login` to log in to the container
and inspect the environment that caused the problem.
You can often run the failing command interactively and
reproduce the error this way in a disposable environment.

<a name="isolating_pytests"/>

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
