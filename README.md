# fastapi-docker-kube
A repo for exploring a simple app, and the deployment stages from pure docker or podman, to kube and helm.

## Installations ##

Specific installations will depend on your environment, and will also depend on the kubernetes flavour you wish to work with. The instructions below are for an Ubuntu 20.04 box.

### Docker installation ###

Docker is in the apt repos for Ubuntu 20.04

```
sudo apt install docker.io
```

### Podman installation ###

Podman is not in the apt repos for Ubuntu 20.04 (it is for Ubuntu 20.10 upward). For Ubuntu 20.04, one can use [these instructions](https://www.atlantic.net/dedicated-server-hosting/how-to-install-and-use-podman-on-ubuntu-20-04/)

### Minikube installation ###

The [minikube](https://minikube.sigs.k8s.io/docs/start/) install instructions are a little sparse. [This guide](https://www.linuxtechi.com/how-to-install-minikube-on-ubuntu/) covers more details.

### Kompose installation ###

The [kompose install pages](https://kompose.io/installation/) describe what to do.

### Helm installation ###

There are a variety of methods, although doing manually from the binary releases as described on the [helm install docs](https://helm.sh/docs/intro/install/) is fairly straightforward.

## Available contents ##

Each folder contains a simple README.md that describes how to get the code running.
- __services__ : contains a Python app that can be built in a conda environment, or in a docker container
- __k8s__ : contains kubernetes manifests for kubernetes deployment
- __helm__ : contains a helm chart for kubernetes deployment

There is also an over-arching [tutorial](Tutorial.md) that references each of those README.mds.