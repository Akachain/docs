4. Microservice Architecture
============================

The Akachain (AKC) platform and its applications on top of AKC platform follow a microservices architecture rather than the traditional monolithic software stack. The main reason for this decision is due to the complexity of the system. There are many components that are implemented using different platform/programming languages with different network configurations and development life-cycles.

Each microservice is put inside a Docker container and is deployed entirely on AWS. However, it is straightforward to bring those to any other cloud providers or dedicated servers per customer request.

One of the main drawbacks of microservices in general is the complexity in managing all those containers. They are hard to keep track of (especially when we put auto scaling group and backup for each services) and debugging is a nightmare. Our current system in April 2019 runs ~ 100 microservices. 

For this reason, we choose Kubernetes (K8S) to improve the complexity of the system.  Kubernetes is a portable, extensible open-source platform for managing containerized workloads and services.  It allows us to automate the deployment of containerized microservices. This makes it easier to manage all of the components and microservices of Akachain Platform.

As we are using AWS exclusively, we use the implementation of K8S on AWS (Amazon EKS)

