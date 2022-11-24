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

# DAY-4
# Resource metrics

1.  Resource requests specify the minimum amount of a resource required to run the
application. 
2. Resource limits specify the maximum amount of a resource that an
application can consume.

NOTE:
- Resources are requested per container, not per Pod. 
- The total resources requested by the Pod is
the sum of all resources requested by all containers in the Pod. 
- When you establish limits on a container, the kernel is configured to ensure that
consumption cannot exceed these limits

# Persisting Data with Volumes
To add a volume to a Pod manifest, there are two new stanzas to add to our
configuration. 
1. The first is a new **spec.volumes** section(This array defines all of
the volumes that may be accessed by containers in the Pod manifest)
2. The second addition is the **volumeMounts** array in the
container definition. This array defines the volumes that are mounted into a
particular container, and the path where each volume should be mounted.

Note
that two different containers in a Pod can mount the same volume at different
mount paths.

# DAY-5
# Labels and Annotations
# Resource grouping
- Label Selectors
Label selectors are used to filter Kubernetes objects based on a set of labels.
Selectors use a simple Boolean language. They are used both by end users (via
tools like kubectl) and by different types of objects (such as how ReplicaSet
relates to its Pods).
Each deployment (via a ReplicaSet) creates a set of Pods using the labels
specified in the template embedded in the deployment. This is configured by the
kubectl run command.
- Annotations are used in various places in Kubernetes, with the primary use case
being rolling deployments. During rolling deployments, annotations are used to
track rollout status and provide the necessary information required to roll back a
deployment to a previous state.
Users should avoid using the Kubernetes API server as a general-purpose
database. Annotations are good for small bits of data that are highly associated
with a specific resource. If you want to store data in Kubernetes but you don’t
have an obvious object to associate it with, consider storing that data in some
other, more appropriate database

# DAY-6
# Service Discovery

- The Domain Name System (DNS) is the traditional system of service discovery
on the internet. DNS is designed for relatively stable name resolution with wide
and efficient caching. 
- Many systems (for example, Java, by default) look up a name in
DNS directly and never re-resolve. This can lead to clients caching stale
mappings and talking to the wrong IP. Even with short TTLs and well-behaved
clients, there is a natural delay between when a name resolution changes and the
client notices. There are natural limits to the amount and type of information that
can be returned in a typical DNS query, too. Things start to break past 20–30 A
records for a single name. SRV records solve some problems but are often very
hard to use. Finally, the way that clients handle multiple IPs in a DNS record is
usually to take the first IP address and rely on the DNS server to randomize or
round-robin the order of records. This is no substitute for more purpose-built
load balancing.
# DAY-7
# OverlayFS
- overlayFS is a union mount filesystem implemenatation for Linux
- Union mount is a combination of multiple directories into one that appears to contain their combined contents.
- ![image](https://user-images.githubusercontent.com/85927700/203813107-8cce257f-d6c6-461a-81a6-0559629c90ae.png)
- The dir1 lower layer contains file1 file2 file5
- The dir2 upper layer contains file3 file4 file5
- when the overlay is mounted on the upper layer the contents of file1 is same as file1 from dir2

  ![image](https://user-images.githubusercontent.com/85927700/203821382-cac577d2-0c81-4c18-8f1a-aad68ca67c74.png)
  ![image](https://user-images.githubusercontent.com/85927700/203822138-076b5f83-4c5b-415a-83d0-56770996064b.png)

