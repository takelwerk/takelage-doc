# takelage-doc: tools/kubernetes

[Table of contents](../../README.md)

## Overview

- [Introduction](#introduction)
- [Usage](#usage)

<a name="introduction"/>

# Introduction

[Kubernetes](https://kubernetes.io)
is the de facto standard to run docker containers in production.
The takelage framework creates docker containers.
To test these containers in a local kubernetes development environment
takelage comes with kubernetes in docker preinstalled:
[k3d](https://k3d.io)
(which is a wrapper for
[k3s](https://k3s.io)).

<a name="usage"/>

# Usage

Create a new kubernetes cluster:

```bash
k3d cluster create mycluster
```

Run `kubectl` to talk to kubernetes:

```bash
kubectl get nodes
```
