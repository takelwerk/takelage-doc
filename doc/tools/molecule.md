# takelage-doc: molecule

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [molecule](#molecule)

<a name="introduction"/>

# Introduction

<a name="molecule"/>

## molecule Development Framework

[molecule](https://github.com/ansible-community/molecule) is
designed to aid in the development and testing of ansible roles.
molecule is the takelage development virtualization software
which create one or more docker containers
(but can also handle other virtualization formats).
These containers can then be provisioned with ansible,
tested with pytest (and cucumber or behave) and
introspected by logging in to the container interactively.

molecule resides in directories named *molecule* which reside
below the ansible directory both in each role and in the project.
In the *molecule* directory are scenario directories which
configure independent development setups for the same role or
project. One of those directories is *molecule/default* and this
is often the only configured scenario.

Each molecule scenario is configured through a *molecule.yml*
file. It defines the base image which will be started to build
the role or project. The resulting container is then provisioned
and tested. This allows test-driven development of virtual hardware.

Furthermore, the *molecule.yml* files also defines
the sequences available in the scenario.
Besides *converge* (which provisions with ansible) and
*verify* (which tests with pytest) the final 
*test* sequence usually comprises 
*lint*, *syntax*, and *idempotence* which usually leads to
a very high coding standard and helps to find bugs early
in the development process.
