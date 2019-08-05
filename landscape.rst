2. Platform landscape
=====================
.. image:: images/services.png 
            :height: 400px


The Akachain Plaform includes 3 core layers as depicted in the picture above.

2.1. Blockchain core
--------------------

At the lowest layer, the Akachain platform runs on top of **Hyperledger Fabric**. 
Each business runs their own private Distributed Ledger, either hosted on Amazon Web Service or on-premise cloud. 

Akachain provides centralized Ordering Service using Apache Kafka and Identity Service using RootCA to reduce the cost of running those individual ledgers. However, it is possible for businesses to run their own Ordering service on their network.

On top of the stock Hyperledger Fabric, Akachain provides several Software Development Kits (SDKs) that accelerate the process of applications development on Akachain:

**AKC - Golang SDK**: https://github.com/Akachain/akc-go-sdk, The Golang SDK offers a number libraries to help with writing Golang based chaincode on Hyperledger Fabric. Notables lirabries are: Advance Unit Test SDK, High Throughput SDK and High Secure SDK.

**AKC - NodeJS SDK**: https://github.com/Akachain/akc-node-sdk, The NodeJS SDK provides several libraries to assist writing Decentralized Applications (DApp) using NodeJS on Akachain. 

**AKC - DataSync Module**: DApp template that periodically synchronizes Hyperledger Fabric transactions to an external PostgreSQL database to allow better data analytic functions.

2.2. Middleware and Tools
-------------------------

We provide a number of pre-built Middleware and tools support developers and network administrators to develop/deploy/monitor blockchain applications and private Fabric network.

**Development Toolkit**: Take your time and try our `development tool <https://accounts.akachain.io/login?redirectUrl=https://dev.akachain.io&description=Development%20Tool>`_. The tool allows developers to write and upload their chaincode directly to Akachain Test Network with 2 peers. It also automatically exposes REST APIs for DApp to call to those deployed chaincodes.

**Wallet Management**: Akachain accounts are linked to customer public keys. Currently this tool behaves like a normal cryptocurrency wallet. It allow users having Akachain account to access several public services such as Development Toolkit Portal and Network Monitoring tools for our test network.

**Blockchain Monitoring Toolkit**: Akachain provides a number of monitoring tools such as Network Explorer, Network Log Toolkit (Kibana), Prometheus, Graphana for critical components that work out of the box. 

**Network Management Toolkit**: Akachain provides automated network management tools that support automatic application deployment on private network via CI/CD, automatic Hyperledger Fabric network deployment, chaincode installation/upgrade, etc. 

2.3. Industry Solutions and Templates
-------------------------------------

Akachain provides a number of ready-made industrial solution templates from multiple domains: Loyalty and Rewards Network, Retail Solution, Financial Services, Health and Insurance and Manufacturing Traceability. They are template solutions that have already been developed and tested on our platform. Thus, they require only a small modification to be ready for production.

Among our industrial templates, we are proud to present one of the first blockchain-based Loyalty / Rewards Network & E-Payment solution in Southeast Asia: Utop (https://utop.vn/). Utop currently has around 6000 users in Vietnam and 4000 transactions per day. Gift redeemption and payment using Utop is widely accepted at all major merchants in Vietnam.

.. note:: To get more information about our industrial solutions and templates, please send us an email at: admin@akachain.io
