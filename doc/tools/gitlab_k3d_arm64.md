# gitlab/docker with gitlab-runner/k3d on Apple Silicon

## Create your own factory

GitHub Actions does not have arm64 runners.
But any Apple Silicon based machine can
be used to run a 
[GitLab](https://gitlab.com/)
CI/CD platform and a
[k3d](https://k3d.io/)
kubernetes cluster for the gitlab runner 
using Docker Desktop.
This is how the takelage arm64 images are built.

A gitlab docker image is officially available
for amd64 but not for arm64. Unofficially, it is.

We install k3d via
[homebrew](https://brew.sh/).

## Prerequisites

Be sure to change the following setting 
in your Docker Desktop docker host:

`Choose file sharing implementation for your containers`

Do not use `VirtioFS` or `FUSE`. Use `gRPC`.

This changes the way mounts are done.
If you run into weird unix socket connection problems, 
may be on a new machine: remember this advice.

## GitLab & k3d

### Run GitLab via Docker Compose 

We run GitLab in a docker container by using
[Docker Compose](https://docs.docker.com/compose/).

### Official GitLab Docker images (for amd64 only)
Set the `GITLAB_HOME` environment variable
and get the docker compose file from:
_Install GitLab using Docker Compose_

https://docs.gitlab.com/ee/install/docker.html

### Inofficial GitLab CE image for arm64

Chose a suitable gitlab image for arm64.
It's always good to watch out for newer, better images.
Here is one that worked well in summer 2023:

https://hub.docker.com/r/yrzr/gitlab-ce-arm64v8

### The `docker-compose.yml`

You should now have a `docker-compose.yml` file
similar to this one:
```
version: '3.6'
services:
  web:
    image: 'yrzr/gitlab-ce-arm64v8:latest'
    restart: always
    hostname: 'gitlabje'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://gitlabje:7280'
        gitlab_rails['gitlab_shell_ssh_port'] = 7222
    ports:
      - '7280:7280'
      - '7222:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

## First GitLab Run: Become root

Start the gitlab docker container:
```bash
docker compose up
```

Get the initial password:
```bash
cat $GITLAB_HOME/config/initial_root_password
```

Sign in using `root` and the initial password:
http://localhost:7280

Register a user. 
Then disallow arbitrary logins as recommended by the 
_Check your sign-up restrictions_ alert.

Set the `root` password
[from the command line](https://docs.gitlab.com/ee/security/reset_user_password.html#reset-your-root-password)
to some real secret and store it in your password manager.
```bash
docker exec -it gitlab-web-1 gitlab-rake "gitlab:password:reset[root]"
```

Sign in to http://localhost:7280 again 
to confirm the `root` password reset.

## Add a Runner: Get a token

Get a `runnnerToken`
[in the GitLab UI](https://docs.gitlab.com/ee/ci/runners/register_runner.html#generate-an-authentication-token).

Create a shared runner with a runner authentication token.
If unsure, select „Run untagged jobs“.

## Add Kubernetes: Get k3d

We use k3d to house the 
[GitLab Runners](https://docs.gitlab.com/runner/).
We can then use the
[Kubernetes executor](https://docs.gitlab.com/runner/executors/kubernetes.html)
to run our GitLab pipelines in
[Kubernetes](https://kubernetes.io/)

Add k3d & friends via homebrew:
```bash
brew install k3d kubernetes-cli helm
```

## Configure a kubernetes runner

We use the GitLab Runner
[Helm Chart](https://docs.gitlab.com/runner/install/kubernetes.html)
to deploy our runners:
```bash
helm repo add gitlab https://charts.gitlab.io
```

Download the helm source code 
of the gitlab runner:
```bash
helm pull gitlab/gitlab-runner --untar
```

Remember that `host.k3d.internal` is your macOS `localhost`.

Configure `gitlab-runner/values.yaml`:
```yaml
image:
  registry: registry.gitlab.com
  image: gitlab-org/gitlab-runner

useTini: false
imagePullPolicy: IfNotPresent
gitlabUrl: http://host.k3d.internal:7280/
runnerToken: <YOUR_TOKEN>
terminationGracePeriodSeconds: 3600
concurrent: 10
checkInterval: 30
sessionServer:
  enabled: false

rbac:
  create: true
  rules: []
  clusterWideAccess: false
  podSecurityPolicy:
    enabled: false
    resourceNames:
      - gitlab-runner

metrics:
  enabled: false
  portName: metrics
  port: 9252
  serviceMonitor:
    enabled: false

service:
  enabled: false
  type: ClusterIP

runners:
  config: |
    [[runners]]
      clone_url = "http://host.k3d.internal:7280"
      [runners.kubernetes]
        namespace = "{{.Release.Namespace}}"
        image = "debian:stable-slim"
        name = "kubernetes"
        privileged = true
      [[runners.kubernetes.services]]
        name = "docker:dind"  
      [[runners.kubernetes.volumes.empty_dir]]
        name = "docker-certs"
        mount_path = "/certs/client"
        medium = "Memory"

  cache: {}

securityContext:
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: false
  runAsNonRoot: true
  privileged: false
  capabilities:
    drop: ["ALL"]

podSecurityContext:
  runAsUser: 100
  fsGroup: 65533

resources: {}
affinity: {}
nodeSelector: {}
tolerations: []
hostAliases: []
podAnnotations: {}
podLabels: {}
priorityClassName: ""
secrets: []
configMaps: {}
volumeMounts: []
volumes: []
```
You need `privileged = true` 
if you want to run docker in docker (dind).

## Deploy a kubernetes runner

Create a gitlab kubernetes cluster:
```bash
k3d cluster create gitlab
```

Create a namespace:
```bash
kubectl create namespace gitlab
```

Install (or `helm upgrade` after changes) the helm chart (adapt the path):
```
helm install --namespace gitlab gitlab-runner -f $GITLAB_HOME/../gitlab-runner/values.yaml gitlab/gitlab-runner
```

You may make this namespace the default namespace:
```bash
kubectl config set-context --current --namespace=gitlab
```
