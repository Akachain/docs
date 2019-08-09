4. Microservice Architecture
============================

The Akachain (AKC) platform and its applications on top of AKC platform follow a microservices architecture rather than the traditional monolithic software stack. The main reason for this decision is due to the complexity of the system. There are many components that are implemented using different platform/programming languages with different network configurations and development life-cycles.

Each microservice is put inside a Docker container and is deployed entirely on AWS. However, it is straightforward to bring those to any other cloud providers or dedicated servers per customer request.

One of the main drawbacks of microservices in general is the complexity in managing all those containers. They are hard to keep track of (especially when we put auto scaling group and backup for each services) and debugging is a nightmare. Our current system in April 2019 runs ~ 100 microservices. 

For this reason, we choose Kubernetes (K8S) to improve the complexity of the system.  Kubernetes is a portable, extensible open-source platform for managing containerized workloads and services.  It allows us to automate the deployment of containerized microservices. This makes it easier to manage all of the components and microservices of Akachain Platform.
For AWS environment, we use the implementation of K8S on AWS (Amazon EKS). For on-premise system, we also support deployment on VMware PKS.

4.1. Enterprise Organization
----------------------------

In the Hyperledger Fabric universe, or more general, in a consortium blockchain network model, each consortium is composed by a group of **organizations**. An organization may have multiple **peers** that simultaneously send transactions created by the organization or receive/validate/endorse transactions from other organizations. The picture below describes the organization structure of a typical consortium blockchain system using Akachain platform.

.. image:: images/slide1.png 
            :height: 400px

Following sections describe the high level microservice architecture of a typical organization in one of Akachain private consortium. Generally, we provide 2 K8S for each organization:

- Organization Blockchain Cluster: contains microservices that directly connect and interact to the blockchain  
- Organization Application Cluster: contains microservices that handle client application requests.

We now describe a more detail about each cluster architecture using Amazon EKS. On-premise cluster architecture is nearly identical similar as VMware PKS provides very similar services

4.2. Organization Blockchain Cluster
------------------------------------

.. image:: images/slide3.png 
            :height: 400px

Starting with the Blockchain Cluster, each cluster requires at least 7 microservices:

- Fabric Peer: Blockchain peer that handle interaction with the blockchain network. Currently, we suggest each organization runs 3 peers that load balanced to ensure fault-tolerance.
- CouchDB: Database instance storing blockchain ledger data
- NodeJS Daemon: Daemon process that perform various tasks in AKC-NodeJS-SDK to improve the system throughput (high throughput transaction)
- DAPP: Decentralized application that invoke Hyperledger Fabric SDK to interact with smart contracts and peers
- Explorer: Administration tool to monitor information in the blockchain network
- Data Sync: Periodically fetches or subscribes to push event from the Peer to synchronize ledger data to a Postgre SQL instance so that other services can quickly implement rich queries for reporting services.
- CA: Certificate Authority client or intermediate CA server.

Using EKS, those microservices are mapped to a number of worker nodes running T3.Large on multiple AZs. This guarantees the availability of the system.

Over the control plane of EKS, we configure the system to use Cluster Autoscaling (CA) and Horizontal Pod Auto-scaling (HPA) to auto scale the system both in term of microservices as well as worker nodes. 

We also use a number of stock AWS services:

- Aurora: PostgreSQL instances that are managed by AWS
- KMS: Key Management Service that we use to store database and disk encryption key. We also plan to use KMS to store various data encryption key that can be used in the application layer later.
- EFS: We store various configuration files on EFS that can be shared among microservices.
- EBS: Microservices in pods managed by EKS can perform a Persistent Volume Claim. This action creates a number persistent instances of EBS and mount them to those pods. Later on, if those pods are replaced, the new pod will automatically mount these persistent EBS volume.
- NLB: For GRPC communication between Peers and the Ordering services over the internet.
- ALB, NAT Gateway, Route53: Allow some services to be accessible from the internet through the ingress controller module of EKS (in this case, the explorer web server)

From the networking point of view. Each cluster is mapped to one VPC. There are multiple private subnets and public subnets accross multiple AZ and only the one that needs to be expose to the internet go into the public subnet.

4.3. Organization Application Cluster
-------------------------------------

.. image:: images/slide4.png 
            :height: 400px

The Application Cluster follows the same architecture as the blockchain cluster. There is only a slight different in terms of microservices. Overall, we have 2 main group of microservices:

- Access Control : Microservice handles authentication and authorization for front end applications
- Application - API: Everything that serves front end applications

Inside each group, we can have multiple different microservices such as authentication, user management, token management, etc. They can be bundled together into one pod or splited up as we see fit.

A slight note here is that the Application Cluster uses the same VPC as the Blockchain Cluster. The main reasons is to allow some microservices to call each other without going through the NAT gateway as well as to share some AWS service together.

We also use MySQL in some microservices in the application cluster

4.4. Interaction between clusters
---------------------------------

.. image:: images/slide2.png 
            :height: 400px


From here, we can present how those different clusters and microservices work together in a multi tier architecture

In the Front-end tier, we can have Web Portals and Mobile applications that are used directly by consumers.

The backend services for those applications resides in the Application cluster. They connect to 2 databases MySQL and PostgreSQL managed by Amazon Aurora. We also see here a link from the Application - API to potential external systems such as on premise existing systems either through the internet or a VPN connection.

The Application API also has a direct connection to the DAPP microservice in the Blockchain Cluster. The DAPP will relay request from the application cluster and create appropriate transaction through chaincode/smart contract deployed on Fabric peers to interact with the blockchain network.

The Data Sync service in the blockchain cluster subscribes to various blockchain events and pushes data to the PostgreSQL which later on is consumed by APIs in the Application Cluster. This way, we have a very reponsive cache that serves complex queries without worrying the peer is overloaded.

4.5. Interaction among organizations

.. image:: images/slide5.png 
            :height: 400px

By joinning a blockchain channel, all peers in the blockchain clusters can install Chaincodes and start communicating with each others.

Peers communicate directly with each other using a gossiping protocol. They also connect to the Ordering Service (Kafka + zoo keeper) managed by Akachain Blockchain Cluster to submit transactions and fetch confirmed blocks. This part follow the standard Hyperledger Fabric communication model.