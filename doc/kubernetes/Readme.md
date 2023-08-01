# takelage-doc: general/docker_workflow
## Table of Contents

### General

- [debug](doc/general/introduction.md)
- [Apps](doc/general/docker_workflow.md)

## Debug

The docker image [netshoot](https://github.com/nicolaka/netshoot) provide alot of tools for debugging docker or kubernetes.

To run a container of the [netshoot image](https://hub.docker.com/r/nicolaka/netshoot) run the following command:

```kubectl run -n gitlab-runner my-shell --rm -it --image nicolaka/netshoot -- bash```

## Applications

### kubernetes-dashboard
- [howto](https://upcloud.com/resources/tutorials/deploy-kubernetes-dashboard) for deploying the kubernetes-dashboard 
- get a token for login: ```kubectl -n kubernetes-dashboard create token admin-user```
