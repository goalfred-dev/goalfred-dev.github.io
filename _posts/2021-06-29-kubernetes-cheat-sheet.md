---
layout: post
title: "Kubernetes Cheat Sheet"
author: alfred
categories: [k8s, cheat sheet]
featured: false
hidden: false
toc: true
---

Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

<!--more-->

### References

[Kubernetes](https://kubernetes.io/)
[Helm](https://helm.sh/)
[minikube](https://minikube.sigs.k8s.io/docs/)

### Articles

[Take the Helm with Kubernetes on Windows](https://medium.com/@JockDaRock/take-the-helm-with-kubernetes-on-windows-c2cd4373104b)
[Minikube on Windows 10 with Hyper-V](https://medium.com/@JockDaRock/minikube-on-windows-10-with-hyper-v-6ef0f4dc158c)

### Helm Commands

```sh
# Install Helm
choco install kubernetes-helm

# Init client only
helm init --client-only

# Show current context
kubectl config current-context

# List releases
helm ls --tiller-namespace entcs

# Upgrade release
helm upgrade \
  <release_name> <path_to_helm_chart_directory> \
  --install \
  --namespace entcs \
# ...

# Delete release
helm delete --tiller-namespace entcs <release> --purge
```

### Minikube Commands

```sh
minikube start --vm-driver hyperv

minikube stop
minikube start

kubectl get pods -n kube-system

minikube dashboard
```
