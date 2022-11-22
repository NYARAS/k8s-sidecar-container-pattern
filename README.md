# Multi-Container Pod Design Patterns in Kubernetes

Kubernetes is an open-source major player container orchestration engine for automating deployments, scaling and and management of containerized applications.

A pod is the basic building block of kubernetes application. Pods encapsulate containers. A pod may have one or more containers, storage, IP Addresses and some other options that govern how containers should run inside the pod.

A pod that has one container is called a single container pod. It's the most common kubernetes use case. A pod that has multiple co-related containers is called multi-container pod. You don't always need multi-container pods. When do you need to use them is the question. Some of the cases where you can may need to use them are:
- When the containers have the exact lifecycle or when the containers must run on the same node. A scenarion where you have a helper process that needs to be located and managed on the same node as the primary container.
- For simpler communication between containers in the pod. These containers can communicate through shared volumes (writing to a shared file or directory) and through inter-process communication (semaphores or shared memory)
When the containers have the exact same lifecycle, or when the containers must run on the same node. The most common scenario is that you have a helper process that needs to be located and managed on the same node as the primary container.

There are three common design patterns and use-cases for combining multiple containers into a single pod:

- Sidecar pattern
- Adapter pattern
- Ambassador pattern

## Sidecar Pattern
This pattern consists of a main application i.e. a web application plus a helper container with a responsibilty that is essential to the web application but it's not necessarily part of the application itself. The most common sidecar containers are logging utilities, sync services, watchers, and monitoring agents. It does not make sense if a logging container is running while the application itself isn't running, so we create a multi-container pod that has the main application and the sidecar container

### Example
Let deploy a simple pod to understand this pattern. A pod that has main and sidecar containers.

The main container is a simple nginx application serving on the port 80 that takes the index.html from the volume mount location. The Sidecar container uses busybox image writes the current date to a log file every five seconds. In practice, your sidecar is likely to be a log collection container that uploads to external storage.

For you to apply this example, you need to to install [Minikube](https://minikube.sigs.k8s.io/docs/start/) as a prerequisite
Apply the manifest
```bash
 kubectl apply -f sidecar-pod.yaml
```
Once the pod is running, connect to the sidecar pod:
```bash
kubectl exec -it  sidecar-container-example -c main-container -- /bin/sh
```
Install `curl` on the sidecar
```bash
apt-get update && apt-get install -y curl
```

Access the log file(index.html) via the sidecar

```bash
curl localhost
```
