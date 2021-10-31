# takelage-doc: tau/hg

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
    - [History](#history)
    - [Warning](#warning)
- [tau hg](#hg)
    - [tau hg list](#list)
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
with a focus on JavaScript code.
Mercurial is faster and smaller than bit.
Furthermore, it is a general purpose tool and 
thus better suited for takelage. 
As a bonus, takelage has the 
[hg-git](https://foss.heptapod.net/mercurial/hg-git)
plugin installed so we can use git remotes both for
the (git) projects and the (hg) subprojects.

<a name="warning"/>

## Warning

Mercurial repositories which are themselves controlled
by another code revision tool like git should only
exist in one git branch. 
Otherwise you might have to merge files in
`.hg` directories and you definitely want to avoid that.
That's why tau defines a `git_hg_branch: 'main'` branch.
`tau hg` will refuse to operate on any other branch.

<a name="hg"/>

# tau hg

The `tau hg` subcommands are shortcuts for some more or less
complicated bash commands which combine 
[`find`](https://www.gnu.org/software/findutils/manual/html_mono/find.html), 
[`parallel`](https://www.gnu.org/software/parallel/) and 
[`hg`](https://www.mercurial-scm.org/).

`tau hg` tries to keep in sync with upstream in order to
make a merge conflict less likely.
`tau hg pull` and `tau hg push` will only work
on the 
[git_hg_branch](https://github.com/takelwerk/takelage-cli/blob/main/lib/takeltau/default.yml)
which defaults to `main`.
This can be 
[changed](https://github.com/takelwerk/takelage-cli#configuration-examples)
generally or per project to any other branch.
Then they check that there are no uncommited changes 
and `git pull` the project so that you are in sync with upstream.
After running the hg commands any changed `.hg` directory
will be committed to git and a `git push` syncs the upstream repository.

<a name="list"/>

## tau hg list: list mercuial repos
`tau hg list` will find all Mercurial repositories
below the project root folder and 
print the `hg clone` commands which create them.

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
