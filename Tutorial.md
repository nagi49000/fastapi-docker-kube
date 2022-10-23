# Tutorial on serving in containers

This tutorial covers the various ways one might host/serve a service from simplest dev to production.

## Development and Docker/Podman

There is a simple nuts-and-bolts guide to sandboxing and containerizing in [services](services/README.md). For development, and building containers, a few aspects are used

### Development sandbox

The app is in Python. One can create a sandbox/environment by using a conda environment. Conda is not Python specific, and can be used for other languages (e.g. R). Conda's job is to create reproducible sandbox environments, including executable binaries (which virtualenv and venv cannot). This environment is completely defined by a yaml file. This gives a way for multiple developers to work in the same environment, and also a way to reproduce that environment in a container.

### Docker

Docker can be used for containerization; building a portable image from which one can run the app. This can be done directly from the dockerfiles, or orchestrated using the docker-compose file. Examples are available [here](services/README.md#docker)

### Podman

Podman (POD MANager) is an alternative to Docker. It improves on a number of aspects
- a daemon/service no longer needs to be running in the background
- images and containers are encapsulated to the user, offering better security (e.g. as a user, running `podman images` will only show that user's images)
- allow for multiple containers to be run in a single pod
- natively run containers as rootless (docker introduces on-system security vulnerabilities for running containers)

Docker images and Podman images are completeley compatible; so building an image via podman or docker, and then pushing to an image repository, should not introduce any docker/podman compatibility worries. Generally speaking, all docker commands can be run wth podman (by interchanging 'docker' on the command line for 'podman').

### Infrastructure as a Service (IaaS)

The development chain described above can be used to host a service in production. One can create a virtual machine on one's favourite VMWare or cloud provider, and run a containerized service on that virtual machine. This provisioning of a virtual machine is called 'Infrastructure as a Service', (IaaS). Whilst it works, this paradigm has some limitations
- for new services, one has to go through the work of creating the infrastructure
- the scope, number of containers, and resource of the services are limited by the created infrastructure
- any kind of scaling has to be done 'by hand'
- failure of virtual machines must be handled directly
- any kind of upgrades can result in services being temporarily unavailable whilst containers are spun down and spun up

Ideally, one would like a system that abstracts away the infrastructure worries, and only worries about serving images in containers; some kind of image-and-container orchestration. One might also like to have a picture of just pushing an image to that system, and the system perform the orchestration (similar to how one might push an image repository, and then the repository manages all the hosting).

This is precisely the reasoning for introducing a 'Platform as a Service' (PaaS). It is a system where one can push images, and then the system handles many of the intricacies of orchestrating the serving of containers. The system can be clustered over multiple virtual machines, meaning the developer pushing the image to PaaS does not need to worry about individual virtual machines; only about how to serve containers based on the image.

## Hosting on Kubernetes

Kubernetes (K8S) is the classic example of a PaaS, although other examples do exist (e.g. Docker Swarm). Many flavours of Kubernetes exist, which have been tailored by various vendors (although they all basically do the same thing).

The [Kubernetes docs](https://kubernetes.io/docs/home/) are pretty detailed and well polished. They cover a lot in a digestible manner, although do sweep a number of devils-int-the-detail under the rug.

Kubernetes hides away many of the direct-docker and IaaS worries. On pushing an image to Kubernetes, one can tell Kubernetes what to do with that image by supplying various yaml files (called 'manifests') that describe the infrastructure that Kubernetes should serve. These manifests are _declarative_ and not _imperative_. This means that (unlike the IaaS case), one can tell Kubernetes what one would like (or to be updated), and Kubernetes works out the steps to perform to make that happen.

### Minikube and kubectl

Minikube is a flavour of Kubernetes well suited for tutorial purposes. It is a simple, single node, instance of kubernetes. Note, that is not production grade. If one wishes to look at single node simple instances of kubernetes, and manage that single node cluster in production, then Rancher/K3S is a production ready flavour of Kubernetes.

There is a simple nuts-and-bolts guide to setting up the fastapi app on Minikube in [k8s](k8s/README.md).

For pedagogy (note, this usually generates fairly poor manifests), one can see the link from docker to kubernetes using the command line tool `kompose`. The deployment and service yamls in [k8s](k8s) were generated from running `kompose` on the [docker-compose.yaml](services/docker-compose.yaml). In this sense, the manifest files contain as much information as the docker-compose.yaml for running a docker container. The reader should look over the docker-compose.yaml, and see how how it compares to the manifest files, noting that
- k8s/web-deployment.yaml describes how to stand up a container that uses a specified image with open ports
- k8s/web-service.yaml describes how to forward ports from that container WITHIN the kubernetes cluster/network
- k8s/web-ingress.yaml describes how to expose that service OUTSIDE the kubernetes network/cluster

This may seem like a lot of boiler plate and expansion over the original docker-compose.yaml, but it offers a substantial amount of flexibility and resilience over a service stood up using docker or podman.
- The 'deployment' (treated as immortal) acts as a template that spawns 'pods' (which are ephemeral). If a pod dies, it is cleared down and another pod launched from the deployment.
- The 'service' (treated as immortal) can link to any pods with the relevant selector, permitting 'horizontal scaling'; the same service can sit in front of multiple pods from the same deployment.
- The 'deployment' can handle how, on a config or image change, a new pod is spun up to replace the old pod, and handle the old pod if the new pod fails (this gives service resilience to users)
- The 'ingress' (which is really just Nginx) can cover external connections, TLS termination, routing etc

Also worth noting, which reflects the PaaS nature, is that with Kubernetes, the only operations required to host and orchestrate an app, are
- Providing a docker image
- Providing manifests to say how to orchestrate that image

There is nothing about the underlying hardware or VMs involved. The docker image (as a dockerfile) and the manifests (as yaml files) can be entirely handled as code (fitting in with an Infrastructure-as-Code, IaC, paradigm).

Precisely how all the machinery in Kubernetes can be exposed as manifests is a massive topic. The typical things to remember/artefacts that crop up are that:
- Kubernetes handles multiple pods, where each pod can run multiple containers. These are typically described by a Deployment or DeploymentConfig.
- In-pod networking between containers in the same pod is completely open.
- Inter-pod networking is handled by a Kubernetes Service.
- Networking out of the Kubernetes cluster is handled by an Ingress or Route attaching onto a Service.

The standard way of communicating with a Kubernetes cluster is via command line using `kubectl`. Various flavours of Kubernetes have their own wrappers around kubectl (e.g. eksctl for AWS and oc for openshift).

### Minikube and helm

Using `kubectl` to deploy manifests can start to become cumbersome. There are issues with
- multiple manifest files that are installed individually
- tweaking manifest files for individual deployments
- managing dependencies of manifests, where another deployment needs to be in place to support the requested deployment

To this end, one can use helm, which is a 'package manager for Kubernetes'. This extends the IaC idea of manifest files by bundling into a package, so that (a) the package can be configured and (b) any dependencies can be listed in in the helm chart for install at `helm install` time. Just like software packages, helm charts can be stored in repositories, and repositories referenced (in the same way that apt or yum can reference repositories of software packages).

There is a simple nuts-and-bolts guide to a helm install of a fastapi app on Minikube in [helm](helm/README.md). The files within the [helm chart](helm/simple-app) are basically the individual manifest files bundled into a single folder, and made into templates (where template values can be inserted via jinja2).

As some background, the manifests used in [k8s](k8s/README.md) were copied into a new, blank helm chart. The new blank helm chart was created with `helm create new` on the command line, and all the template files specific to the generated new application (which happened to be an nginx application) were removed, and the manifest files from [k8s](k8s/README.md) copied into the 'templates' directory in the helm chart, and templatised.

Using helm becomes relatively simple once one realises that is is just a package manager for Kubernetes deployments. The onus then moves to understanding Kubernetes manifest files. Understanding how Kubernetes infrastructure is reflected (and can be configured) via manifests is not straightforward.