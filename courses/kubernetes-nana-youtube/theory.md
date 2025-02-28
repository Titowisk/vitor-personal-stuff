# Concepts

## Nodes: collection of pods

## Pods: abstraction for the container. 
- Usually one container per Pod
- One IP Address per Pod

## Ingress
- responsible for external communication

##  Service: component coupled with pods
- Handles communication with the pod to avoid constant changes to IP Addresss
- Has unique Ip Address
- load balancer between pods of different nodes

## Config Map: coupled with an Pod
- store configuration information for the application to run

## Secret: similar to ConfigMap
- store sensitive information for the application to run


## Volumes: responsible for persistent storage
- attach a physical storage for the database. It can be a local storage (same server) or remote storage
- hard drive coupled with the k8s cluster

## Deployment: blueprint for pods
- manages pods
- creates replicas: replicas cannot be created for statefull components like databases
- for stateless apps

## Statefulset
- for statefull apps or databases
- hard to manage: usually statefull components are created outside k8s clusters

# K8s Archtecture
- each node has multiple pods
- 3 processes must be installed on every node
- worker nodes do the actual work

## Worker Nodes

### Container runtime - process

### Kubelet - process
- interacts with container and node
- starts the pod with the container inside it and assign the resources of the node

### Kubeproxy - process
- fowards the requets of the application

## Master Nodes
- 4 processes run on every master node

### API Server
- gateway (1 entrypoint)
- authtentication

### Scheduler
- decides where to put new Pods

### Controller manager
- detects cluster state changes
- recover from state changes

### etcd
- key-value store for the cluster (cluster brain)


# Main Kubectl Commands

## Deployment
- manages pods, replicasets

- creates pods using deployment name and image. The rest is just defaults
`kubectl create deployment nginx-depl --image=nginx`

- get the yaml configuration for editing
`kubectl edit deployment nginx-depl`

- get detailed information from the pod
`kubectl describe pod [pod name]`
kubectl describe pod mongo-depl

- get logs from pod
`kubectl logs [pod name]`
kubectl logs [pod name]

kubectl logs mongo-depl-67d7db846c-vd7g8

- get access to the application terminal
`kubectl exec -it [pod name] -- bin/bash`

kubectl exec -it mongo-depl-67d7db846c-vd7g8 -- bin/bash

- delete deployment (pods and replicasets)
`kubectl delete deployment nginx-depl`

- create deployment from configuration file
`kubectl apply -f config-file.yaml`

# Yaml Configuration File
- apiVersion field
- kind field: deployment, service, ...
- it has 3 parts: metadata, specification, status (automatically generated)

## Metadata
## Specification
- specification configuration is different depending on the kind field

### Template
- has its own `metadata` and `spec`section
- applies to Pod
- blueprint for a Pod: port?, image?, name?, etc

### Connecting Deployment to Pods
- `labels` is used to define identification for pods
- is defined in `template`
- this label is matched by the `selector`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-depl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-depl
  template:
    metadata:
      labels:
        app: nginx-depl
```

## Status
- is automatically generated
- represent the current status of the component defined in `kind`
- get the information from `etcd`

## Tips
- store configuration files alongside the code: infrastructure as code or repository location
- yaml validator

# Namespaces
- organize resources in namespaces
- virtual cluster inside a cluster
- k8 clusters provides 4 default namespaces

## Use Cases
- structure your components
- avoid conflicts between teams
- share services between different environments
- access and resource limits on namespaces level

## Caveats
- you cant access configMap and secret of another namespace
- you CAN access services from another namespace

## Tips
- it's good to organize resources in namespaces even in smaller projects
- it avoid conflicts: overriding deployments
  - deployments with the same name can live in different namespaces
- creating namespaces for environments (staging and development) can be good for resource sharing ammong this environments
- A/B testing: creating namespaces for different versions of production
- you can limit access or maybe resources (cpu, ram, storage) per namespace

# Ingress
- ingress != external service
- external service open the IP address and port while ingress shows dns address
- it needs an implementation: ingress controller

## YAML configuration
- kind: Ingress
- routing rules: fowards request to the internal service
- host: must be a valid domain address
  - must map domain name to Node's IP address, which is the entrypoint

## Ingress Controller
- evaluates all the rules
- manages redirections
- entrypoint to cluster
- many third-party implementations

### Multiple paths
- create rule for each `/path` of application
  - ex: /analytics, /shopping

### Multiple sub-domains or domains
- creates rule for each domain or sub-domain
  - ex: analytics.myapp.com, shopping.myapp.com

## TLS Certificate
- data keys need to be `tls.crt` and `tls.key`
- values are file contents not file paths/locations
- secret component must be in the same namespace as the ingress component

# Helm
- package manager for kubernetes: to package YAML files and distribute them in public and private repositories
- templating engine
- same applications across different environments

## Helm Chart
- bundle of YAML files

# Persisting Data in K8s with volumes
- cluster resource (like ram or cpu)
- created via YAML file
- kind: PersistentVolume

## Tips
- storage that doesn't depend on the pod lifecycle
- storage must be available on all nodes
- storage needs to survive even if cluster crashes

## Persistent Volume (PV)
- define a volume to be used from a local disk or some remote storage

## Persistent Volume Claim (PVC)
- kind: PersistentVolumeClaim
- decides witch persistent volume (PV) will be claimed
- its used for Pods in their yaml config
- must exist in the same namespace as the pod that will use it

## Storage Class (SC)
- provisions PVs dynamically when PVCs claims it
- uses attribute `provisioner`

# StatefulSet

## Deployment of stateful vs stateless applications
- Deployment is used for stateless
- StatefulSet is used for stateful applications
- replicating stateful applications ir harders then stateless

### Replication
- stateless:
  - identical and interchangeable
  - created and delete in random order with random hashes
  - one Service that load balances to any Pod

- stateful:
  - cant be created/delete at same time
  - cant be randomly addressed
  - replica Pods are not identical

  ## Scaling database applications
  - one master and `n` worker pods
  - only one pod can have write permissions because of data consistency
    - the others can only have read permission
  - pods must have a sticky identity: when a pod dies, it is reacreted with the same id
  - they access different PV that must have the same data so they are always synchronizing with the master pod


# Kubernetes Services

## ClusterIP Services
- default

## Headless Services
- client wants to communicate with 1 specific Pod directly
- Pods want to talk directly with specific Pod
- Use Cases: Stateful applications, like databases
- set ClusterIP attritube to None

## NodePort Services
- is externally accessible
- not secure

## LoadBalancer Services
- it becomes the entrypoint for the cluster
- defines ClusterIP and NodePort services automatically
- 