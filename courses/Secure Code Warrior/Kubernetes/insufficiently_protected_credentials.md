# Introduction
In the ever-evolving landscape of cloud-native applications, containerization has become a key strategy to ensure consistent and scalable application deployment. However, this approach comes with its own set of challenges, especially concerning the management of sensitive data like credentials. At a high level, improperly managing secrets can expose applications to a host of security vulnerabilities, compromising the confidentiality, integrity, and availability of services and data.

# Description
Secrets, in the context of software and containerized applications, refer to confidential data which applications need to function, such as API keys, passwords, database URIs, and encryption keys. This data, if mishandled, can serve as an easy entry point for malicious actors, enabling them to gain unauthorized access, exfiltrate sensitive data, or execute malicious operations.

Common pitfalls include:

**Hardcoding secrets**: Some developers may embed secrets directly in their application code or Dockerfiles. This is problematic as these secrets can be easily exposed if the code is publicly accessible, such as in open-source repositories.

**Inadequate protection**: Even when stored outside the application, if secrets are kept without encryption or in easily accessible locations, they are still at risk.

**Environment variables**: Using environment variables to pass secrets to applications is a widely accepted practice in the world of containerized applications. However, the primary concern lies in how these variables are set and managed. Storing secrets as hard-coded, plain-text environment variables in configuration files or directly in the application's codebase introduces significant security risks, particularly if these files are committed to version control or shared unintentionally. The true strength of environment variables is realized when they are dynamically populated at runtime from secure storage solutions, such as Kubernetes Secrets, Docker Secrets, or dedicated secrets management systems. In such scenarios, the environment variables serve merely as conduits, allowing the application to access secrets without ever exposing the sensitive data in less secure formats or locations.

# Mitigation Techniques
## Avoid hardcoding credentials
Never hardcode credentials or any other sensitive keys (like encryption keys or API keys) within the application code or container configurations. Hardcoded secrets can easily be exposed, especially when code repositories are publicly accessible.

## Leverage native solutions for secrets management
Both Docker and Kubernetes offer built-in mechanisms to manage secrets securely.

## Use encryption to protect secrets
Utilizing encryption is fundamental in secrets management to shield sensitive information from prying eyes. This not only involves encrypting secrets while they're stored (encryption at rest) but also as they're transmitted between services or over networks (encryption in transit).

## Use third-party secrets management tools
Several third-party tools offer robust secrets management capabilities:

- [HashiCorp's Vault](https://www.vaultproject.io/): This tool can securely store and tightly control access to secrets. It offers features such as dynamic secrets, secret versioning, and secret rotation.
- [Bitnami's Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets): Designed for Kubernetes, Sealed Secrets help encrypt secrets to be safely stored even in public repositories.
Cloud Providers' Secrets Managers: AWS Secrets Manager, Azure Key Vault, and Google Cloud Secret Manager specialize in secrets management and often come with features such as auto-rotation, audit trails, and access controls.

# Kubernetes
Kubernetes Secrets allows users to store and manage sensitive information. Unlike ConfigMaps which are designed for non-confidential data, Secrets are meant for sensitive information. Secrets can be either mounted as data volumes or exposed as environment variables to pods. Once created, they reside within a namespace and can be referenced by pods within that same namespace.

However, it's essential to note that while Kubernetes Secrets provide a mechanism to store sensitive information, by default, they are stored in etcd in a base64 encoded format, which isn't encrypted. So, [additional security measures are required](https://kubernetes.io/docs/concepts/security/secrets-good-practices/#configure-encryption-at-rest).

Let's explore some best practices for proper secrets management:

**Avoid hardcoding Secrets in YAML files**

Instead of hardcoding secrets in deployment YAML files which are typically committed to code repositories, create Secrets separately and reference them in your deployments.

This way, we avoided hardcoding the secrets in the Pod manifest file.

However, it's essential to keep your original Secret manifests secure (like the secret.yaml file) because they contain the base64 encoded values of your secrets. Although base64 encoding is not encryption, it can easily be decoded.

Due to the previous concern, it is important to consider the next recommendations.

**Use encryption at rest**

Kubernetes has the capability to encrypt data stored in etcd (where secrets are kept) using [EncryptionConfiguration](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/). This ensures that even if someone gains access to etcd backups, the secrets remain encrypted.

**Use external secrets management systems**

Solutions like HashiCorp Vault or AWS Secrets Manager can be integrated with Kubernetes to manage secrets outside of the cluster.

**Use dynamic secret creation**

Instead of hardcoding secrets in the Secrets YAML file with the additional overhead of keeping these files secure, dynamically create them using scripts or CI/CD pipelines. The secret values can be fetched from secure secret management systems at the time of deployment.

# References:
- https://owasp.org/www-project-kubernetes-top-ten/2022/en/src/K08-secrets-management
- https://cheatsheetseries.owasp.org/cheatsheets/Kubernetes_Security_Cheat_Sheet.html#securing-data
- https://kubernetes.io/docs/concepts/configuration/secret/
- https://kubernetes.io/docs/concepts/security/secrets-good-practices/
