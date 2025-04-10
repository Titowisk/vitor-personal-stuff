# Introduction
In the era of containerization, Docker and Kubernetes have become central players. However, with their versatile configurations comes potential security pitfalls. One significant vulnerability that can be easily overlooked is improper permissions. Ensuring stringent and appropriate permissions is paramount to avoid potential security breaches.

# Description
The adoption of containers has paved the way for faster, more efficient deployments, and scalable applications. However, the flexibility of configurations also introduces several potential weak points. One of the most subtle yet dangerous is the misconfiguration of permissions. In the digital world, permissions act as gatekeepers, determining what actions are allowed and by whom. While these container platforms provide extensive configurations to fine-tune these permissions, their default settings might not always align with the best security practices. Improper permissions can lead to scenarios where malicious actors exploit these vulnerabilities to gain unauthorized access, manipulate application behavior, or escalate privileges, putting the entire system at risk. Recognizing and rectifying these misconfigurations is imperative to maintain the integrity and security of containerized environments.

# Mitigation Techniques
Avoid running as root
One of the primary rules in container security is to avoid running container processes as the root user. By default, many container images are set to run as root. This can pose a significant risk because if an attacker gains control over a container running as root, they can potentially compromise the host system as well.

Avoid privileged containers
A privileged container is essentially a container that has almost all the capabilities of its host machine. Running containers in privileged mode breaks the isolation between the container and its host, making it a potential security risk.

Avoid the use of writable filesystems
Containers should be treated as immutable entities, meaning once they're spun up, their filesystems should not change. If a container uses a writable filesystem, it could be modified during its runtime, potentially leading to various security vulnerabilities.

# Kubernetes
Kubernetes workload configurations, while versatile, often open the door to potential security misconfigurations. Ensuring that containers run with the least privilege necessary is a fundamental best practice to minimize security risks. Critical precautions include avoiding running containers as the root user, restricting the use of writable file systems, disallowing privileged containers, and consistently auditing access controls. By failing to enforce strict permissions and configurations, clusters can become vulnerable to unauthorized data access, container breakouts, and other malicious activities, undermining the robustness and security of the entire Kubernetes environment.

Let's explore some recommendations:

**Avoid running as root**: Containers running processes as root pose a significant risk. If compromised, this grants attackers root-level access to initiate harmful processes. It's pivotal to limit root usage to essential workloads. You can specify the user a container runs as by setting the runAsUser field under securityContext in a Pod manifest. The user id 0 corresponds to the root user.

**Avoid privileged containers**: Privileged containers have the ability to access all the host's resources and capabilities, essentially circumventing the isolation that containers typically provide. Pairing containers that run as root with privileged containers can be particularly disastrous, as it could provide malicious actors full access to the host system. Even for non-root users, running a container in privileged mode should be avoided unless strictly necessary due to the associated risks. It's recommended to set the privileged field under securityContext to false in the Pod manifest to ensure the container runs in non-privileged mode.

> NOTE: If there's a need to grant specific capabilities without running the container in full privileged mode, consider using the capabilities field under securityContext to add or drop specific capabilities. This provides a finer-grained control over container permissions.

**Avoid the use of writable filesystems**: Utilizing writable filesystems in containers can make them vulnerable, especially if they are compromised. A writable filesystem allows malicious processes or applications to make undesired changes, potentially persisting malware or exfiltrating data. To guard against this, it's strongly advised to make the container's root filesystem read-only. This significantly reduces the avenues of attack, ensuring that even if the container is compromised, the attacker cannot write back to the system. In the Pod manifest, this can be achieved by setting the readOnlyRootFilesystem field under securityContext to true.

> NOTE: For those scenarios where a container needs to write data temporarily, consider using Kubernetes' ephemeral storage, like emptyDir, which provides a temporary space that is cleared when a Pod is removed. This allows for a writable space without compromising the main filesystem.

# References
- https://owasp.org/www-project-kubernetes-top-ten/2022/en/src/K01-insecure-workload-configurations
- https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html
- https://kubernetes.io/docs/concepts/security/