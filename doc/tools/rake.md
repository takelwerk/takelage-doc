# takelage-doc: rake

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [rake](#rake)
  - [Examples](#example)
- [Usage rake ansible](#ansible)
- [Usage rake image](#image)
- [Usage rake project](#project)

<a name="introduction"/>

# Introduction

<a name="rake"/>

## rake Task Management Tool

[rake](https://github.com/ruby/rake) is ruby's make.
In takelage it is used as a command wrapper.
While the 
[tau](https://github.com/geospin-takelage/takelage-cli) 
handles global not project specific tasks
like “log in to the development environment”
or “download updates of bit components”
rake handles project specific tasks like
“test the build” or “tag the image”.

rake tasks reside in Rakefiles in directories
below a rake directory in the project root directory.
The directory nesting is echoed in namespaces
which create a command hierarchy.

Lots of takelage Rakefiles are shared as 
[bit](../tau/tau_bit.md)
components. They can be added to a project
if needed but no project needs all Rakefile.

Some of takelage's Rakefiles are parametrized
and outsource the heavy work to library files
which are plain ruby files that can be found 
in lib directories.

In takelage, `rake` is a shortcut for `rake -T` which
shows all currently available rake tasks:

```
rake
```

<a name="example"/>

## Examples

Let's have a look at two examples.
We start with a rake shortcut for a call of
[rubocop](https://github.com/rubocop-hq/rubocop)
with some custom options. 

Relative to our project root, we create the file 
`rake/rubylint/Rakefile` with this content:

```ruby
# frozen_string_literal: true

cmd_rubylint = 'rubocop --parallel --display-style-guide'

desc 'Run ruby linter'
task :rubylint do
  @commands << cmd_rubylint
end
```

This Rakefile (in combination with the 
[rake meta bit component](https://github.com/geospin-takelage/takelage-dev/blob/master/rake/meta/Rakefile))
gives us a rake shortcut:

```bash
rake rubylint
```

A lightly more complex example is a 
rake shortcut to create a git tag
with the project version 
if such a tag does not exist yet 
and push the local tags to remote afterwards.

Relative to our project root, we create the file 
`rake/git/Rakefile` with this content:

```ruby
# frozen_string_literal: true

require 'rake'

cmd_git_tag = 'git rev-parse ' \
    "#{@project['version']} " \
    '>/dev/null 2>&1 || ' \
    '(git tag -s -m ' \
    "'#{@project['version']}' " \
    "#{@project['version']} && " \
    'git push --tags)'

namespace :git do
  desc 'Create and push git tag'
  task :tag do
    @commands << cmd_git_tag
  end
end
```

This Rakefile gives us a rake shortcut:

```bash
rake git:tag
```

<a name="ansible"/>

# Usage rake ansible

These rake tasks handle the development of a project or a role. 

Here is an example:

```bash
rake ansible:docker:takelbase:project:prod:from_base:converge
```

This task will run `molecule converge` on the
production environment building on the 
[takelbase](https://hub.docker.com/r/takelage/takelbase)
docker base image.

Similar tasks exist for every molecule command for the project
and for every ansible role.
So in this case, a handful of Rakefiles can lead to dozens of tasks.

You can have a look what commands a task will run by appending `run=dry`:

```
$ rake ansible:docker:takelbase:project:prod:from_base:converge run=dry
cd ansible && bash -c 'TAKELAGE_PROJECT_ENV=prod TAKELAGE_PROJECT_BASE_IMAGE=takelage/takelbase TAKELAGE_PROJECT_NAME=takelage-dev TAKELAGE_MOLECULE_CONVERGE_PLAYBOOK=playbook-project-base.yml molecule converge --scenario-name default'
```

<a name="image"/>

# Usage rake image

Another rake namespace which is almost always present is `rake image`:

```bash
$ rake
...
rake image:docker:takelbase:base:debian10                      # Build takelbase base image with packer
rake image:docker:takelbase:project:prod:from_base             # Build takelbase project image with packer
...
```

In this case, we have two different tasks:

`rake image:docker:takelbase:base:debian10`
will build a takelbase debian 10 docker base image with packer.

`rake image:docker:takelbase:project:prod:from_base`
will build a docker image of our project with packer.

<a name="project"/>

# Usage rake project

The modular structure of takelage's Rakefiles make it possible
to build a custom interface to each project with reusable
bit components. 

But some tasks differ from project to project.
For example, the task `rake project:prod` should trigger the
whole build queue of each project 
which is different for each project.

So the file `rake project/Rakefile` is no bit component.
It consists of meta tasks which do not add code by itself
but call dependent rake tasks to fulfill whatever necessary.

Here is an example
[Rakefile](https://github.com/geospin-takelage/takelage-cli/blob/master/rake/project/Rakefile)
of the takelage cli:

```ruby
# frozen_string_literal: true

namespace :project do
  desc 'Build takelage cli ruby gem'
  task prod: %w[gem:signin
                rubylint
                test
                features
                doc:clean
                doc:build
                doc:commit
                gem:clean
                gem:build
                gem:move
                git:tag
                gem:push]
end
```

Remember that you can display all tasks with their dependencies:

```bash
$ rake -P
...
rake project:prod
    gem:signin
    rubylint
    test
    features
    doc:clean
    doc:build
    doc:commit
    gem:clean
    gem:build
    gem:move
    git:tag
    gem:push
...
```

By runnint `rake project:prod` you will in fact run all
the subtasks listed above. 
If one of the dependent tasks fails
the whole composite command will fail.
