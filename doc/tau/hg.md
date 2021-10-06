# takelage-doc: tau/hg

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
    - [History](#history)
    - [Warning](#warning)
- [Usage tau hg](#hg)
    - [tau hg export](#export)
    - [tau hg pull](#pull)
    - [tau hg push](#push)

<a name="introduction"/>

# Introduction

[Mercurial](https://www.mercurial-scm.org/)
(with its binary `hg`) is a free, 
distributed source control management tool just like git.
The main difference from git is that it is not git 
and thus can be used within a git repository.
It solves the problem to share parts of git repositories or
the "git in git" problem.


<a name="history"/>

## History

Prior to Mercurial takelage used [bit](https://docs.bit.dev/)
which is an
[open-source](https://github.com/teambit/bit)
cli tool for collaborating on isolated components
across projects and repositories 
with a focus on JavaScript projects.
Mercurial is faster and smaller than bit.
Furthermore, it is a general purpose tool and 
thus better suited for takelage. 
As a bonus, takelage has the 
[hg-git](https://foss.heptapod.net/mercurial/hg-git)
plugin installed to we can use git remotes both for
the projects and the subprojects.

<a name="warning"/>

## Warning

Mercurial repositories which are themselves controlled
by another code revision tool like git should only
exist in one git branch. 
Otherwise you might have to merge files in `.hg`
and you definitely want to avoid that.
That's why tau defines a **git hg branch** through the
`git_hg_branch` setting, which is by default 
git's `main` branch.
`tau hg` will refuse to operate on any other branch
but you can choose your favorite git hg branch by setting
`git_hg_branch: main` to a different value.

<a name="hg"/>

# Usage tau hg

The `tau hg` subcommands are shortcuts for complicated
bash commands which combine 
[`find`](https://www.gnu.org/software/findutils/manual/html_mono/find.html), 
[`parallel`](https://www.gnu.org/software/parallel/)
and `hg`.

<a name="export"/>

## tau hg export: export mercuial repos
`tau hg export` will find all Mercurial repositories
below the project root folder and 
print the `hg clone` commands which created them.

<a name="pull"/>

## tau hg pull: pull mercuial repos

`tau hg pull` will find all Mercurial repositories
below the project root folder and pull updates from remote.

<a name="push"/>

## tau hg push: push mercuial repos

`tau hg push` will find all Mercurial repositories
below the project root folder,
add and commit new and changed files 
and then push the new changesets.
