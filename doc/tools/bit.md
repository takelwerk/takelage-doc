# takelage-doc: bit

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [bit](#bit)
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
the [bit docs](https://docs.bit.dev/docs/faq).

The takelage devops workflow uses the open source parts of bit.
The bit part of takelage is targeting devops engineers.
It enables them to share parts of their git repositories.

<a name="usage"/>

# Usage

bit is meant for JavaScript.
As we want to use to share general components
we have to switch off the JavaScript part.
Futhermore, most fine-grained bit operations 
are not needed in daily usage. 

For these reasons,
[takelage-cli](https://github.com/geospin-takelage/takelage-cli)
chains certain bit commands and wraps.
This way, `tau` provides a high-level cli to bit
without reengineering bit's features.

## Copy a component

<a name="copy"/>

## Paste a component

<a name="paste"/>

## Push all components

<a name="push"/>

## Pull all components

<a name="pull"/>
