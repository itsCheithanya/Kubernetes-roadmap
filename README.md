# Kubernetes-roadmap
# DAY-1
# Dockers

- Containers and Container image(A container image is a binary package that encapsulates all of
the files necessary to run an application inside of an OS container)
- Docker image format
- Docker container runtime(The Docker Container Runtime
Kubernetes provides an API for describing an application deployment, but relies
on a container runtime to set up an application container using the container-
specific APIs native to the target OS. On a Linux system that means configuring
cgroups and namespaces.
The default container runtime used by Kubernetes is Docker. Docker provides an
API for creating application containers on Linux and Windows systems.)
- A Dockerfile can be used to automate the creation of a Docker container image.
- Creating and Running Containers

# DAY-2
- kubectl(use it to create objects and interact with the Kubernetes API.)

- Viewing Kubernetes API Objects(Everything contained in Kubernetes is represented by a RESTful resource)
```
EXAMPLE:Kubernetes object exists at a unique HTTP path; for example, https://your-
k8s.com/api/v1/namespaces/default/pods/my-pod leads to the representation of a
pod in the default namespace named my-pod. The kubectl command makes
HTTP requests to these URLs to access the Kubernetes objects that reside at
these paths.
The command get helps to retreive it from the specified url over http using kubectl
```
```
kubectl get <resource-name>
```
- Creating, Updating, and Destroying Kubernetes
Objects
```
Objects in the Kubernetes API are represented as JSON or YAML files. These
files are either returned by the server in response to a query or posted to the
server as part of an API request. You can use these YAML or JSON files to
create, update, or delete objects on the Kubernetes server.

$ kubectl apply -f object.yaml
$ kubectl delete <resource-name> <obj-name>

```
# DAY-3
# Pods
- Pods in Kubernetes
A Pod represents a collection of application containers and volumes running in
the same execution environment. 
- Running Containers vs Pods
```
Running my-image by first fetching it from the GCR using the following Docker command:
$ docker run -d --name  my-image \
--publish 8080:8080 \
gcr.io/my-image-demo/my-image-amd64:1
```
```
Creating and running a Pod is via the imperative kubectl run command, to run my-image server, use:
$ kubectl run my-image --image=gcr.io/my-image-demo/my-image-amd64:1
```
- Pod manifest(Similar result can be achieved by instead writing to a file named
kuard-pod.yaml and then using kubectl commands to load that manifest to
Kubernetes.)
```
apiVersion: v1
kind: Pod
metadata:
name: my-image
spec:
containers:
- image: gcr.io/my-image-demo/my-image-amd64:1
name: my-image
ports:
- containerPort: 8080
name: http
protocol: TCP
```
Use this to launch a single instance of my-image
```
$ kubectl apply -f my-image.yaml
```

# DAY-4
# POD HEALTH Checks
- Liveness Probe

Liveness health checks run application-specific logic (e.g., loading a web page)
to verify that the application is not just still running, but is functioning properly.
Once a pod is up and running,the way to confirm that it is
actually healthy and shouldn’t be restarted is by liveness probe
- Readiness Probe

Readiness describes when a container is ready to serve user
requests. 
- tcpSocket health checks
- exec probe
