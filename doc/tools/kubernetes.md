# takelage-doc: tools/kubernetes

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
- [Usage](#usage)

<a name="introduction"/>

# Introduction

[Kubernetes](https://kubernetes.io)
is the de facto standard to run docker containers in production.
To test the docker containers created with the takelage framework
in a local kubernetes development environment
takelage comes with kubernetes in docker preinstalled:
[k3d](https://k3d.io)
(which is a wrapper for
[k3s](https://k3s.io)).

<a name="usage"/>

# Usage

Create a new kubernetes cluster:

```bash
k3d cluster create
```

Run `kubectl` to run an interactive pod in kubernetes:

```bash
kubectl run -it --rm debian --image debian -- bash
```
