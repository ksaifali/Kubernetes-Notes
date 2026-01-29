# Kubernetes

### What is Kubernetes
Kubernetes is an open-source container management also known as container orchestration tool for automating software deployment, scaling, and management.

### What is Container Orchestration
Orchestration enables developers to easily build containerized applications and services, as well as scale, schedule and monitor those containers.

### Nodes
Nodes are physical or virtual machine in which kubernetes is installed.

### Cluster
Cluster is group of nodes, if one node fails we have our application accessible from other nodes.

## Master
Master is other node in which kubernetes is installed and it manages other nodes

### Components
- API Server
- etcd
- kubelet
- Container Runtime
- Controller
- Scheduler
Control Plane Components
These components manage the cluster's overall state and operations, ensuring the current state matches the desired state. 
API Server (kube-apiserver): This is the central management entity and the front end of the Kubernetes control plane. It exposes the Kubernetes API and is responsible for processing, authenticating, and validating all API requests. It serves as the hub for all communication between other components.
- etcd: A consistent and highly-available key-value store used as the backing store for all cluster data. It stores the entire configuration and state of the cluster, and all other components interact with it via the API Server.
- Scheduler (kube-scheduler): This component watches for newly created Pods that have no node assigned and selects an appropriate node for them to run on. It considers resource requirements, hardware/software/policy constraints, and other factors to make the optimal placement decision.
- Controller Manager (kube-controller-manager): This runs various controller processes in the background, which are responsible for maintaining the desired state of the cluster.

Examples include the Node Controller (notices and responds when nodes go down), the Job Controller (watches for Jobs that represent a one-off task), and the Replication Controller (ensures the desired number of Pod replicas are running). 
### Master V/S Worker node
Master has kube-apiserver installed and nodes have kubelet installed (Kubelet is an agent which make sure nodes are healthy) which provides information to master which stores information in etcd (key value storage)
Worker Node Components
These components run on each individual node (worker machine) and provide the execution environment for your containerized applications.

- Kubelet: An agent that runs on every node in the cluster. It ensures that containers are running in a Pod as expected. The Kubelet takes the Pod specifications provided by the API server and manages the container lifecycle, interacting with the container runtime to start or stop containers.
- Container Runtime: The underlying software that is responsible for running containers. Kubernetes supports several runtimes through the Container Runtime Interface (CRI), such as containerd, CRI-O, and Docker. This component pulls images and runs the actual application containers.

### Minikube
Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. 
- Download kubectl and minikube to start using minikube

### PODS
A Pod is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers.

### Replication Controllers
Replication Controller used for high availability, it ensures that a specified number of pod replicas are running at any one time. It also helps load balancing and scaling when demand increases.

###
ReplicaSets replaced replication controllers. A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time.

## Deployments and Updates

### Create Deloyment
```
kubectl create -f deployment.yml
```
### Get Deloyment
```
kubectl get deployments
```
### Update Deloyment
```
kubectl set image deployment/fitness-app mysql=mysql:8.0
```
### Status of Deloyment
```
kubectl rollout status deployment/fitness-app
```
```
kubectl rollout history deployment/fitness-app
```
### Rollback Deloyment
```
kubectl rollout undo deployment/fitness-app
```
### Kubernetes Commands

### kubectl
Kubectl is a Command line tool for communicating with a Kubernetes cluster.

### Kubernetes version running on nodes

```bash
kubectl version
```
### Number of nodes

```bash
kubectl get nodes
```
### Command to create a pod and deploy docker container in PODs

```bash
kubectl run mysql --image mysql
```
### List of PODS in Cluster

```bash
kubectl get pods
```
### Get information about POD

```bash
kubectl describe pod mysql
```
### Additional Information about POD

```bash
kubectl get pods -o wide
```
### Create POD using pod-definition.yml file

```bash
kubectl create -f pod-definition.yml
```
### Delete Pod

```bash
kubectl delete pod pod-name
```
### Edit Pod

```bash
kubectl edit pod pod-name
```

### pod-definition.yml

Kubernetes uses yml file as an input to create pods, replicas, deployments, services and so on,

The yml files contain four top level fields-

- apiVersion:  Depending on what we need to create use apiVersion, for creating pods use version v1
- kind: Type of object we want to create such as Pod
- metadata: Data about the object like name, labels and app.
- spec: Specification 

### Create replica sets

```
kubectl create -f replicaset.yml
```
### Get replica sets

```
kubectl get replicaset
```
### Delete replica sets

```
kubectl delete replicaset replicaset-name
```
### Replace replica sets

```
kubectl replace -f replicaset.yml
```
### Scale replica sets

```
kubectl scale -replicas=6 -f replicaset.yml
```
What is a Service?

A Kubernetes Service is a stable network endpoint that exposes Pods and allows reliable communication, even when Pods are recreated.

👉 Service = stable IP + DNS + load balancing

Why Pods need Services (IP problem 🚨)
The problem

Pods are ephemeral

When a Pod:

crashes

restarts

is rescheduled
👉 its IP address changes

Example:

Pod A IP = 10.244.1.5  ❌ (dies)
Pod A IP = 10.244.2.9  ❌ (new)
What happens internally?

Pod crashes

Kubernetes creates a new Pod

New Pod gets a new IP

Service automatically updates its endpoints

Traffic is sent to the new Pod IP

💡 Client never notices the change
2️⃣ Labels & Selectors (CORE Kubernetes Mechanism)
What are Labels?

Labels are simple key–value pairs attached to Kubernetes objects (Pods, Services, ReplicaSets, etc.).
Example:
labels:
  app: frontend
  env: prod
Think of labels as tags or stickers.
Why labels exist?

Kubernetes does NOT use Pod names to manage traffic or scaling.

Instead, it asks:

“Give me all Pods with label app=frontend”

That’s how everything works.

What are Selectors?

Selectors are rules used to select Pods based on labels.

Example:

selector:
  app: frontend


👉 Selectors = find matching labels
How Services find Pods (VERY IMPORTANT 🔥)
Service definition
kind: Service
spec:
  selector:
    app: frontend
    od definition
kind: Pod
metadata:
  labels:
    app: frontend


✅ Match found → Pod becomes part of the Service
❌ No match → Pod ignored
Traffic flow
Client → Service → Pods (matched by labels)
If Pod restarts:

New IP

Same label

Service auto-routes traffic

One diagram to remember everything 🧠
ReplicaSet
   |
   | selector (app=backend)
   ↓
Pods (app=backend)
   ↑
   | selector (app=backend)
Service


Same labels → different responsibilities.
Labels & Selectors (EASIEST WAY)
Think of it like this 🧠
Labels = Name tags

You put name tags on Pods.

Example:

Pod 1 → app=frontend
Pod 2 → app=frontend
Pod 3 → app=backend

Selectors = Search rule

Services and ReplicaSets search for Pods using labels.

Example:

selector: app=frontend


👉 “Give me all Pods with name tag frontend”

How Service uses labels (VERY SIMPLE)
Service says:

“I will send traffic to Pods with app=frontend”

Pods say:

“I have label app=frontend”

✅ Match → traffic goes to that Pod
❌ No match → Pod ignored

Flow:
User → Service → Matching Pods

How ReplicaSet uses labels (VERY SIMPLE)

ReplicaSet says:

“I need 3 Pods with label app=backend”

If only 2 Pods exist → create 1 more

If 4 Pods exist → delete 1

Flow:
ReplicaSet → keeps correct number of Pods

ONE picture in your head 🖼️
Labels = stickers on Pods
Selectors = filter to find stickers

Why this is IMPORTANT

Pod IPs change ❌

Pod names change ❌

Labels don’t change ✅

So Kubernetes uses labels, not IPs.
3️⃣ Namespaces (EASIEST WAY)
What is a Namespace?

A Namespace is a logical partition inside a Kubernetes cluster.

Think of it like:

Folders inside one computer

Same cluster, different spaces.
Why we use Namespaces?
1️⃣ To separate environments

Instead of multiple clusters:

ex
dev namespace
test namespace
prod namespace
All inside one cluster.
2️⃣ To avoid name conflicts

You can have:

mysql pod in dev
mysql pod in prod


Same name, different namespace → no problem.

3️⃣ To organize resources

Namespaces help manage:

Pods

Services

Deployments

Especially in big teams.
4️⃣ Security & access control

Give developers access to only dev

Restrict access to prod

(Used with RBAC — no need deep now)
4️⃣ ConfigMap & Secret (EASY + INTERVIEW READY)
The problem they solve 🚨

Hard-coding values inside Pods is bad.

❌ Bad practice:

DB_HOST=localhost
DB_PASSWORD=admin123


If value changes → rebuild image ❌

The solution ✅

👉 Store configuration outside the container.

That’s where ConfigMap & Secret come in.

ConfigMap (NON-SENSITIVE)
What is a ConfigMap?

A ConfigMap stores normal configuration data:

URLs

Port numbers

Environment names

Feature flags

Example:

DB_HOST=mysql-service
APP_ENV=prod

How Pod uses ConfigMap

Pod reads ConfigMap as:

Environment variables OR

Files

Easy example
env:
- name: DB_HOST
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: DB_HOST

Interview line 🎯

ConfigMaps are used to store non-sensitive configuration data outside the application code.

Secret (SENSITIVE 🔐)
What is a Secret?

A Secret stores sensitive data:

Passwords

Tokens

API keys

Certificates

Example:

DB_PASSWORD
AWS_ACCESS_KEY

Important point ⚠️

Secrets are:

Base64 encoded (not encrypted by default)

More secure than ConfigMaps

Used the same way as ConfigMaps

How Pod uses Secret
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password

Interview line 🎯

Secrets store sensitive information like passwords and tokens and are mounted securely into Pods.

ConfigMap vs Secret (EASIEST TABLE)
Feature	ConfigMap	Secret
Data type	Non-sensitive	Sensitive
Stored as	Plain text	Base64
Use case	App config	Passwords, keys
Simple real-world example 🌍
Frontend Pod
 ├─ ConfigMap → API_URL
 └─ Secret → API_TOKEN

One sentence to impress interviewers ⭐

We externalize application configuration using ConfigMaps and Secrets instead of hardcoding values in container images.

Common beginner mistake ❌

Putting passwords in ConfigMap.

Interviewers will reject this.

One golden rule 🔑

ConfigMap for config, Secret for secrets — never mix them.
5️⃣ Volumes (BASIC & EASY)
Why Pods lose data when restarted ❌

By default:

Containers store data inside the container

When a Pod:

restarts

crashes

is recreated

👉 All data is lost
Because:

Pods are temporary

Example problem
Pod writes file → /data/file.txt
Pod restarts ❌
file.txt is gone ❌

What is a Volume?

A Volume is a storage location attached to a Pod that:

Exists outside the container

Can survive container restarts (sometimes Pod restarts)

👉 Volume = external storage for Pods
Interview answer (easy 🎯)

Pods lose data when restarted because containers are ephemeral. Volumes provide persistent or temporary storage by storing data outside the container filesystem.
6️⃣ Ingress (BASIC IDEA)
Why Ingress exists (the problem 🚨)

Imagine you have multiple services in Kubernetes:

frontend-service
backend-service
auth-service

Without Ingress ❌

You must expose each service separately:

frontend → LoadBalancer IP

backend → LoadBalancer IP

auth → LoadBalancer IP

Problems:

Too many load balancers 💸

Hard to manage URLs

No clean routing
What is Ingress?

An Ingress is a single entry point for external traffic that:

Routes traffic to multiple services

Uses paths or domains

👉 Ingress works on HTTP/HTTPS (Layer 7)
Simple picture 🧠
User
 ↓
Ingress
 ↓
Service
 ↓
Pod
RL path

Host name

Example:

example.com        → frontend-service
example.com/api    → backend-service

Difference: Service vs Ingress (EASY TABLE)
Feature	Service	Ingress
Purpose	Expose Pods	Route external HTTP traffic
Level	Network (L4)	Application (L7)
Traffic type	All TCP/UDP	HTTP / HTTPS
External access	NodePort / LoadBalancer	Single public entry
Use case	Internal & basic external	Production routing

Interview answer 🎯

A Service exposes Pods, while Ingress provides a single external entry point and routes HTTP/HTTPS traffic to multiple services based on URL or domain.

gress ❌
Each app needs its own IP address.

That’s messy and expensive.

👉 Ingress solves this by giving one entry door for all apps.

What is Ingress? (one line)

Ingress is like a traffic police or gatekeeper that sends users to the correct app.

Simple real-life example 🧠

Think of a shopping mall:

One main gate → Ingress

Different shops → Services

Workers inside shops → Pods
User → Ingress → Service → Pod
Difference: Service vs Ingress (VERY EASY)
Service
Service connects Pods and exposes one app

Example:

Backend service

Frontend service

Ingress
Ingress decides which service should get the request
Ingress decides which service should get the request

Example:

/myapp   → frontend service
/api     → backend service

One-line interview answer 🎯

A Service exposes an application, while Ingress routes external traffic to the correct service using one common entry point.
