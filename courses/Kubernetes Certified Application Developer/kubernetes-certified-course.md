# Notes

## Kubelet
- the primary "node agent" that runs on each node

# Section 3 - Configuration

## Environment Variables
```yaml
env:
  - name: APP_COLOR
  value: pink
```

- it can be defined based on ConfigMap or Secret
```yaml
env:
  - name: APP_COLOR
  valueFrom:
    configMapKeyRef:
```

```yaml
env:
  - name: APP_COLOR
  valueFrom:
    secretKeyRef:
```

## ConfigMaps

## Secrets 
A quick note about Secrets!
Remember that secrets encode data in base64 format. Anyone with the base64 encoded secret can easily decode it. As such the secrets can be considered as not very safe.

The concept of safety of the Secrets is a bit confusing in Kubernetes. The kubernetes documentation page and a lot of blogs out there refer to secrets as a "safer option" to store sensitive data. They are safer than storing in plain text as they reduce the risk of accidentally exposing passwords and other sensitive data. **In my opinion it's not the secret itself that is safe, it is the practices around it.** 

Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:

- Not checking-in secret object definition files to source code repositories.
- Enabling Encryption at Rest for Secrets so they are stored encrypted in ETCD. 

Also the way kubernetes handles secrets. Such as:

- A secret is only sent to a node if a pod on that node requires it.
- Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
- Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

Read about the protections and risks of using secrets here:
- https://kubernetes.io/docs/concepts/configuration/secret/#protections
- https://kubernetes.io/docs/concepts/configuration/secret/#risks
- https://kubernetes.io/docs/concepts/configuration/secret/#risks

Having said that, there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, HashiCorp Vault. I hope to make a lecture on these in the future.

### Secrets and etcd
By default, etcd doesn't encrypt secrets stored there. So it's necessary to encrpyt etcd at rest.
This will encrypt newly created secrets, but not those who were created already. (encrpytion is on write)
It's necessary to update the created secrets to encrypt them.

## Security
- how to manage capabilities and users for containers and pods

## ServiceAccount
- allow access to the kubernetes API of the cluster
- user accounts are used by humans
- service accounts are used by machines 
- creates a token to grant access to applications to use kuberentes API - **No longger happens for version 1.24+**
  - this creates a `Secret` object automatically to be used by the `ServiceAccount` **No longger happens for version 1.24+**
- theres a default serviceAccount that is created by namespace for every prod created within it.

## Resource Requirements (CPU, Memory)
- define the start requirements of resources
- pods can consume more them the required. 
  - limits can be set to avoid it

### Exceed Limits
- when pod exceeds CPU, it is throttled
- when pod exceeds Memory, it is terminated and an OOM (out of memory) is thrown in logs

### Behavior - CPU and Memory
- if requests and limits are set for all pods, it can lead to resource waste when one pod requires more CPU/Memory than others
- ideally, requests are set but no limits. (of course it can change depending on the problem)

## LimitRange
- it can define resource requirements at the level of namespace. This avoids having to define it for each POD
- it only affects only PODs created after the definition of `LimitRange`

## Resource Quotas

## Taints and Tolerations
- relationship between nodes and pods
- taints determine witch pods tolerates been inserted in nodes

## Node Selector
- selects node by name. Nodes must be labeled previously

## Node Affinity
- its possible to define rules to allocate pods to nodes
- these rules are based on various conditions
  - the behavior dependes wether its applied during scheduling of pods or during execution of pods

# Section 4 - Mult-Container Pods

## Ambassador

## Adapter

## Sidecar

## Init Containers
- Are different from the other containers in this sections. They must run to completition BEFORE the other containers start to run.
- does not share all the features that the other containers have

# Section 5 - Observability

## Readness Probe
K8s doesn't know for sure when a Pod is actually ready to receive client traffic. K8s can determine that the container is running inside the pod, but it isn't ready to receive client traffic because of some other start up step from the application.
So, it's up to the developer to determine a way to tell k8s that the Pod is ready

- for API, a route /api/ready or /health can do the trick
- for database, a TCP Test - [port]
- or just a Exec Command inside the container

## Liveness Probe
The same logic as Readness Probe, but it evaluates the health of a live containers. Sometimes applications can
crash but keep running in an error state, thus k8s will understand its still running without problems.
So, the developer can determine ways to check if containers are healthy

- for API, a rout /api/health can check for it
- for database, a TCP Test - [port]
- or just a Exec Command inside the container

## Monitoring
- Metrics Server - In memory
- Prometheus - Open Source
- ElasticStack
- Datadog
- Dynatrace

# Section 6 - POD Design

## Labels, Selectors and Annotations
- labels are for categorization
- selectors are for finding categorized objects

## Rolling Updates & Rollbacks in Deployments
- rolling update is when a change in the deployment makes k8s deploy a new pod and disable the old pod, one at a time.
  - it creates a new replica set to deploy new pods, and keep the old replica set to delete the pods
- rollback uses both replicaSets in reverse

### Deployment Strategies
- rolling update
- recreate
- blue/green: creates both replicasets and their pods, and in one move switches all traffic to the new pods (green)
  - best implemented with istio
- canary

## Jobs and Cron Jobs
- define a set of pods to perform a job to completition and them stop all of them
  - to better understand, this is different from replicaSets that define a set of pods to be running indefinetely
- cron jobs can be scheduled

## Completitions
- defines the amount of pods to be created
- by default, one pod is only created when the last one has run successfully
  - pods are created until the number of successful completitions is achieved

# Section 7 - Services and Networking
- NodePort
- ClusterIP
- LoadBalancer

## NodePort
```yaml
ports:
  - targetPort: 80 # port of the pod that the service wants to forward requests
    port: 80 # port of the nodePort service created
    nodePort: # port of the node that contains the service and the pod
```
- it can exists across multiple nodes

## ClusterIP

## Ingress
- handles load balancing of traffic
- dns
- https and tls/ssl
  - ssl: secure sockets layer (deprecated)
  - tls: transport layer secury (is the replacement for ssl) 
- single point of entry

### Ingress Controller
- Nginx, HAPROXY, traefik
- requires multiple components: deployment, service, configMap, serviceAccount

### Ingress Resources
- where rules are defined to decide routing and traffic
- uses `kind: Ingress` component

## Network Policies
- by default, all pods reside in a virtual network and can communicate with each other
- defines policies to manage communication inside the network
- it's applied to pods
- `kind: NetworkPolicy`

### Tips:
- if a policy is defined for ingress, but not for egress, k8s will allow only traffic defined by the ingress policy and will
allow all traffic for egress.

# Section 8 - State Persistence

## Volumes
- a way to persist data idependently of pod life cycle

## Tips
- defining volumes for multi-node clusters will create volumes for each node and can lead to
undesirable behavor
- according to the documentation, hostPath volumes shoudl be avoided because of many security risks
  - https://kubernetes.io/docs/concepts/storage/volumes/#hostpath 

## Persistent Volumes (PV)
- cluster wide volumes configured by an administrator to be used by user deploying applications to the cluster
- can be requested using persitent volume claims (PVC)
- PVCs and PVs ahave a 1:1 relationship. If a pvc claims only half of a PV, other PVCs can't use it too

## Persistent Volume Claim (PVC)
- when a PVC is deleted, by default the bounded PV remains. And no other PVC cam claim it
  - `persistentVolumeReclaimPolicy: Retain`
  - `persistentVolumeReclaimPolicy: Recycle` data is pruned and PV is available to other PVCs

## Storage Class
- dynamic provisioning of persistent volumes
- `kind: StorageClass`
- when definied, the PVs will be automatically created when PVCs are created

## StatefulSet
- creates pods with unique IDs
- defines master/slave pods: masters can receive Read and Write requests while slaves only Read
- need a HeadLess Service: because of master/slave requirements, regular services aren't useful for StatefulSets

### Headless Service
- provides a dns especification for each pod that's not based on IP
  - `[podname.headless-servicename.namespace.svc.cluster-domain.example]`

# Section 9 - Security

## User
Kuberntes does not have an internal way to handle user access. It has only
the Service Account for bots access

Access is managed by `kube-apiserver` that depends on
- static Password File (NOT RECOMMENDED)
- static token file (NOT RECOMMENDED)
- certificates
- third-party Identity Services

## KubeConfig
- Clusters
- Contexts
- Users

Define a cluster, an user and them a context to determine which user should be used for a cluster.

## API Groups
- /apps
- /extensions
- /networking.k8s.io
etc

- each group has **resources** and each resources has **verbs**

### Tips
- kube proxy != kubectl proxy

## Authorization
- more than one authorization-mode can be configured for the KubeApi
  - requests are authorized using the modes in the order it was specified

### Node

### Attribute Based Access Controll - ABAC
- associate user or group to a set of permissions

### Role Based Access Controll - RBAC
- permissions are defined for specifc roles and these roles are associated with users
- standard approach

### Webhook
- delegates authorization management to third party tools

### AlwaysAllow vs AllwaysDeny

## Cluster Roles
- namespace scoped: pods, replicasets, jobs, deployments, services, roles, rolebindings, configmaps, pvcs...
- cluster scoped: nodes, PV, clusterroles clusterrolebindings, certificatesigniningrerequests, namespaces

- roles are for namespace scope
- cluster roles are for cluster scope

## Admission Controller
- User > kubectl > Authentication > Authorization > Admission Controllers > resource:verb (Create Pod)
- manages what specific things can be configured or used within resources
that cant be done by auth

- Examples: AlwaysPullImages, DefaultStorageClass, EventRateLimit, NamespaceExists, etc

### Mutating Admission Controller x Validating Admission Controller
- Mutating AC: it can change the object before its created
- Validating AC: it can validate the object and allow or deny its creation
- mutation are applied before validations

Example
- NamespaceAutoProvision (Mutating AC) is applied before the NamespaceExists (Validating AC)

- MutatingAdmissionWebhook and ValidatingAdmissionWebhook can be configured to use external **custom** MAC and VAC 

## API Versions
- determines the reliability of the api used
- non-reliable versions like alpha and beta should be avoided

- preferred/storage versions

### API Deprecation
- in k8s, api elements can only be deprecated by increasing the api group version
  - example: /v1alpha1 has /feature-A and /feature-B ==> /v1alpha2 has only /feature-B (A removed)
  
## Custom Resource

### Custom Resource Definition
Its possible create custom kuberentes components with user definied `kind` and `spec`
The desired states manifested in the `.yml` file is stored in the `etcd` in the Control Plane

- it's better to develop this in `Go`

### Custom Controller
Custom resources need custom controllers to work
Custom Controllers will observe the desired state and the status, and take actions to achieve the desired state
and to perform the user defined behaviors of the custom resource

### Operator Framework
Encapsulate both CRD and Custom Controller

# Section