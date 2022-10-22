# Tutorial on serving in containers

This tutorial covers the various ways one might host/serve a service from simplest dev to production.

## Development and Docker/Podman

There is a simple nuts-and-bolts guide to sandboxing and containerizing in [services](services/README.md). For development, and building containers, a few aspects are used

### Development sandbox

The app is in Python. One can create a sandbox/environment by using a conda environment. Conda is not Python specific, and can be used for other languages (e.g. R). Conda's job is to create reproducible sandbox environments. This environment is completely defined by a yaml file. This gives a way for multiple developers to work in the same environment, and also a way to reproduce that environment in a container.

### Docker

Docker can be used for containerization; building a portable image from which one can run the app. This can be done directly from the dockerfiles, or orchestrated using the docker-compose file.

### Podman

Podman (POD MANager) is an alternative to Docker. It improves on a number of aspects
- a daemon/service no longer needs to be running in the background
- images and containers are encapsulated to the user, offering better security (e.g. as a user, running `podman images` will only show that user's images)
- allow for multiple containers to be run in a single pod

Docker images and Podman images are completeley compatible; so building an image via podman or docker, and then pushing to an image repository, should not introduce any docker/podman compatibility worries. Generally speaking, all docker commands can be run wth podman (by interchanging 'docker' on the command lone for 'podman').