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
  * [Monolith Architecture](#monolith-architecture)
  * [Service Oriented Architecture (SOA)](#service-oriented-architecture-(soa))
  * [Microservices Architecture](#microservices-architecture)
* [Integrating Services - Part I](#integrating-services---part-i)
  * [API Gateway](#api-gateway)
  * [Resiliency](#resiliency)
* [Integrating Services - Part II](#integrating-services---part-ii)
  * [Observability](#observability)
  * [Security](#security)

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
    * Netflix Falcor provides similar function

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
* Architecure Evolution
  * Monolith
  * SOA (2000s)
  * Microservices (2010s)

### Monolith Architecture
* Contained in a single deployment
* Everything, from user interface to database calls, is included in the same codebase
* Good for __relatively small__ applications
* __Advantages__
  * Easier to pull down a single code base and start working
  * Ramp up time may be less
  * Creating test environments is as simple as providing a new copy
  * It may be designed to include multiple components and applications
* __Disadvantages__
  * Difficult to work in parallel in the same code base
  * Any change, no matter how trivial, requires deploying a new version of the entire application
  * Refactoring potentially impacts the entire application -> tight coupling
  * Often the only solution to scale is to create multiple, resource-intensive copies of the monolith
  * Integration can be difficult
  * Difficulty to test due to the need to configure the entire monolith
  * Code reuse is challenging and often other apps end up having their own copies of code
  * Hard to apply agile development
  * Single point of failure
  * Difficult to adopt new technologies and frameworks, as all the functionalities have to build on homogeneous technologies/ frameworks
* __N-Layer applications__
  * Partition application logic into specific layers
  * Most common layers
    * UI Layer
    * Business Logic Layer
    * Data access Layers
  * Advantages
    * Refactoring is isolated to a layer
    * Teams can independently build, test, deploy, and maintain separate layers
    * Layers can be swapped out
* Visual Schema
  * ![Imgur](https://i.imgur.com/9ec3q0S.png)
    
### Service Oriented Architecture (SOA)
* Applications are composed of more loosely coupled components that use a __messaging bus__ to communicate between themselves
* Services -> __reusable, loosely coupled__ entities
  * Self-contained implementation of a well-defined __business functionality__
  * Acessible via calls over the network
  * Software components with well-defined interfaces that are implementation-independent. Separation of the interface (the what) from its implementation (the how).
  * Consumers are only concerned about the service interface and do not care about its implementation.
  * Composite services can be built from aggregates of other services.
  * Deployed inside an application server
* Requires an additional layer - __Enterprise Service Bus (ESB)__
  * Integrates business capabilities (product, customer, ...) -> __creates composite business capabilities__, exposed to the consumers
  * Contains a significant portion of the business logic of the entire application
  * __Monolithic entity__ where all developers share the same runtime to develop/deploy their service integrations.
  * Smart Pipes
* API Gateway
  * Difficulty in interact with SOAP, which leads to
  * Layer on top of the existing SOA implementations
  * Known as the __API façade__
  * Exposes a simple APIfor a given business functionality and hides all the internal complexities of the ESB/Web Services layer
  * Also used for security, throttling, caching and monetization
* Visual Schema
  * ![Imgur](https://i.imgur.com/qAOMF68.png)
  * ![Imgur](https://i.imgur.com/GpNsKHr.png)

### Microservices Architecture
* Independent application services delivering one single functionality in a loosely connected and self-contained fashion, communicating through a light-weight protocol (e.g. HTTP, REST, ...)
* More details in previous chapters
* Visual schema
  * ![Imgur](https://i.imgur.com/Vgot7gC.png)
* __Characteristics__
  * __Business Capability Oriented__
    * Service a specific business purpose a well-defined set of responsibilities
    * Each service does only one thing and does it well
    * SOA weill have more generic services, while here we have fine-grained services
  * __Autonomous__: Develop, Deploy, and Scale Independently
    * Microservices are developed, deployed, and scaled as independent entities
    * Services do not share the same execution time
    * Increases system resilience due to isolation of failures to service level;
    * Can scale microservices according to each microservice traffic
  * __Elimination of the central ESB__ by breaking its functionalities into each service
  * Services take care of the inter-service communication and composition logic
    * Using __smart endpoints and (dumb pipes__ or lightweight protocols like REST)
      * Smart endpoints
        * All business logic resides at micro service level
      * Dumb pipes
        * Only __route messages__
        * Zero business logic
  * __Failure tolerance__
    * One microsrevice crashes -> only it collapses
    * Need to apply all the resiliency-related capabilities, such as circuit breakers, disaster recovery, load- balancing, fail-over, and dynamic scaling based on traffic patterns
  * __Decentralized data management__
    * __Each microservice has its own database__
    * Several databases might need to be updated as a consequence of a single API request
    * Microservices update each other using the APIs
    * A microservice can only access its own database
  * __Service Governance__
    * __Decentralizede process__ -> each entity govens its own domain
      * In -soa this concepts are discarded
    * Design-time governance of services
      * Technologies, protocols, ...
    * Runtime governance
      * Service definitions, service registry and discovery, service versioning, service runtime dependencies, service ownerships and consumers, enforcing QoS, and service observerability
  * __Service Observerability__
    * combination of monitoring, distributed logging, distributed tracing, and visualization of a service’s runtime behavior and dependencies (as seen in previous chapters)
    
## Integrating Services - Part I
* In a monolith approach, a request may be satisfied with a single call
* With microservices, we may need to access multiple services to satisfy the same request
  * External access has higher latency
* Invoking the services directly (_e.g._ web service calling microservices) have the following problems:
  * __Multiple requests__ to retrieve the data needed
    * inefficient
    * can result in a poor user experience;
  * The __lack of encapsulation__
    * caused by clients knowing about each service and its API
    * difficult to change the architecture and the APIs
    * developers might change an API in a way that breaks existing clients
    * Updating client’s app is more cumbersome
  * Services might use __communication mechanisms__ that aren’t convenient or practical for clients to use

### API Gateway
* __Entry point service__ into the microservices-based application from external API clients
* __Encapsulates__ the application’s internal architecture and provides an API to its clients
* May also have other responsibilities, such as __authentication__ and __monitoring__
* Visual impact
  * ![Imgur](https://i.imgur.com/fKkOoJi.png)
* Responsible for
  * Request routing (to a service)
  * API composition (invokes multiple services)
  * Protocol translation
    * Client protocol may be different from the services (_e.g._ REST and RPC)
* May have 3 API modules
  * __Mobile API__
    * API for the mobile client
  * __Browser API__ 
    * API for the Js app runinng on the browser
  * __Public API__
    * API for third party developers
* Why provide __each client with its own API__?
  * _e.g._ -> mobile apps present less information than browsers
  * Higher reliability
  * Independently scalable
* Architecture Disadvantages
  * __yet another highly available component__ that must be developed, deployed, and managed
  * __risk__ that the API gateway becomes a development __bottleneck__
    * Developers must update the API gateway to expose their services’s API
    * The process for updating the API gateway must be as lightweight as possible
* Design issues
  * Performance and scalability
    * Every external request goes through the gateway
    * Synchronous I/O model
      * Each connection is handle by a dedicated thread
      * OS threads are heavyweight
      * Limit on the number of threads
    * Asynchronous (nonblocking) I/O model
      * Single thread dispatches I/O requests to event handlers
      * More scalable but more complex programming
  * Writing maintainable code by using reactive programming abstractions
    * Calling services sequentially
      * The response time is the sum of all services response times
      * If there is no dependencies among services, all services should be __called concurrently__
        * Solution: __REACTIVE APPROACH__
          * Java 8 CompletableFutures
          * RxJava (Reactive Extensions for Java) Observables, created by Netflix specifically to solve this problem
          * Scala Futures
          * JavaScript promises, RxJS (for NodeJS)
  * Handling partial failure
    * Properly handle failed requests and requests that have unacceptably high latency.
      * Circuit breaker pattern when invoking services
  
### Resiliency
* __measure of the capacity__ of a system or individual components in a system to __recover quickly from a failure__
* attribute of a system that enables it to __deal with failure in a way that doesn’t cause the entire system to fail__ (other definition)
* __microservices__ architecture is naturally a __distributed system__
  * collection of computes (or nodes) connected over the network, with no shared memory, that appears to its users as a single coherent system
  * __Network will always be unreliable__
* __Patterns__
  * Timeout
    * deciding when to stop waiting for a response at the caller service level
    * After that time some action is taken
    * Isolate failures
      * Isolate failures
        * Service A (integrator) calls services X and Y
          * A timeout should be defined separately for X and Y
        * A failure of another service does not have to become your service’s problem
  * Circuit Breaker
    * Wrap the invocation with an object that monitors and prevents further damage to the system
    * If the service invocation __fails repeatedly__ and reaches a certain __threshold__, then the circuit breaker wrapper __prevents any further invocation__ by the external service
    * After a certain period of time (circuit reset timeout), a __new request is allowed__ (Half Open state). If it succeeds the breaker goes to the closed state (allows requests), and to the open state (does not allow requests) otherwise
    * Prevents cascade failures
    * Visual schema
      * !
  * Fail fast
    * detect a failure as quick as possible
    * Concept
      * a failure response is much better than a slow failure response
  * Bulkhead
    * Application is partitioned so that an error that occurs in a partition is localized to that one partition only, avoiding leading the entire system to a fail state
    * Procedure
      * Group similar operations into a microservice
      * Independent business functionalities are implemented as separate microservices
    * Following the microservices architecture ensures this pattern
  * Load Balancing
    * Distribute the load across multiple microservice instances
    * Kubernetes (_e.g._) provides this capability
  * Failover
    * Reroute requests to alternate services if a given service fails
    * Kubernetes (_e.g._) provides this capability
  * Let it crash
    * If service is unstable and recovery difficult -> start new server instance
    * Having a service per host and a rapid server startup time is crucial (container based)

## Integrating Services - Part II
### Observability

### Security
