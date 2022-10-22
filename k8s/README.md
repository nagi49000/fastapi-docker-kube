# Deploying a simple app to Kubernetes

### Setting up Minikube

Setting up will require installation and set up of minikube and kubectl, which is detailed [here](https://www.linuxtechi.com/how-to-install-minikube-on-ubuntu/).

After initial set up, it is also worth switching on the ingress add on
```
minikube addons enable ingress
```

### Deploying to Minikube

From the [services guide](../services/README.md), one can build a production docker image. As a quick way round of building the image locally, and pushing to minikube's container registry (which is what one would normally do with a kubernetes cluster), one can build the image directly in minikube's image registry.

```
# in fastapi-docker-kube/services/
minikube image build -f Dockerfile-prod . -t web
minikube image ls
```

Minikube can be instructed to handle and serve the image via kubernetes yamls
```
# in fastapi-docker-kube/k8s/
kubectl apply -f web-deployment.yaml
kubectl apply -f web-service.yaml
kubectl apply -f web-ingress.yaml
```

One should then see the deployment in kubernetes using
```
kubectl get all
```

And the ingress
```
kubectl get ingress
```

To debug any problems of the deployment coming up, one can have a look at
```
kubectl get events
```

If all is well, the deployment will have spawned pods, the service will be linked up to the pod, and the ingress linked up to the service. In which case,

```
curl $(minikube ip)/hello_world
```

should return `{"message":"Hello World"}`. The IP returned by `$(minikube ip)` can be put into the web browser and now used as an http address on port 80 for exploring the app, in the same way that http://localhost:8080 could be used in the pure docker example in services.
