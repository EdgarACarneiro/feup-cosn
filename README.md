# feup-cosn

Summary of the contents lectured in 'Cloud and Service Oriented Computing', a course from the Master in Software Engineering @FEUP.

## Index
* [Introduction](#introduction)
  * [Basic Concepts](#basic-concepts)
  * [Evolution of cloud platforms](#Evolution-of-cloud-platforms)

## Introduction
### Basic Concepts
* Computing and storage as a service
  * Computing and storage resources providing an application platform as a service
    * Utility Pricing
    * Elastic Resource capability
    * Virtualized Resources
    * Management Automation
    * Self-service provisioning
    * ...
* Different types of services can be offered, namely:
  * __Infrastructure as a Service (IaaS)__
    * Offer computing infrastructures (_e.g_ virtual machines)
    * High-level APIs used to dereference various low-level details of underlying network infrastructure
    * Hypervisor (Virtual Machine Monitor) is responsible for loading virtual machines
    * Other examples of IAAS: disk-image library, firewalls, loadbalancers, VLANs, software bundles
  * __Platform as a Service (PaaS)__
    * Platform allowing customers to develop, run, and manage applications
    * Discards complexity of building and maintaining the infrastructure
    * Software deployment controlled with minimal configuration options
    * Provider provides the networks, servers, storage, operating system (OS), middleware (e.g. Java runtime, .NET runtime), database and other services to host the consumer's application.
  * __Software as a Service (SaaS)__ aka "on-demand software"
    * Access to application software and databases over the Internet
    * Providers manage the infrastructure and platforms that run the applications
    * Usually priced on a pay-per-use basis or using a subscription fee
  * __Function as a Service (FaaS)__
    * Platform allowing customers to develop, run, and manage applications
    * Complete abstraction of servers away from the developer
    * FaaS vs PaaS
      * PaaS: deploy an entire application
      * FaaS: deploy what is essentially a single function, or part of an application
      * FaaS: designed to potentially be a __serverless architecture__
* __Serveless Computing__
  * Provider dynamically manages the allocation of machine resources
  * Pricing based on the actual amount of resources consumed by an application
  * Server management and capacity planning decisions are completely hidden from the developer
  * Can be used in conjunction with code deployed in traditional styles, such as micro-services
* __Types of cloud__
  * ![Imgur](https://i.imgur.com/jPwN1pB.png)
* __Cloud Features__
  * __Elasticity__
    * Self-managing system. Users only inputs the desired policies
    * Provides agility and adaptability to environment changes
    * Implies horizontal and vertical scalabilities
      * Horizontal: adding more machines/ resources
      * Vertical: adding more power (_e.g.:_ CPU, RAM) to existing machines
  * Reliability and Availability
    * Ensures constant operation through redundant resource usage (_e.g.:_ fault tolerance)
    * Loag-balancing -> Ability to deal with increasing concurrent access
  * Quality of Service
    * Services meet users requirements (_e.g.:_ response time)
  * Pay per use
    * Services sold as Utility Computing
    * Costs according to actual resource consumption
  * Going Green
    * Reduce energy consumption -> Reduce costs & carbon footprint
* __Virtualization__ is essential in the Cloud
  * Provides all the cloud features (_e.g.:_ ease of use, flexibility and adaptability, location independence, etc.)

Summarizing in an image:
![Imgur](https://i.imgur.com/byxRwGP.png)

### Evolution of cloud platforms
* __Serverless__ (or Functions as a Service (FaaS)) is the culmination of several iterations
* The evolution began with physical metal in the data center and progressed through Infrastructure as a Service (IaaS) and Platform as a Service (PaaS).
* Before the cloud, to deploy one had to answer:
  * What hardware should be installed?
  * How is the physical access to the machine secured?
  * Where are storage backups sent?
  * ...
1. IaaS
  * Still requires heavy overhead because staff are still responsible for various tasks
    * Patching and backing up servers;
    * Installing packages;
    * Keeping the operating system up-to-date;
    * Monitoring the application.
2. PaaS
  * reduces the overhead
    * cloud provider handles operating systems, security patches, and even the required packages to support a specific platform
    * Instead of building VM, developers now user "platform targets"
  * Questions are reduced to:
    * What size services are needed?
    * How do the services scale horizontally?
    * And vertically?
3. Serverless
  * Abstracts servers by focusing on event- driven code.
  * Developers focus on a microservice that does one thing (instead of platform)
  * Questions are:
    * What triggers the code?
    * What does the code do?
* Billing
  * IaaS and PaaS
    * Pay to host the endpoints even when they aren't being accessed
  * Serverless
    * micro-billing
      * scale each endpoint independently
      * pay for usage
      * no costs are incurred when the APIs aren't being called
