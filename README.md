# Kubernetes-roadmap

Kubernetes is a container orchestration framework commonly de-
ployed and used by researchers and practitioners. It facilitates the
deployment, (auto)scaling and management of container-based ap-
plications through declarative configuration files

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
![image](https://user-images.githubusercontent.com/85927700/213722237-2c79e6f6-e43d-4215-ab74-632408511302.png)

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
- A pod is a group of containers that share storage and network resources.
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


     - Add a liveness probe to our existing pod manifest:
     - Liveness probes are container/application specific, thus we have to define them in the pod manifest individually.
            - After applying this pod manifest (and after deleting the old pod), you can actually view these liveness probes by running the port-forward command from above and navigating to Liveness Probe.
            You can force failures here too!
            
            
```
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
      name: kuard
      livenessProbe:
        httpGet:
          path: /healthy
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3 
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
          
```
          
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
#  what makes containers possible 
kernel provides a feature to run process's in isolation or a sandbox environment and this feature is the namespaces.

# namespace
- namespace gives the process isolation from other processes that are running on
the same host,that is a set of processes is running in a sandbox isolated environment that's the PID namespace.

Then there are a bunch of other namespaces which make it look like and feel like a virtual environment just like running a VM and that's what
isolates one container from another one or one process rather from another one. In a sandbox environment there is a file system
namespace so, each container can have its
own operating system, other namespaces are
each container can have its own network


# OverlayFS
- overlayFS is a union mount filesystem implemenatation for Linux
- Union mount is a combination of multiple directories into one that appears to contain their combined contents.
- ![image](https://user-images.githubusercontent.com/85927700/203813107-8cce257f-d6c6-461a-81a6-0559629c90ae.png)
- The dir1 lower layer contains file1 file2 file5
- The dir2 upper layer contains file3 file4 file5
- when the overlay is mounted on the upper layer the contents of file1 is same as file1 from dir2

  ![image](https://user-images.githubusercontent.com/85927700/203821382-cac577d2-0c81-4c18-8f1a-aad68ca67c74.png)
  ![image](https://user-images.githubusercontent.com/85927700/203822138-076b5f83-4c5b-415a-83d0-56770996064b.png)

- removal of files from overlay
- ![image](https://user-images.githubusercontent.com/85927700/203828480-ead13fa9-627c-42cc-bd58-8fac2c9705a7.png)
- containers are not only light weight to run but tranfer of upadtes is also light weight because of adaptaion of overlayFS 

# DAY-10

# DOCKER STORAGE
# 1. Volumes
# 2. Bind mount
# 3. tmpfs

# DAY-11
# DOCKER-networking
# DAY-10
![image](https://user-images.githubusercontent.com/85927700/206914734-c520a685-17fd-47aa-a4d8-acad8fd1a716.png)
the docker0 is the virtual bridge interface and is the default brige in docker
list the docker networks 
![image](https://user-images.githubusercontent.com/85927700/206921187-edda0b5f-22c7-450c-b0c9-014960cf9040.png)
### Driver is the type of the network
in this case network named bridge is of the type bridge
![image](https://user-images.githubusercontent.com/85927700/206921066-2425016b-d904-4c67-a56f-543ae0fe99f4.png)


creating three containers con1,con2 and con3 makes it go into default network brige, docker automatically created 3 [veth] interfaces and connected it to the virtual docker bridge where it acts as a switch 
![image](https://user-images.githubusercontent.com/85927700/206921305-fb9120b4-c2fe-4151-9c04-b94484de0f30.png)
the link can be seen using the bride link command
![image](https://user-images.githubusercontent.com/85927700/206921341-88f1c140-133b-462f-8ab7-3b3f4404e7b4.png)
![image](https://user-images.githubusercontent.com/85927700/206921342-c828d524-f8a2-4588-8847-a1dc965bd102.png)

 Not only did it create virtual ethernet interfaces,but he also handed out IP addresses, which means it's also running some DHCP.which can be known by using this command.It does this using the host's /etc/resolv.conf which is the dns resolver present in the host machine  and making a copy of it.
 
 ![image](https://user-images.githubusercontent.com/85927700/206921526-5c0d3c08-f20f-45e0-aad7-bbf9b2619d90.png)
 
 Also since the bridge interface acts as a switch the containers connected to them can talk to each other .also conatiners can talk to outer world using the docker0 route interface 
 ![image](https://user-images.githubusercontent.com/85927700/206922364-43d6a9e1-635d-418a-92e3-7a7440ca6d1a.png)
 
 we can ping other containers from con4 conatiner and also to outer world using docker interface route
![image](https://user-images.githubusercontent.com/85927700/206922132-1e1ccbd7-cd76-42d4-beaf-858c07bb30a1.png)

but you cannot connect with the nginx containers con1,2,3 from outer world through their ip's
whyy??? because we didnt expose the ports
so redeploy them ![image](https://user-images.githubusercontent.com/85927700/206923013-6bbc9c9b-ba40-4464-887d-5db152cfdcdb.png)

![image](https://user-images.githubusercontent.com/85927700/206922999-7689fe28-ffc0-4193-9709-3ac2dacf6ff6.png)

## user denfined network
![image](https://user-images.githubusercontent.com/85927700/206923584-f26b510b-9b5d-477d-ab08-d6d23dcbe02e.png)
the user denfined network gives you dns and hence ping using name of the container 
![image](https://user-images.githubusercontent.com/85927700/206923715-7b6e7769-b94d-47ae-a30a-6619ad006665.png)
# mcvlan network
# ipvlanl2
# ipvlanl3
# overlay
# none network
# day-20
![image](https://user-images.githubusercontent.com/85927700/213723462-5bb66386-5926-4339-98dd-4447ce45bc43.png)
![image](https://user-images.githubusercontent.com/85927700/213723653-b45e835c-2867-4dd9-a99d-1c3bf79f2b19.png)
![image](https://user-images.githubusercontent.com/85927700/213725074-96aed8ba-554d-42a9-bd5a-13831084c2bf.png)


# day 21
While ConfigMaps are great for most configuration data, there is certain data
that is extra-sensitive. This can include passwords, security tokens, or other
types of private keys. Collectively, we call this type of data “secrets.”
Kubernetes has native support for storing and handling this data with care.
Secrets enable container images to be created without bundling sensitive data.
This allows containers to remain portable across environments. Secrets are
exposed to pods via explicit declaration in pod manifests and the Kubernetes
API. In this way the Kubernetes secrets API provides an application-centric
mechanism for exposing sensitive configuration information to applications in a
way that’s easy to audit and leverages native OS isolation primitives.

# day 23

A special use case for secrets is to store access credentials for private Docker
registries. Kubernetes supports using images stored on private registries, but
access to those images requires credentials. Private images can be stored across
one or more private registries. This presents a challenge for managing
credentials for each private registry on every possible node in the cluster.
Image pull secrets leverage the secrets API to automate the distribution of
private registry credentials. Image pull secrets are stored just like normal secrets
but are consumed through the spec.imagePullSecrets Pod specification field.
Use the create secret docker-registry to create this special kind of secret:

$ kubectl create secret docker-registry my-image-pull-secret \
--docker-username=<username> \
--docker-password=<password> \
--docker-email=<email-address>
  
Enable access to the private repository by referencing the image pull secret in
the pod manifest file, as shown in kuard-secret-ips.yaml
 ```
apiVersion: v1
kind: Pod
metadata:
  name: kuard-tls
spec:
  containers:
    - name: kuard-tls
      image: gcr.io/kuar-demo/kuard-amd64:1
      imagePullPolicy: Always
      volumeMounts:
      - name: tls-certs
        mountPath: "/tls"
        readOnly: true
  imagePullSecrets:
  - name:  my-image-pull-secret
  volumes:
    - name: tls-certs
      secret:
        secretName: kuard-tls
```

The easiest way to create a secret or a ConfigMap is via kubectl create
secret generic or kubectl create configmap. There are a variety of ways to
specify the data items that go into the secret or ConfigMap. These can be
combined in a single command:
  ```
--from-file=<filename>
Load from the file with the secret data key the same as the filename.
--from-file=<key>=<filename>
Load from the file with the secret data key explicitly specified.
--from-file=<directory>
Load all the files in the specified directory where the filename is an
acceptable key name.
--from-literal=<key>=<value>
Use the specified key/value pair directly.
  ```

# day 30
The Deployment object exists to manage the release of new versions.
Deployments represent deployed applications in a way that transcends any
particular software version of the application. Additionally, Deployments enable
you to easily move from one version of your code to the next version of your
code. This “rollout” process is configurable and careful. It waits for a user-
configurable amount of time between upgrading individual Pods. It also uses
health checks to ensure that the new version of the application is operating
correctly, and stops the deployment if too many failures occur.
Using Deployments you can simply and reliably roll out new software versions
without downtime or errors. The actual mechanics of the software rollout
performed by a Deployment is controlled by a Deployment controller that runs
in the Kubernetes cluster itself. This means you can let a Deployment proceed
unattended and it will still operate correctly and safely. This makes it easy to
integrate Deployments with numerous continuous delivery tools and services.
  
Deployment Strategies
When it comes time to change the version of software implementing your
service, a Kubernetes Deployment supports two different rollout strategies:
# Recreate
# RollingUpdate

learn deployments using commands:
```
$ kubectl run nginx --image=nginx:1.7.12

view this Deployment object by running:
$ kubectl get deployments nginx

You can see the label selector by looking at the Deployment object:
$ kubectl get deployments nginx \
-o jsonpath --template {.spec.selector.matchLabels}

We can use this in a label selector query across ReplicaSets to
find that specific ReplicaSet:
$ kubectl get replicasets --selector=app=nginx

see the relationship between a Deployment and a ReplicaSet in action.
We can resize the Deployment using the imperative scale command:
$ kubectl scale deployments nginx --replicas=2

Now if we list that ReplicaSet again, we should see:
$ kubectl get replicasets --selector=app=nginx

let’s try the opposite, scaling the ReplicaSet:
$ kubectl scale replicasets <your-replicaset-id> --replicas=1

Now get that ReplicaSet again:
$ kubectl get replicasets --selector=app=nginx
```
# day 31
# Integrating Storage Solutions and Kubernetes 
## 1.Services Without Selectors
When we first introduced services, we talked at length about label queries and
how they were used to identify the dynamic set of Pods that were the backends
for a particular service. With external services, however, there is no such label
query. Instead, you generally have a DNS name that points to the specific server
running the database. For our example, let’s assume that this server is named
database.company.com. To import this external database service into
Kubernetes, we start by creating a service without a Pod selector that references
the DNS name of the database server  dns-service.yaml
  ```
kind: Service
apiVersion: v1
metadata:
name: external-database
spec:
type: ExternalName
externalName: "database.company.com
  ```
When a typical Kubernetes service is created, an IP address is also created and
the Kubernetes DNS service is populated with an A record that points to that IP
address. When you create a service of type ExternalName, the Kubernetes DNS
service is instead populated with a CNAME record that points to the external
name you specified (database.company.com in this case). When an application
in the cluster does a DNS lookup for the hostname external-
database.svc.default.cluster, the DNS protocol aliases that name to
“database.company.com.” This then resolves to the IP address of your external
database server. In this way, all containers in Kubernetes believe that they are
talking to a service that is backed with other containers, when in fact they are
being redirected to the external database.
Note that this is not restricted to databases you are running on your own
infrastructure. Many cloud databases and other services provide you with a DNS
name to use when accessing the database (e.g., my-
database.databases.cloudprovider.com). You can use this DNS name as the
externalName. This imports the cloud-provided database into the namespace of
your Kubernetes cluster.Sometimes, however, you don’t have a DNS address for an external database
service, just an IP address. In such cases, it is still possible to import this server
as a Kubernetes service, but the operation is a little different. First, you create a
Service without a label selector, but also without the ExternalName type we
used before external-ip-service.yaml
  ```
kind: Service
apiVersion: v1
metadata:
name: external-ip-database
  ```
At this point, Kubernetes will allocate a virtual IP address for this service and
populate an A record for it. However, because there is no selector for the service,
there will be no endpoints populated for the load balancer to redirect traffic to.
Given that this is an external service, the user is responsible for populating the
endpoints manually with an Endpoints resource 
 external-ip-endpoints.yaml
  ```
kind: Endpoints
apiVersion: v1
metadata:
name: external-ip-database
subsets:
- addresses:
- ip: 192.168.0.1
ports:
- port: 3306
  ```
If you have more than one IP address for redundancy, you can repeat them in the
addresses array. Once the endpoints are populated, the load balancer will start
redirecting traffic from your Kubernetes service to the IP address endpoint(s).
  
# 2.Running a Singleton
 1.Running a MySQL Singleton
  To do this, we are going to create three basic objects:
- A persistent volume to manage the lifespan of the on-disk storage
independently from the lifespan of the running MySQL application
- A MySQL Pod that will run the MySQL application
- A service that will expose this Pod to other containers in the cluster
  
  if your needs are
simple and you can survive limited downtime in the face of a machine failure or
a need to upgrade the database software, a reliable singleton may be the right
approach to storage for your application.
  
 2.Dynamic Volume Provisioning 
  
# 3.Kubernetes-Native Storage with StatefulSets

Properties of StatefulSets
  
1.StatefulSets are replicated groups of Pods similar to ReplicaSets, but unlike a
ReplicaSet, they have certain unique properties:
Each replica gets a persistent hostname with a unique index (e.g.,
database-0, database-1, etc.).
  
2.Each replica is created in order from lowest to highest index, and creation
will block until the Pod at the previous index is healthy and available. This
also applies to scaling up.
  
3.When deleted, each replica will be deleted in order from highest to lowest.
This also applies to scaling down the number of replicas.
  
  
  
# KUBERNETES  CHEATSHEET
  
    kubectl get pod: Get information about all running pods
    kubectl describe pod <pod>: Describe one pod
    kubectl expose pod <pod> --port=444 --name=frontend: Expose the port of a pod (creates a new service)
    kubectl port-forward <pod> 8080: Port forward the exposed pod port to your local machine
    kubectl attach <podname> -i: Attach to the pod
    kubectl exec <pod> -- command: Execute a command on the pod
    kubectl label pods <pod> mylabel=NotALabel: Add a new label to a pod
    kubectl run -i --tty busybox --image=busybox --restart=Never -- sh: Run a shell in a pod - very useful for debugging, connect to malfunctioning pod.
    kubectl get deployments: Get information on current deployments
    kubectl get rs: Get information about the replica sets
    kubectl get pods --show-labels: get pods, and also show labels attached to those pods
    kubectl rollout status deployment/helloworld: Get deployment status
    kubectl set image deployment/helloworld k8s-demo=k8s-demo:2: Run k8s-demo with the image label version 2
    kubectl edit deployment/helloworld: Edit the deployment object
    kubectl rollout status deployment/helloworld: Get the status of the rollout
    kubectl rollout history deployment/helloworld: Get the rollout history
    kubectl rollout undo deployment/helloworld: Rollback to previous version
    kubectl rollout undo deployment/helloworld --to-revision=n: Rollback to any version version

     
 # Kubenetes architecture

# A worker node should have:
     
     1. conatiner-runtime
  
     2. Kube Proxy
     
     3. Kubelet:
![image](https://user-images.githubusercontent.com/85927700/217565214-c1ab627f-0d10-4dba-9918-ccc278df7038.png)
Application pods have containers running inside a container runtime needs to be installed on every node but the process that actually schedules those can those pods and the containers underneath is kubelet which is a process of kubernetes itself unlike container runtime that has interface with both
container runtime and the machine the node itself because at the end of the day kubelet is responsible for taking that configuration and actually running
a pod or starting a pod with a container inside and then assigning resources from that node to the container like CPU RAM and storage resources so usually kubernetes cluster is made up of multiple nodes which also must have container runtime and kubelet services installed.
![image](https://user-images.githubusercontent.com/85927700/217567512-76523b03-49ff-4abe-8d35-edabea3723f3.png)     
     
# A master  have 4 processes run everytime:
     
![image](https://user-images.githubusercontent.com/85927700/217569021-6476531f-40ff-4407-9de3-c7abab7f8951.png)     
     1. Kube API
     
![image](https://user-images.githubusercontent.com/85927700/217569513-4c8c281a-b972-4118-988f-9d1218c61daa.png)
![image](https://user-images.githubusercontent.com/85927700/217569696-b741a328-abf5-4a33-b3a4-dda61acbfc6f.png)     
     
     2. Scheduler
 ![image](https://user-images.githubusercontent.com/85927700/217569866-487f3c4d-844f-4d9a-b799-e86c5986eb8b.png)
 ![image](https://user-images.githubusercontent.com/85927700/217570162-fc84d535-8841-4d13-ac53-895366f1aab3.png)     
    
     3. Controller
    
 ![image](https://user-images.githubusercontent.com/85927700/217570770-07c598f6-63a8-44cf-a1ba-68d842d0674b.png)
 ![image](https://user-images.githubusercontent.com/85927700/217570964-f1280a8b-737c-4cb3-a0c2-21ccb5e59bb7.png) 
     
     4. etcd
     
  ![image](https://user-images.githubusercontent.com/85927700/217571225-ed22ad13-a6e5-463d-8485-75bf695186c6.png)
  ![image](https://user-images.githubusercontent.com/85927700/217571480-549c0787-3ed8-4689-b789-64bcff3cfe6c.png)


# day 35
# Kubernetes services 

 ![image](https://user-images.githubusercontent.com/85927700/217620186-d77885e7-8555-493d-b7bb-87bdf89ebe84.png)

     
if a pod dies then the ip address of the pods goes away with it hence we assign new ip so referring pod ip is unstable and hence we get stable ip by using 
 service.
 Service also provides load balancing among pods
     
 ![image](https://user-images.githubusercontent.com/85927700/217619643-bdd3158e-86c1-44db-8627-f9b5c28ecfd6.png)
 
  types of services offered are:-
 #  1.ClusterIP:
     
 ![image](https://user-images.githubusercontent.com/85927700/217621043-65e86cd8-e0eb-4a56-8e18-846ba5d994ba.png)
 ![image](https://user-images.githubusercontent.com/85927700/217628665-614a567b-d72f-4ae6-8fe8-ec63a1300cf2.png)
     
  how does service know which pods to forward to and port?It is done by labels selectors and target port
     
![image](https://user-images.githubusercontent.com/85927700/217629566-2cfa692d-e85c-4f94-9369-cf6d310ae37d.png)
     
 below image shows target port to match a pod with specific port
     
![image](https://user-images.githubusercontent.com/85927700/217630424-bc8a04d5-1c0e-4d8e-9f84-65f642848211.png)
     
 Overall concept is put into one and shown as below
     
![image](https://user-images.githubusercontent.com/85927700/217630956-c2b70fd5-d8b5-4909-bd96-1cc689015875.png)
     
     
# Target port vs port of service
     
![image](https://user-images.githubusercontent.com/85927700/217631483-4e961cb0-c6d5-4064-8139-80f523ad89a8.png)
     
# how in cluster  monogoDB pods and application pods communicate with services
 ![image](https://user-images.githubusercontent.com/85927700/217632209-c84f0920-cfe9-46b2-95fb-8b5154f56754.png)

 ![image](https://user-images.githubusercontent.com/85927700/217632310-a0ccb970-efaa-46d5-9a3a-0de6f1cb6d42.png)

# Serving multiple end points by a service(by using mulltiports)
 
![image](https://user-images.githubusercontent.com/85927700/217633278-ccec1f92-0255-4519-be6d-bc533e2adb4b.png)
     
 NOTE:important point is when it is multi port serving service then we name the ports as shown below
     
![image](https://user-images.githubusercontent.com/85927700/217633603-47a2088e-e57f-4f3c-9a04-ae74dd907a1d.png)

# 2. Headless service 
when application wants to talk to a specific pod 
![image](https://user-images.githubusercontent.com/85927700/217634823-3d8ee730-15b8-411a-8839-b1be28ac2ea9.png)
 
how to find the ip address of the specific pod which u want?

![image](https://user-images.githubusercontent.com/85927700/217636635-7a6e3193-27bf-41d9-ab6c-dda9bac237c1.png)
 
 So a headless service can be written by making the clusterIP field to none
     
![image](https://user-images.githubusercontent.com/85927700/217637077-7de8b497-d232-4f66-ae1c-faf47f1c9b79.png)
 
![image](https://user-images.githubusercontent.com/85927700/217637592-abb92ab4-a252-4f9d-95e9-62315452def4.png)
     
# 3. NodePort service 
![image](https://user-images.githubusercontent.com/85927700/217637795-13a7fb69-f76a-4046-aac3-6c47188bac8a.png)
![image](https://user-images.githubusercontent.com/85927700/217864931-62e5a8f3-4b80-40c8-8298-1b93538fe28e.png)
![image](https://user-images.githubusercontent.com/85927700/217865282-6b9e9b0c-75c7-408a-adf8-9402bdfa73ff.png)
however clusterIP service is automatically created
![image](https://user-images.githubusercontent.com/85927700/217865630-b9bd7034-a63e-4764-8731-31601459efa6.png)
its not very secure 
![image](https://user-images.githubusercontent.com/85927700/217866356-012a4816-24c5-48bf-a919-92709c4f1f66.png)

# 4. LoadBalancer service     
![image](https://user-images.githubusercontent.com/85927700/217866817-2d24f36b-96fb-4ba0-b8d3-49b36067c021.png)
![image](https://user-images.githubusercontent.com/85927700/217866908-4bd3b850-4d4d-4870-ae7b-edec333996e9.png)
 
In reallife setup it is preferred to use
![image](https://user-images.githubusercontent.com/85927700/217867592-b2f9d8f7-adc1-4226-affb-b8b06dab2b21.png)




# day 50
# hpa working


HPA is a form of autoscaling that increases or decreases the number of pods in a replication controller, deployment, replica set, or stateful set based on CPU utilization—the scaling is horizontal because it affects the number of instances rather than the resources allocated to a single container.

HPA can make scaling decisions based on custom or externally provided metrics and works automatically after initial configuration. All you need to do is define the MIN and MAX number of replicas.

Once configured, the Horizontal Pod Autoscaler controller is in charge of checking the metrics and then scaling your replicas up or down accordingly. By default, HPA checks metrics every 15 seconds.

To check metrics, HPA depends on another Kubernetes resource known as the Metrics Server. The Metrics Server provides standard resource usage measurement data by capturing data from “kubernetes.summary_api” such as CPU and memory usage for nodes and pods. It can also provide access to custom metrics (that can be collected from an external source) like the number of active sessions on a load balancer indicating traffic volume.

In simple terms, HPA works in a “check, update, check again” style loop. Here’s how each of the steps in that loop work.
   ![image](https://user-images.githubusercontent.com/85927700/224563908-d926bd39-ea8e-49dc-9508-45aec9d84a10.png)
    1.HPA continuously monitors the metrics server for resource usage.
    2.Based on the collected resource usage, HPA will calculate the desired number of replicas required.
    3.Then, HPA decides to scale up the application to the desired number of replicas.
    4.Finally, HPA changes the desired number of replicas.
    5.Since HPA is continuously monitoring, the process repeats from Step 1.

 





   


    
 
     
     










