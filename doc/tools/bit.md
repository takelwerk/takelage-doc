# takelage-doc: bit

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [bit](#bit)
  - [Warnings](#warnings)
  - [Definitions](#definitions)
- [Usage](#usage)
  - [Copy](#copy)
  - [Paste](#paste)
  - [Push](#push)
  - [Pull](#pull)

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
[bitboard server](https://github.com/geospin-takelage/takelage-bit)
created with this project.

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

As we want to use to share general components
we have to switch off the javascript part.
Futhermore, most fine-grained bit operations 
are not needed in daily usage in our case.

For these reasons,
[takelage-cli](https://github.com/geospin-takelage/takelage-cli)
chains certain bit commands and wraps.
This way, `tau` provides a high-level cli to bit
without reengineering bit's features.
See the 
[cucumber features](https://github.com/geospin-takelage/takelage-cli#commands)
for a more technical but living documentation.

<a name="warnings"/>

## Warnings

bit is a stateful tool. 
This means, that there is a single global resource
which records the state of a project.
This resource file `bit.json` is shared via git.
This means, you must only use bit in the 
master branch of git.
`tau bit clipboard` will complain 
if you are on any other branch.

<a name="definitions"/>

## Definitions

Let `mydir/mycomponent` be a directory
relative to the project root directory.
Let `myscope` be a scope either on `bit.dev`
or on a private bit server.

<a name="usage"/>

# Usage

<a name="copy"/>

## Copy a component

`tau copy` (which is an alias for `tau bit clipboard copy`)
copys a directory as a bit component to a scope.

```bash
tau copy mydir/mycomponent myscope
```

It will create a `README.bit` in `mydir/mycomponent`
which may be edited but must not be deleted.

`tau paste` chains `bit tag`, `bit export` and `bit status`.

<a name="paste"/>

## Paste a component

`tau paste` (which is an alias for `tau bit clipboard paste`)
pastes a bit component from a scope as a directory.

```bash
tau paste myscope/mydir/mycomponent /mydir/mycomponent
```

`tau paste` chains `bit import`, `bit checkout` and `bit status`.

<a name="push"/>

## Push all components

`tau push` (which is an alias for `tau bit clipboard push`)
will push every change in all components of a project to
their remote scopes. 

```bash
tau push
```

`tau push` chains `bit tag`, `bit export` and `bit status`.

<a name="pull"/>

## Pull all components

`tau pull` (which is an alias for `tau bit clipboard pull`)
will pull every remotely changed component of a project.

```bash
tau pull
```

`tau pull` chains `bit import`, `bit checkout` and `bit status`.
