# feup-cosn

Summary of the contents lectured in 'Cloud and Service Oriented Computing', a course from the Master in Software Engineering @FEUP.

## Index
* [Introduction](#introduction)
  * [Basic Concepts](#basic-concepts)
  * [Evolution of cloud platforms](#evolution-of-cloud-platforms)
* [Designing an application with a microservice architecture - Part I](#designing-an-application-with-a-microservice-architecture---part-i)
* [Designing an application with a microservice architecture - Part II](#designing-an-application-with-a-microservice-architecture---part-ii)
  * [Decomposition](#decomposition)
  * [Service API](#service-api)
  * [Microservices Design Principles](#microservices-design-principles)
* [Inter-Service Communication - Part I](#inter-service-communication---part-i)
  * [Synchronous communication](#synchronous-communication)
* [Inter-Service Communication - Part II](#inter-service-communication---part-ii)
  * [Asynchronous communication](#asynchronous-communication)
* [Software Architecture Patterns](#software-architecture-patterns)

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
  
__1. IaaS__
  * Still requires heavy overhead because staff are still responsible for various tasks
    * Patching and backing up servers;
    * Installing packages;
    * Keeping the operating system up-to-date;
    * Monitoring the application.
    
__2. PaaS__
  * reduces the overhead
    * cloud provider handles operating systems, security patches, and even the required packages to support a specific platform
    * Instead of building VM, developers now user "platform targets"
  * Questions are reduced to:
    * What size services are needed?
    * How do the services scale horizontally?
    * And vertically?
    
__3. Serverless__
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

## Designing an application with a microservice architecture - Part I
* Key idea
  * Application as a set of services instead of one large application
  * A service is a standalone, independently deployable software component that implements some useful functionality
  * Each service is deployed separately and they communicate through well-defined network-based interfaces
* __Hexagonal architecture style__
  * Alternative to the layered architectural style (UI Logic -> Business Logic -> Data Access Layer)
  * Puts the business logic at the center
  * Instead of the UI layer, the application has __one or more inbound adapters__ that handle requests from outside and invoke the business logic (center of the hexagon)
  * Business logic independent of the adapters
  * Decoples business logic from UI and data acess logic in the adapters
* __Desinigning with microservice architecture__
  * Identify system operations -> Identify services -> Define APIs and collaborations
  * __Identify system operations__
    * Identify the application's requirements (_aka_ User Stories and associated user scenarios) -> ___macro-architecture___
    * A requirement / external request will map to a system operation
    * A system operation is an __abstraction of a request that the application must handle__
      * Can be a __command__ -> update data (create, update, delete)
        * specified by the parameters, return value and behaviour
        * Behaviour:
          * Specifies the __preconditions__ that must be true before invoke the operation
          * Specifies __post-conditions__ that are true after invoking the operation
      * Can be a __query__ -> retrivies data (get)
    * Two steps:
      * Create a high-level domain model (identify key classes)
        * __nouns__ of the user stories
        * simpler than the fina implementation
        * The application __won’t even have a single domain model__ because each service has its own domain model
        * Useful for defining vocabulary for describing behaviour / system operations
      * Describe operations using those classes
        * __verbs__ of the user stories

## Designing an application with a microservice architecture - Part II
### Decomposition
* __Decomposition__ strategies
  * by business capability pattern
    * Organizes services around business capabilities
  * by subdomain pattern
    * Organizes services around domain-driven design (DDD) subdomains
* __Decompose by business capability__
  * A __business capability__ is something that a business does in order to __generate value__.
  * Business capabilities define __what__ an organization does independently of _how it does_.
    * Capabilities are stable over time
    * How capabilities are executed may change over time
    * _e.g.:_ deposit that previosuly was in checks now is ATM, bu it is still a deposit
  * Based on:
    * Organization purpose
    * Structure
    * Business processes
  * In our project we followed this decomposition
  * From business capabilities to services
    * A capability -> A Service, AND/ OR
    * A group of capabilities -> A Service
    * The process is subjective
      * Dependends on granularity and similarity among domain objects
    * Iterative process
      * As we learn more about the application domain, we may change the architecture
    * _e.g.:_
      * ![Imgur](https://i.imgur.com/yFxvPrG.png)
* __Decompose by subdomain pattern__
  * DDD - domain driven design
    * Aligned with the internal company organization
    * Domain is related to the business
    * One department/sub-domain -> One Service
  * DDD concepts
    * Subdomains
      * A department can have multiple sections, and each section may be a sub-domain
    * __Bounded contexts__ (scope)
      * Explicit boundary within which a domain model exists
    * DDD vs global business modelling
      * No single model for the entire business
      * The domain model __is private__ to the sub-domain and other sub-domains __do not have to agree__ with the model developed.
> Must understand the difference between the two decompositions
* __Notes__
  * A good design will scope out __one microservice to a single bounded context__
  * The SOA (Service Oriented Architecture) approach would model the enterprise as a whole
  * Communication between microservices can happen via events:
    * __Events__ are triggered as a result of __state changes__ in bounded contexts
    * Like we did in the project
* __Decomposition guidelines__
  * Single Responsibility Principle
    * A class/ service should have only one reason to change
    * If a class/ service has multiple responsibilities that change independently, it won’t be stable
  * Common Closure Principle
    * The classes in a package should be closed together against the same kinds of changes. A change that affects a package affects all the classes in the package
    * Two classes change in lockstep -> same package
    * Improves the maintainability of application
* __Decomposition obstacles__
  * Network latency
    * Too many messages between two services
  * Reduced availability due to synchronous communication
    * REST is synchronous and may become a bottleneck
  * Maintaining data consistency across services
    * Some system operations need to update data in multiple services.
    * atomic updates -> data reside within a single service
  * Obtaining a consistent view of the data
    * Although each service’s database is consistent, we can’t obtain a globally consistent view of the data.
    * Consistent view needed -> must reside in a seginle service
  * __God classes__ preventing decomposition:
    * Classes that are used throughout an application
    * Tipically implements __business logic for many different aspects of the application__
    * Normally has a __large number of fields__ mapped to a database table with many columns
    * __Central concept__ in the application domain
    * DDD solution
      * __Treat each service as a separate sub-domain__ with its own domain model (_i.e.:_ a version of the God class).
      * _e.g.:_ an Order for the kitchen is a ticket, for the delivery an adress, ...

### Service API
  * __Operations__
    * A system operation
    * Collaborative operation between services
  * __Events__
    * State changes
    * Notifications
  * Step 1
    * Assigning System Operations to Services
      * Which service is the initial entry point for a request?
      * Assign an operation to a service that __needs the information__ provided by the operation
      * Assign an operation to the service that __has the information required__ to handle it
  * Step 2
    * Defining the APIs required to support collaboration between services
      * Which services will I need to collab with?
  * __Context map__
    * Help visualize the relationships between bounded contexts.
    * The __upstream__ context defines the domain model passed between the two contexts.
    * The __downstream__ context should be well aware of any changes happening to the upstream context.
    * _e.g.:_
      * ![Imgur](https://i.imgur.com/kzPFuhz.png)
  * Notes
    * Each service should represent a Business Logic (___Is it not this decomposition by BC? Confirm with them___)
    * A data service is probably a bad design
    
### Microservices Design Principles
* __High Cohesion and Loose Coupling__
  * AVOID: one microservice to address two or more unrelated problems
  * Highly cohesive system is naturally loosely coupled. Coupling is a measure of the interdependence between different microservices
* __Resilience__
  * Measure of the capacity of a system / individual components in a system to recover quickly from a failure
  * Doesn’t cause the entire system to fail
  * Implementation:
    * Timeouts for calls over the network
    * Circuit Breaker
      * microservice keeps timing out against one endpoint all the time -> no point keep trying, at least for some time -> wrapper circuit breaker does this
      * automatic error responses for services exceeding a failure threshold in the recent past
* __Observability__
  * combination of monitoring, distributed logging, distributed tracing, and visualization of a service’s runtime behavior and dependencies
  * May track throughput of each microservice, the number of success/failed requests, utilization of CPU, memory, latency and some business-related metrics
  * How to achieve?
    * Logging
      * record events
    * Metrics
      * latency, ... obtained by processing the logged events
    * Tracing
      * consider the event logs ordering
      * Allows to trace a problem (_e.g._ high latency)
* __Automation__
  * rational behind a microservice architecture -> less time to production and shorter feedback cycles
  * Two categories
    * Continuous integration
      * Focus on maintaining maintain source code integrity
    * Continuous deployment
      * bundle applications, infrastructure, middleware, and the supporting installation processes and dependencies into release packages

## Inter-Service Communication - Part I
* Services are autonomaus
* Services communicate over the network
* A __service based application__ can be considered a distributed system running __multiple services__ on __different network locations__.
* Communication types:
  * __Synchronous__
    * The client sends a request and waits for response from the service. Both parties have to keep the connection open until response arrives.
    * Can be a non-blocking IO implementation, using callbacks
  * __Asynchronous__
    * Send message and proceed without waiting for response
### __Synchronous communication__
* __REST__ (Representational State Transfer)
  * Uses a navigational scheme to represent objects and services over a network, known as resources.
  * Not protocol dependent
  * With the HTTP protocol, a resource is accessed using a unique URL and the standard HTTP operations __GET, PUT, DELETE, POST__, and __HEAD__
  * __Stateless__ servers.
* __RPC__ (Remote Procedure Calls)
  * Key objective
    * Make the process of executing code on a remote machine as simple and straightforward as calling a local function
  * Lost popularity due to complexity and performance
  * __gRPC__
    * Developed by Google -> now Open Source
    * Uses __Protocol Buffers__: a language-neutral, platform-neutral extensible mechanism for serializing structured data
    * __Interface Definition Language__ (IDL) describe both the service interface and the structure of the payload messages
    * USes __server-side skeletions__ and __client-side stubs__ to invoke the service
    * Uses __HTTP2__ as the transport protocol -> key reason for the success and wide adaptation of gRPC
      * Advantages
        * __Multiple requests__ in the same open connection
        * __Lower overhead__ due to less redundancy over several requests (e.g. Cookies)
        * __Avoids header repetition__ -> introduces header compression to optimize bandwidth use
* REST - Richardson Maturity Model
  * Level 0
    * Not considered RESTful at all
    * Single URL for all resources and the content decides the operation
    * Single HTTP method (in most cases, POST) 
    * SOAP web services are of this kind
  * Level 1 - Resource URIs
    * Has individual URIs for each resource, but
    * The message still contains operation details
  * Level 2 - HTTP verbs
    * HTTP verbs to specifiy operation
    * RESTful service consider to be a proper REST API
  * Level 3 - Hypermedia Controls
    * Service responses have links that control the application state for the client (Hypertext as The Engine of Application State
    * Hypermedia controls tell us what we can do next, and the URI of the resource we need to manipulate to do it
* REST - Message formats
  * JSON
  * GraphQL
    * Improves the REST model by allowing to retrieve multiple data in a single call
    * JSON format

## Inter-Service Communication - Part II
### __Asynchronous communication__
* Allows the services to be __more autonomous__
* The client __does not wait__ for a response
* The client may note receive a __response__ at all or the response will be received asynchronously __via a different channel__
* __Middleware__: lightweight and __dumb__ message broker
  * There is __no business logic__ in the broker and it is a centralized entity with high-availability
* Message Protocols
  * JSON
  * XML
  * Apache AVRO
    * Compact, fast, binary data format
* __Messaging styles__
  * Single receiver
    * A given message is __reliably__ delivered from a producer to exactly one consumer through a message broker
  * Multiple receivers
    * Message produced by a single producer is delivered to more than one consumer
    * ___Publisher-subscriber___ pattern (pub-sub)
    * AMQP-based brokers support pub-sub messaging, _e.g._ RabbitMQ
    * ___Kafka___ is the __most widely used__ broker for _pub-sub_ messaging between microservices
* __AMQP__ - Advanced Message Queuing Protocol
  * Protocol for __interoperability__ between all messaging middleware
  * Ensures __reliability of message delivery__, fast and message acknowledgements
    * When a message is delivered to a consumer, the __consumer notifies the broker__
    * The broker will only completely remove a message from a queue when it receives a notification for that message
    * The queue ensures the ordered delivery and processing of the messages
  * AMQP Message Brokers: Software that implements the protocol (RabbitMQ, ActiveMQ, ...)
  * _e.g._
    * ![Imgur](https://i.imgur.com/3AOfcXW.png)
  * Features
    * _heartbeat_ / healtcheck
      * To ensure that the application layer promptly finds out about disrupted connections and completely unresponsive peers
    * Broker failures
      * AMQP standard defines a concept of durability for exchanges, queues, and persistent messages, requiring that a durable object or persistent message survive a restart
    * Producer failures
      * __Retransmit__ any messages for which an acknowledgement has not been received from the broker
      * Possibility of __message duplication__: consumer applications need to be implement in a way that __internal state doesn’t change__ even if the same message is processed multiple times.
  * RabbitMQ
    * General purpose message broker, employing several variations of __point to point__, __request/reply__ and __pub-sub__ communication styles patterns.
    * __Smart broker / dumb consumer__ model
    * _e.g_
      * [Imgur](https://i.imgur.com/aC1Wjc5.png)
* __Kafka__
  * Distributed pub-sub messaging system, designed for high volume messages
  * Has its own messaging protocol
  * __Data is stored durably__, in order and can be read deterministically
  * Data is distributed for __failover__ and __scalability__
  * Unit of data is a __message__ (key, value, timestamp)
    * __Value__ is an array of bytes
    * Messages are organized in __Topics__
      * Topics may be split into multiple __partitions__
        * A producer can select a partition by using a __key__
  * Partitions are the primary mechanism in Kafka for parallelizing consumption and __scaling a topic__ beyond the throughput limits of a single node
  * __Each partition can be hosted in different nodes__
  * No need to specify a partition to write, by default
  * __Dumb broker__ and assumes __smart consumers__ to read its buffers
  * Use cases:
    * Distribute a message to multiple receivers
      * ![Imgur](https://i.imgur.com/T2DlHL9.png)
    * Similar receivers consume messages, from the same topic, for scaling the consumer side
      * ![Imgur](https://i.imgur.com/aPMjye5.png)
  
## Software Architecture Patterns
