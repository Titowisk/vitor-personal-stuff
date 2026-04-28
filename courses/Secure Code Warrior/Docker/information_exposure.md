# Introduction
In the realm of containerized applications, network segmentation becomes even more paramount. Containers, by design, encapsulate applications in discrete environments, ensuring that they run consistently across various platforms. However, if proper network segmentation isn't implemented, a compromise in one container could potentially lead to unauthorized access or exposure in another. The microservices architecture, often employed with containers, further emphasizes the importance of this. Each microservice, while operating independently, communicates over the network, and insufficient segmentation could mean that sensitive information might leak, be tampered with, or even be exposed to unauthorized entities.

# Description
With the advent of containerization, networking dynamics have experienced a profound change. If not properly configured, the network where containers operate might be devoid of segmented partitions and strict firewall rules. This can pave the way for a network setup where every microservice can communicate freely with its peers, raising security concerns. To make matters more intricate, the general practice of dedicating one microservice to a single container amplifies the networking challenge. This is because while some microservices need to communicate with each other, others shouldn’t due to security reasons.

# Some pressing threats include:

Internet exposure of orchestration tool management interfaces, even if these tools require login credentials.
Microservices accessing host services.
Microservices from the same application being unnecessarily exposed within the LAN/DMZ.
Traditional services like NFS, Samba, CI/CD tools, and databases becoming accessible targets.
Inadequate network separation between different tenants, causing them to share the same network.
Mitigation Techniques
Strict port management
Explicitly define and document the network ports that your containerized applications should expose and listen to. Regularly audit and ensure that only the necessary ports are exposed to mitigate potential access points.

Network isolation and segmentation
Create distinct networks for different types of workloads or services. For instance, a database should ideally not be on the same network as frontend services. When running multiple containerized applications or microservices, ensure they are in separate networks unless they genuinely need to communicate with one another.

Implement network policies
Even within a segmented network, use network policies to define precise communication rules between containers or services. By default, most orchestrators allow broad communication; tightening these rules reduces the risk of lateral movement in the event of a breach.

Docker
Docker provides robust networking capabilities to ensure seamless communication between containers and with external systems. One primary mechanism to facilitate this is through the exposure of ports.

Within the Dockerfile, the EXPOSE directive denotes that the container listens on specified network ports at runtime. However, the actual port the application inside the container listens on is determined by the app's configuration. This distinction is crucial because it's possible for the application to be set up to listen on a different port than what's indicated by the EXPOSE directive. It's essential to understand that the EXPOSE command in the Dockerfile doesn't automatically open the port. Instead, it serves as documentation to inform users of the image about the intended ports for communication.

To actually bind and open the ports, one must utilize the -p flag during the docker run command, e.g., -p 8080:80 would map the host's port 8080 to the container's port 80.

On the other hand, Docker Compose, provides a more structured approach. In the docker-compose.yml file, under the services section, users can define the ports directive to specify which ports of the service should be exposed to the host. For instance:
```
version: '3'
services:
web:
    image: nginx:latest
    ports:
    - "8080:80"
```

This configuration achieves the same port mapping as the docker run command example earlier. Whether using raw Docker commands or Docker Compose, managing network exposures and port bindings is pivotal for ensuring that containerized applications can communicate effectively while maintaining secure boundaries.

Be aware that if you need to expose a port to be used internally between 2 services, you should not use the ports directive but the expose directive. This will indicate which ports are intended to be used by the application inside the container without making them accessible from the host. Instead, they'll only be accessible to other services within the same Docker network. This method provides an added layer of security, as the exposed ports are shielded from external access while still allowing inter-service communication.

Insecure Code Example
The Docker Compose file below should configure the app1 service to be internally accessible by other services on port 8885 while also making it externally accessible from the host on port 8080.

```
version: '3'

services:
  app1:
    image: app1_image_name
    ports:
      - "8080:8080"
      - "8885:8885"

  app2:
    image: app2_image_name
    depends_on:
      - app1
    environment:
      APP1_URL: "http://app1:8885"
```

The provided Docker Compose file exposes a potential concern regarding network isolation. While the intent seems to be for port 8885 of app1 to be accessible only internally by other services, the current configuration with the ports directive exposes it externally to the host as well. This configuration makes the port reachable not only by other services but also by any external entity that can access the host's IP on that port. This can be a significant security risk, especially if the service running on port 8885 is not meant for external exposure.

Secure Code Example
The Docker Compose file below should configure the app1 service to be internally accessible by other services on port 8885 while also making it externally accessible from the host on port 8080.

```
version: '3'

services:
  app1:
    image: app1_image_name
    expose:
      - "8885"  # Expose port 8885 to other services, but not to the host
    ports:
      - "8080:8080"  # Expose port 8080 to the host

  app2:
    image: app2_image_name
    depends_on:
      - app1
    environment:
      APP1_URL: "http://app1:8885"
```

The provided Docker Compose file effectively maintains network isolation according to the stated requirements. In the configuration, the app1 service exposes port 8885 only to internal services using the expose directive, ensuring that this port isn't accessible from outside the Docker network. Conversely, port 8080 is explicitly made available to the host through the ports directive, enabling external access as intended. This setup ensures that app2 and any other services within the Docker network can communicate with app1 on port 8885, while external entities can only access app1 via port 8080 on the host machine.

References & Resources
Docker:
OWASP Docker Top 10 - D03: Network Segmentation and Firewalling
OWASP Cheat Sheet Series - Docker Security
Docker Networking
Kubernetes:
OWASP Kubernetes Top 10 2022 - K07: Network Segmentation
OWASP Cheat Sheet Series - Kubernetes Security
Kubernetes Network Policies