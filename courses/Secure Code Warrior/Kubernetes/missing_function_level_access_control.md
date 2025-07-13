# Introduction
Role-Based Access Control (RBAC) is a fundamental security mechanism that determines the permissions for users and services in a system. Through RBAC, organizations ensure that each entity only interacts with the resources they are authorized to, thus enhancing the overall security posture.

# Description
RBAC works by associating operations (often termed as "verbs" like create, read, or delete) with objects or "resources". The end-goal is to ensure that a user or a service might have permission to perform a specific action on one type of resource but may be restricted from performing another action on a different resource. The scope of permissions can be as narrow as a sub-component of the system or as broad as the entire environment.

Permissions in RBAC are usually structured into roles, which are then assigned to users or services. These roles define a set of allowable actions on specific resources. The scope of the roles can be limited to parts of the system or applied system-wide.

# Mitigation Techniques
To ensure a secure implementation of RBAC and reduce the potential for misuse:

Follow the principle of least privilege
Always grant the minimal necessary access. This means only providing permissions essential for tasks to prevent potential vulnerabilities or unauthorized exploits.

Scoped permissions
Assign permissions specific to certain areas or resources, avoiding broad permissions that span the entire environment.

Avoid over-permissive roles
Some roles might grant extensive access. Use these sparingly and only when necessary.

Limit wildcards
While wildcards can simplify permissions, they can unintentionally grant more access than intended.

Limit direct access
Reduce the number of entities that can directly access critical resources.

# Kubernetes
At the heart of Kubernetes security mechanisms is Role-Based Access Control (RBAC). RBAC determines who can do what within a Kubernetes cluster, ensuring that users and services only have access to the resources they need, and nothing more. Fundamentally, RBAC associates verbs (like create, get, or delete) with resources (like pods, services, or nodes). For instance, a user might have permission to "get" a "pod," but not to "delete" a "service". Permissions can be defined in a narrow scope, just for a particular namespace, or in a broad scope for the entire cluster.

To implement RBAC in your environment, one must initiate the API server with a specific flag. Specifically, the --authorization-mode flag should be set to include RBAC.

A central feature of the RBAC API is its declaration of four primary Kubernetes objects: Role, ClusterRole, RoleBinding, and ClusterRoleBinding:

An RBAC Role or ClusterRole encapsulates a series of rules that denote a set of permissions. Notably, these permissions are additive, meaning there are no provisions for "deny" rules. The distinction between the two lies in their scope. A Role assigns permissions within a specific namespace, and its creation necessitates specifying this namespace. In contrast, a ClusterRole is not tied to a namespace, standing as a cluster-scoped resource.
A RoleBinding is designed to assign permissions defined in a role to specific users, groups, or service accounts within a given namespace. This is distinguished from a ClusterRoleBinding, which applies the permissions cluster-wide, without the restrictions of a namespace.
These are some examples of important considerations to take into account when configuring RBAC:

For instance, using the cluster-admin role in Kubernetes grants extensive permissions, allowing any action on any cluster resource. This becomes critical when linked with a ClusterRoleBinding, offering broad authority across the cluster.
Giving the LIST permission can be tricky. Even though you only see names when you use kubectl, it actually gets everything about those items. So, if someone can LIST, they see a lot. To keep things safe, only let someone LIST if you're okay with them seeing everything about that item.
Be careful with the WATCH permission. When using it, you're not just seeing item names, but everything about them when they change. If someone can WATCH, they see all the details. Make sure to only give someone WATCH if you're also okay with them seeing and listing everything about that item.

These are some examples of **important considerations** to take into account when configuring RBAC:

For instance, using the cluster-admin role in Kubernetes grants extensive permissions, allowing any action on any cluster resource. This becomes critical when linked with a ClusterRoleBinding, offering broad authority across the cluster.
Giving the LIST permission can be tricky. Even though you only see names when you use kubectl, it actually gets everything about those items. So, if someone can LIST, they see a lot. To keep things safe, only let someone LIST if you're okay with them seeing everything about that item.
Be careful with the WATCH permission. When using it, you're not just seeing item names, but everything about them when they change. If someone can WATCH, they see all the details. Make sure to only give someone WATCH if you're also okay with them seeing and listing everything about that item.

To minimize the potential for attackers to exploit RBAC settings, consistently review and tighten your configurations. Here are some guidelines to consider:

- Limit direct cluster access for end users as much as you can.
- Keep Service Account Tokens inside the cluster; don't use them externally.
- Refrain from auto-mounting the default service account token.
- Regularly review RBAC settings in third-party components you've installed.
- Implement central policies that identify and restrict hazardous RBAC permissions.
- Use RoleBindings to confine permissions to specific namespaces rather than granting broad, cluster-wide access.
- Caution on wildcards: while wildcards like * can be used to indicate 'all' for resources and verbs, this can inadvertently grant broader permissions than intended. It's always advised to follow the principle of least privilege, ensuring only the necessary permissions are provided.
- Always follow the [RBAC best practices](https://kubernetes.io/docs/concepts/security/rbac-good-practices/) detailed in the official Kubernetes documentation

# References
- https://owasp.org/www-project-kubernetes-top-ten/2022/en/src/K03-overly-permissive-rbac
- https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html
- https://kubernetes.io/docs/reference/access-authn-authz/rbac/
