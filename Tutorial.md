# Tutorial on serving in containers

This tutorial covers the various ways one might host/serve a service from simplest dev to production.

## Development and Docker/Podman

There is a simple nuts-and-bolts guide to sandboxing and containerizing in [services](services/README.md). For development, and building containers, a few aspects are used

### Development sandbox

The app is in Python. One can create a sandbox/environment by using a conda environment. Conda is not Python specific, and can be used for other languages (e.g. R). Conda's job is to create reproducible sandbox environments, including executable binaries (which virtualenv and venv cannot). This environment is completely defined by a yaml file. This gives a way for multiple developers to work in the same environment, and also a way to reproduce that environment in a container.

### Docker

Docker can be used for containerization; building a portable image from which one can run the app. This can be done directly from the dockerfiles, or orchestrated using the docker-compose file.

### Podman

Podman (POD MANager) is an alternative to Docker. It improves on a number of aspects
- a daemon/service no longer needs to be running in the background
- images and containers are encapsulated to the user, offering better security (e.g. as a user, running `podman images` will only show that user's images)
- allow for multiple containers to be run in a single pod

Docker images and Podman images are completeley compatible; so building an image via podman or docker, and then pushing to an image repository, should not introduce any docker/podman compatibility worries. Generally speaking, all docker commands can be run wth podman (by interchanging 'docker' on the command line for 'podman').

### Infrastructure as a Service (IaaS)

The development chain described above can be used to host a service in production. One can create a virtual machine on one's favourite VMWare or cloud provider, and run a containerized service on that virtual machine. This provisioning of a virtual machine is called 'Infrastructure as a Service', (IaaS). Whilst it works, this paradigm has some limitations
- for new services, one has to go through the work of creating the infrastructure
- the scope, number of containers, and resource of the services are limited by the created infrastructure
- any kind of scaling has to be done 'by hand'
- failure of virtual machines must be handled directly
- any kind of upgrades can result in services being temporarily down whilst containers are spund down and spun up

Ideally, one would like a system that abstracts away the infrastructure worries, and only worries about serving images in containers; some kind of image-and-container orchestration. One might also like to have a picture of just pushing an image to that system, and the system perform the orchestration (similar to how one might push an image repository, and then the repository manages all the hosting).

This is precisely the reasoning for introducing a 'Platform as a Service' (PaaS). It is a system where one can push images, and then the system handles many of the intricacies of orchestrating the serving of containers. The system can be clustered over multiple virtual machines, meaning the developer pushing the image to PaaS does not need to worry about individual virtual machines; only about how to serve containers based on the image.

## Hosting on Kubernetes

Kubernetes (K8S) is the classic example of a PaaS, although other examples do exist (e.g. Docker Swarm). Many flavours of Kubernetes exist, which have been tailored by various vendors (although they all basically do the same thing).

The [Kubernetes docs](https://kubernetes.io/docs/home/) are pretty detailed and well polished. They cover a lot in a digestible manner, although do sweep a number of devils-int-the-detail under the rug.

Kubernetes hides away many of the direct-docker and IaaS worries. On pushing an image to Kubernetes, one can tell Kubernetes what to do with that image by supplying various yamls that describe the infrastructure that Kubernetes should serve. These yamls are _declarative_ and not _imperative_. This means that (unlike the IaaS case), one can tell Kubernetes what one would like (or to be updated), and Kubernetes works out the steps to perform to make that happen.

### Minikube

Minikube is a flavour of Kubernetes well suited for tutorial purposes. It is a simple, single node, instance of kubernetes. Note, that is not production grade. If one wishes to look at single node simple instances of kubernetes, and manage that single node cluster in production, then Rancher/K3S is a production ready flavour of Kubernetes.

There is a simple nuts-and-bolts guide to setting up the fastapi app on Minikube in [k8s](k8s/README.md).