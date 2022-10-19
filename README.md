# Kubernetes demo

This repository contains some files to demonstrate how to deploy a simple application to Kubernetes.  

## Pre-requisites

- A working kubernetes cluster
- kubectl
- helm

## Echo server

The first application is a simple echo server.  
It can be deployed with :

```
cd echoserver
kubectl create ns echoserver
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

You can then test it with :

```
kubectl port-forward svc/echo-service 8080:80
curl 127.0.0.1:8080
```

You can scale the deployment and observe the round-robin behaviour of the service :

```
kubectl scale deploy echo-deployment --replicas=2
```

You can try the service with a NodePort (not recommended in production) :

´´´
kubectl delete -f service.yaml
kubectl create -f service-nodeport.yaml
´´´

If you have a firewall, you need to open port 30007.  
For instance with gcloud:

```
gcloud config set project <project>
gcloud compute firewall-rules create echoserver --allow tcp:30007
```

To cleanup :

```
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
kubectl create -f service-nodeport.yaml
```

## Pacman (with helm)

To demonstrate a deployment with helm, we'll use a pacman application chart.  

```
helm repo add pacman https://shuguet.github.io/pacman/
helm install pacman pacman/pacman -n pacman --create-namespace
```

You can test with :

```
kubectl port-forward svc/pacman 8080:80
```

## With an ingress

If you want to test with an ingress (might incur additional costs for the load balancer) :

As we are using GKE, we first need a global IP address :

```
gcloud compute addresses create pacman-ip --global
```

We can then deploy the application, with the updated values :

```
helm install pacman pacman/pacman -n pacman --create-namespace -f values-ingress.yaml
```

