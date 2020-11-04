# takelage-doc: rake

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
  - [rake](#rake)

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

## rake Example

Let's have a look at a minimal example.
We want a rake shortcut to create a git tag
with the project version if such a tag 
does not exist yet 
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

This Rakefile (in combination with the 
[rake meta bit component](https://github.com/geospin-takelage/takelage-dev/blob/master/rake/meta/Rakefile))
gives us a rake shortcut

```bash
rake git:tag
```

