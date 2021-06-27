# takelage-doc: getting_started/workshop

[Table of contents](../../README.md)

## Getting Started: workshop

### Outline
1) Answers: What is takelage?
2) Example: Build a docker image with packer and ansible and test a docker container with pytest
3) Example: Minimal takelage project with rake subsystem

### Prerequisites:
- (on the host) the takelage ruby gem is installed
- (on the host) tau login works
- (in the container) tau status is "ok"

### 1) What is takelage?

takelage is a unified development and build environment. Mostly, you'll use the shell to interact with takelage – either interactively while developing or headless when testing and building. It's basically a versioned Debian system with some tools preinstalled running in a docker container which is running on a macOS or Linux host. takelage uses the bash command line interface, so for the sake of simplicity: takelage is the shell.

A unified environment solves the „it works on my machine `¯\_(ツ)_/¯`“ problem: one developer builds something successfully on her machine but no one (or worse: nearly no one) else is able to reproduce it on their machine. More honestly, the problem should be called: „it does not work on your machine `¯\_(ツ)_/¯`“.

One other common pitfall for most projects is the „outdated README“ problem. Especially commands which are noted in README files are likely to change over time. Sadly, the necessary changes in the documentation tend to be forgotten. If you copy outdated commands from a README they may work. But they may also wreck havoc. This is why takelage suggests to put shell commands in Rakefiles which can be then executed by calling a rake command with a descriptive name and a description. These rake tasks will be executed frequently by your colleagues and pipelines so it is far more likely that mistakes will be found.

### 2) Example: Build a docker image with packer and ansible and test a docker container with pytest

See [Hello world!](helloworld.md) example.

### 3) Example: Minimal takelage project with rake subsystem

This example is very similar to the [Hello world!](helloworld.md) example. Change into a new directory `takelage-hellorake` and run `tau login`. The main difference is the `tau init` command: 

```bash
tau init takelage rake hellorake
```

This will create a new project with a preconfigured [rake subsystem](../tools/rake.md).
It does not include any kind of default build system.

You may want to create a new rake namespace called `project` in a `rake/project/Rakefile`.
Now you can create a rake task `hello` with the description "Hello world in rake lingo" in the namespace `project` which will run the command `echo "world!"`.




