# CI/CD Notes

## Kubernetes

Is a powerful open-source platform for container orchestration. It provides an efficient framework to deploy, scale, and manage applications, ensuring they run seamlessly across a cluster of machines.
This tool ensures that containerized applications run reliably, effectively managing deployment and scaling while abstracting the hardware and network configurations.

### How does Kubernetes work?

Kubernetes operates through a control plane and core components, each with specialized roles in managing containerized applications across clusters.

#### Nodes

Nodes are the individual machines that form the backbone of a Kubernetes cluster. They can be either master nodes or worker nodes, which are crucial in running multiple containers. Nodes can operate on both physical and vps.

#### Pods

Pods are the smallest deployable units on this platform, serving as the basic building blocks of Kubernetes applications. A pod can contain one or more containers. Kubernetes schedules these containers together on the same node to optimize communication and load balancing.

#### API server

The API server is the front end for the Kubernetes control plane, processing both internal and external requests to manage various aspects of the cluster.

#### Replication sets

These maintain a specified number of identical pods to ensure high availability and reliability. If a pod fails, the replication set automatically replaces it.

#### Ingress controllers

Ingress controllers act as gatekeepers for incoming traffic to your Kubernetes cluster, managing access to services within the cluster and facilitating external access.

These components collectively enable Kubernetes to manage the complexities of containerized workloads within distributed systems efficiently.

### Key features of Kubernetes

Kubernetes offers a robust set of features designed to meet the needs of modern containerized applications.

#### Scaling

Kubernetes dynamically adjusts the number of running containers based on demand, ensuring optimal resource utilization. This adaptability helps reduce costs while maintaining a smooth user experience.

#### Load balancing

Load balancing is integral to Kubernetes. it effectively distributes incoming traffic across multiple pods, ensuring high availability and optimal performance and preventing any single pod from becoming overloaded.

#### Self-healing

Kubernetes self-healing capabilities minimize downtime. If a container or pod fails, it is automatically replaces, keeping your application running smoothly and ensuring consistent service delivery.

#### Service discovery and metadata

Service discovery is streamlined in Kubernetes, facilitating communication between different application components. Metadata enhances these interactions, simplifying the complexities associated with distributed systems.

#### Rolling updates and rollbacks

Kubernetes supports rolling updates to maintain continuous service availability. If an update causes issues, reverting to a previous stable version is quick and effortless.

#### Resource management

Kubernetes allows for precise resource management by letting you define resource limits and requests for pods, ensuring efficient use of CPU and memory.

#### ConfigMaps, Secrets and environment variables

Kubernetes uses ConfigMaps and Secrets for secure configuration management. These tools help store and manage sensitive information such as API keys and passwords securely, protecting them from unauthorized access.

### Kubernetes pros and cons

Weighing Kubernetes strengths and weaknesses is crucial to deciding whether it is the right platform for your container management needs.

#### Scalability

- **Effortless scaling** - Kubernetes can automatically scale up by deploying additional containers as demand increases without manual intervention.
- **Zero downtime** - During deployments, Kubernetes uses a load balancer to distribute traffic across both existing and new containers, ensuring continuous service.

#### High availability

- **Automatic failover** - If a container or node fails, Kubernetes automatically redirects traffic to functional containers or nodes, minimizing downtime.
- **Loadbalancing** - Built-in load balancing spreads incoming traffic across multiple pods, enhancing performance and ensuring service availability.

#### Flexibility and extensibility

- **Custom resources and operators** - Kubernetes lets you create custom resources and operators, extending its core functionality to meet specific business needs better.
- **Best of breed ideas** - The open-source nature and string community support foster a rich ecosystem of extensions and tools. This enables continuous improvement of your Kubernetes environment with various add-ons, from monitoring solutions to external access tools.

### Disadvantages of Kubernetes

While Kubernetes is a robust platform, it has certain drawbacks you should consider.

#### Complexity

The steep learning curve of Kubernetes can be a hurdle, particularly for new users. Expertise in managing Kubernetes clusters is essential to unlocking its capabilities.

#### Resource intensiveness

Kubernetes demands significant server resources, including CPU, memory, and storage. For smaller applications or organizations with limited resources, this can lead to more overhead than benefits.

#### Lack of native storage solutions

Kubernetes doesn't include built-in storage solutions, posing challenges for applications requiring persistent or sensitive data storage. As a result, you need to integrate external storage solutions, such as network-attached storage (NAS), storage area networks (SAN), or cloud services.

### How to deploy applications on Kubernetes

#### Choose a deployment method

- **Local environment** - Ideal for learning, testing, and development, deploying Kubernetes on a local machine can be done using tools like Minikube and Kind (Kubernetes in Docker). This method is quick and convenient for individuals and small teams, although it may require more resources than other methods.
- **Self-hosted Kubernetes** - This method involves setting up and managing your own Kubernetes cluster from scratch. It offers more control and flexibility but requires significant time and expertise, making it suitable for larger organizations with complex infrastructure needs or specific compliance requirements.
- **Managed Kubernetes services** - For most production workloads and larger-scale projects, consider opting for a managed service like Amazon EKS, Google Kubernetes Engine (GKE), or Azure Kubernetes Service (AKS). These services are known for their ease of use and robustness, providing scalability and reliability without the administrative overhead.
