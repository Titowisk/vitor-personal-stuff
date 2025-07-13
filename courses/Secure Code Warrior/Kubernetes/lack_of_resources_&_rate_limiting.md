Introduction
Lack of resource protection in containerized applications is a critical concern in modern software development and deployment practices. Containers provide an isolated environment to run applications, but when resource constraints and protection mechanisms are not properly implemented, it can lead to a myriad of issues, ranging from poor performance to security vulnerabilities. Inadequate resource protection can allow a single container to monopolize system resources, potentially leading to Denial of Service (DoS) or impacting the stability of other containers running on the same host.

Description
In the deployment of containerized applications, like those orchestrated by Kubernetes or Docker, each container receives allocations of resources such as CPU, memory, disk I/O, and network, based on the settings defined during deployment. A lack of sufficient resource protection can allow containers to consume more than their allocated share, adversely affecting the performance and stability of other containers on the same host machine.

For instance, when multiple containers are deployed on a single host and share the host’s resources, the absence of limitations or quotas can allow one container to consume an excessive amount of CPU or memory. This can lead to resource exhaustion, causing other containers to be deprived of essential resources, resulting in slow performance, application failures, or even system crashes in severe cases.

Mitigation Techniques
To circumvent the issues associated with a lack of resource protection, adopt the following best practices:

Define resource requests and limits
Specify resource requests and limits for every container to avoid resource contention and ensure fair distribution of resources amongst containers.

Use quotas
Implement quotas to assign specified resources to a group of containers, preventing resource exhaustion and upholding a balanced environment.

Monitor Resource Usage
Regularly monitor the resource usage of containers and modify the limits and requests based on actual usage patterns to sustain optimal performance and security.

# Kubernetes
In Kubernetes, resource constraints are primarily established through the configuration of Limits and Requests for CPU and Memory resources, although constraints can be applied to other resources as well.

When no resource constraints are explicitly configured within a Kubernetes Pod or container, Kubernetes defaults to a relatively permissive behavior. In the absence of CPU and Memory limits, containers are allowed to consume available resources without strict boundaries. This means that the container can potentially use all the CPU and memory available on the node it's running on. While this flexibility can be advantageous in certain scenarios, it also carries the risk of resource contention and instability within the cluster.

Resource constraints in Kubernetes include:

1. **Requests**: These are the amount of CPU/Memory that Kubernetes will guarantee for the container. If your container tries to use more than this, it will not be killed for exceeding the amount.
2. **Limits**: These are the maximum amount of CPU/Memory that the container can use. If the container tries to exceed the limit, it will be terminated and potentially restarted.
In Kubernetes, resource constraints can be set at various levels within the cluster to control the allocation of CPU and memory resources. Here are the primary levels at which resource constraints can be configured:

**Pod Level**

Setting resource constraints at the [Pod level](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) is the most granular and flexible approach. You can specify resource requests and limits for each container within a Pod. This allows you to tailor resource allocations to the specific needs of your application components. It's commonly used when different containers within the same Pod have varying resource requirements.

**Namespace Level**

Resource constraints can also be set at the namespace level to manage resource allocation for multiple Pods within the same namespace. This approach simplifies resource management by providing defaults and constraints for all Pods in that namespace.

- **ResourceQuotas**: You can use [ResourceQuotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/) to set limits on the total amount of CPU and memory that all Pods in a namespace can consume. This restricts resource consumption at the namespace level and prevents resource contention.

- **LimitRange**: Additionally, you can use the [LimitRange](https://kubernetes.io/docs/concepts/policy/limit-range/) object to specify default values for CPU and memory requests and limits for containers within Pods in the namespace. If developers do not explicitly set these values in their Pod definitions, the LimitRange will apply the defaults and enforce resource constraints.

By combining ResourceQuotas and LimitRange at the namespace level, you can establish comprehensive resource management practices within the namespace, ensuring both total resource usage and individual container constraints are well-defined and adhered to.

# References
- https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html#limiting-resource-usage-on-a-cluster
- https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/