
# Vulnerability in Clusters

When considering cloud-native deployments, we can identify three primary domains vulnerable to attacks. 

The **first** is interfaces associated with **the orchestration tool**, which includes the management dashboard, the etcd datastore, and the API interfaces. These interfaces are gateways to manage and modify cluster configurations, and their compromise could allow unauthorized access and alterations.

The **second** domain is interfaces from **the host system**. These range from RPC services, which enable inter-process communications, to OpenSSHD, a protocol for secure remote logins. Also in this category are network listeners like Avahi for service discovery on local networks, and certain systemd services. An attacker with access to these can potentially gain broad privileges on the host.

**Lastly**, we have interfaces within the **container**. This encompasses vulnerabilities either from the microservice itself, such as those developed using frameworks like Spring Boot, or arising from the base distribution on which the container is built. A compromise here might lead to application breaches or exploitations of underlying infrastructure.

# Mitigation Techniques
## Ensure recommended permissions are not modified
It's imperative to verify that the default security permissions, particularly those recommended by trusted sources like CIS, remain intact. Modifying or overriding these permissions without a comprehensive understanding can introduce vulnerabilities.

## Do not grant excessive capabilities
Both Docker and Kubernetes have mechanisms to control the capabilities granted to containers and services. Granting excessive or unnecessary capabilities can compromise security.

## Ensure orchestration components are not misconfigured
Orchestration tools, whether it's Docker's engine or Kubernetes' various components, are complex systems with multiple configuration settings. Misconfiguration can expose systems to potential threats. Regularly review configurations against best practice guidelines to ensure they are securely set.

## Limit external exposure
Avoid exposing essential components, such as the API server in Kubernetes or Docker Engine, to the public internet. Instead, keep communication internal and secure, relying on TLS and other encryption mechanisms.

## Maintain secure communication
Whenever components communicate, whether internally or externally, ensure the data remains encrypted in transit. Utilize trusted encryption standards like TLS to maintain data integrity and confidentiality.

# Kubernetes

Certain misconfigurations can leave a Kubernetes cluster exposed to threats, allowing adversaries to compromise essential components, potentially extending their reach beyond containerized processes.

The Center for Internet Security (CIS) provides a [benchmark](https://www.cisecurity.org/benchmark/kubernetes) for Kubernetes that includes a comprehensive set of security best practices and recommendations. For the Kubernetes components, the CIS recommendations can be summarized as follows:

- API server:
    - Ensure secure communication using TLS.
    - Avoid exposing the API server to the Internet.
    - Implement strict authentication and authorization modules, avoiding modes like AlwaysAllow.
    - Enable only necessary admission control plugins and disable potentially risky ones.

- etcd:
    - Protect communication with etcd using TLS.
    - Ensure etcd is not exposed to the public Internet.
    - Store etcd data (including secrets) encrypted at rest.

- Controller manager:
    - Use secure communication methods.
    - Limit the range of IP addresses that can communicate with the controller manager.
    - Disable unnecessary and potentially insecure features, such as RotateKubeletServerCertificate.

- Scheduler:
    - Ensure secure communication with the scheduler.
    - Limit the IP range that can access the scheduler.

- Kubelet:
    - Avoid anonymous authentication.
    - Set authorization mode to something other than AlwaysAllow.
    - Secure communication with the kubelet using TLS.

# Secure Code Example

The Kubernetes manifest provides a secure configuration for the API Server pod by enforcing two authorization modes: Node and RBAC. The Node authorization mode ensures that kubelets can only access resources relevant to their specific tasks, while RBAC (Role-Based Access Control) offers granular control over which Kubernetes API resources users and services can access. By enabling these modes, the API server significantly reduces the risk of unauthorized or inadvertent actions, helping safeguard the integrity of the Kubernetes cluster.


# References
- https://owasp.org/www-project-kubernetes-top-ten/2022/en/src/K09-misconfigured-cluster-components
- https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html
