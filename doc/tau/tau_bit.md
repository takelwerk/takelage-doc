# takelage-doc: tau bit

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [bit](#bit)
  - [Warning](#warning)
  - [Definitions](#definitions)
- [Usage tau bit clipboard](#clipboard)
  - [tau copy](#copy)
  - [tau paste](#paste)
  - [tau push](#push)
  - [tau pull](#pull)
- [Usage tau bit scope](#scope)
  - [tau bit scope add](#add)
  - [tau bit scope list](#list)
  - [tau bit scope new](#new)
  - [tau bit scope inbit](#inbit)
- [Usage tau bit require](#require)
  - [tau bit require export](#export)
  - [tau bit require import](#import)

<a name="introduction"/>

# Introduction

<a name="bit"/>

## bit Components Sharing Tool

[bit](https://docs.bit.dev/) is an 
[open-source](https://github.com/teambit/bit)
cli tool for collaborating on isolated components 
across projects and repositories.
It solves the problem to share parts of git repositories.

bit is a 
[git](https://git-scm.com) extension.
bit is not meant to replace git.
You may think of bit as a git powered clipboard manager
with versioning and central storage.
For open source projects you may use 
[bit.dev](https://bit.dev)
as your central bit storage.
For software testing or private use you may use a 
[bitboard server](https://github.com/geospin-takelage/takelage-bit).

Both bit and git are in their core free and open source tools.
Around these tools both free and non-free infrastructure has emerged.
git sparked projects like 
[GitHub](https://github.com) and 
[GitLab](https://gitlab.com) while bit has been
developed by Cocycles, the company behind 
[bit.dev](https://bit.dev).

bit.dev's intended audience is the javascript community and
they also host the best resources for bit: 
the [bit docs](https://docs.bit.dev/).

As we want to use bit to share general components
we have to switch off the javascript part.
Futhermore, most fine-grained bit operations 
are not needed in daily usage in our case.

For these reasons,
[takelage-cli](https://github.com/geospin-takelage/takelage-cli)
chains and wraps certain bit commands.
This way, `tau` provides a high-level cli to bit
without reengineering bit's features.
See the 
[cucumber features](https://github.com/geospin-takelage/takelage-cli#commands)
for a more technical but living documentation.

<a name="warning"/>

## Warning

bit is a stateful tool. 
This means, that there is a single global resource
which records the state of a project.
This resource file `bit.json` is shared via git.
As a consequence, you must only use bit in the 
master branch of git.
`tau bit clipboard` will complain 
if you are on any other branch.

<a name="definitions"/>

## Definitions

Let `mydir/mycomponent` be a directory
relative to the project root directory.
Let `myscope` be a scope either on `bit.dev`
or on a private bit server.

<a name="clipboard"/>

# Usage tau bit clipboard

<a name="push"/>

## tau push: upload all components

`tau push` (which is an alias for `tau bit clipboard push`)
will push every change in all components of a project to
their remote scopes. 

```bash
tau push
```

`tau push` chains `bit tag --all`, 
`bit export --all` and `bit status`.

<a name="pull"/>

## tau pull: download all components

`tau pull` (which is an alias for `tau bit clipboard pull`)
will pull every remotely changed component of a project.

```bash
tau pull
```

`tau pull` chains `bit import`, 
`bit checkout --all latest` and `bit status`.

<a name="copy"/>

## tau copy: upload one component

`tau copy` (which is an alias for `tau bit clipboard copy`)
copys a directory as a bit component to a scope.

```bash
tau copy mydir/mycomponent myscope
```

It will create a `README.bit` in `mydir/mycomponent`
which may be edited but must not be deleted.

`tau paste` chains `bit tag`, `bit export` and `bit status`.

<a name="paste"/>

## tau paste: download one component

`tau paste` (which is an alias for `tau bit clipboard paste`)
pastes a bit component from a scope as a directory.

```bash
tau paste myscope/mydir/mycomponent /mydir/mycomponent
```

`tau paste` chains `bit import`, `bit checkout` and `bit status`.

<a name="scope"/>

# Usage tau bit scope

A bit scope is a directory in which `bit init --bare` has been executed.
This can be a directory on the local machine or on a remote server.
In the latter case the scope is called a remote scope.
“Built-in” remote scopes are those on
[bit.dev](https://bit.dev/).
Custom remote scopes are located on a 
[custom bit server](https://github.com/geospin-takelage/takelage-bit)
and have to be added to a local bit workspace
before they can be used.

If you want to use a custom bit server with `tau`
you have to set two configuration options,
e.g. in your `~/.takelage.yml`:

```yaml
bit_remote: ssh://bit@exmaple.com:2222:/bit
bit_ssh: ssh -p 2222 bit@example.com
```

<a name="add"/>

## tau bit scope add: add a scope to local workspace 

`tau bit scope add` makes a remote scope on a custom bit server
(as opposed to a scope on `bit.dev`) 
available in a local bit workspace.

```bash
tau bit scope add bitboard.myscope
```

`tau bit scope add` is a wrapper for `bit remote add`.

<a name="list"/>

## tau bit scope list: list remote scopes

`tau bit scope list` will list the remote scopes on a custom bit server.
These scopes can then be added with `tau bit scope add`.

```bash
tau bit scope list
```

<a name="new"/>

## tau bit scope new: create a new remote scope

`tau bit scope new` creates a new remote scope on a custom bit server.

```bash
tau bit scope new myscope
```

<a name="inbit"/>

## tau bit scope inbit: log in to a remote bit server

`tau bit scope inbit` will ssh in to the configured custom bit server.

```bash
tau bit scope inbit
```

This command can be used for debugging purposes
and to remove custom remote bit scopes.
`tau` does not expose scope deletion as it is considered too
dangerous for a daily option.
You can remove a custom bit remote scope by using
`tau bit scope inbit` and then `rm -fr /bit/myscope`.

<a name="require"/>

# Usage tau bit require

`tau bit require` can  “clone” a project in the
sense that it will install all bit components
which are installed in a source project 
in the destination project.

<a name="export"/>

## tau bit require export: list bit components 

`tau bit require export` will list the bit scopes and components
of a project.

```bash
tau bit require export
```

You can save the bit scopes and components of a project
by redirecting the output to a file:

```bash
tau bit require export > bitrequire.yml
```

<a name="import"/>

## tau bit require import: bulk install bit components 

`tau bit require import` will read bit components from 
a `bitrequire.yml` file and install them.

```bash
tau bit require import
```

At the moment, you have to add custom remote bit scopes
to the local workspace so that `bit require import`
to install the components in those scopes.
