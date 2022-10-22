# fastapi-docker-kube
repo for exploring a simple app, and the deployment stages from pure docker or podman, to kube and helm

## Installations ##

Specific installations will depend on your environment, and will also depend on the kubernetes flavour you wish to work with. The instructions below are for an Ubuntu 20.04 box.

### Docker installation ###

Docker is in the apt repos for Ubuntu 20.04

```
sudo apt install docker.io
```

### Podman installation ###

Podman is not in the apt repos for Ubuntu 20.04 (it is for Ubuntu 20.10 upward). For Ubuntu 20.04, one can use [these instructions](https://www.atlantic.net/dedicated-server-hosting/how-to-install-and-use-podman-on-ubuntu-20-04/)
