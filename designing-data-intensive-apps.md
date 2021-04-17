## Chapter 1

### Introduction

- Data intensive apps are built from common building blocks. Many apps need to do the following:
    - Store data in **databases** so it can be retreived later
    - Remember the results of expensive operations to spead up reads using **caches**
    - Allow users to search by keyword/filters using search **indexes**
    - Send message to another process to be handled async using **stream processing**
    - Periodically crunch a large amount of data using **batch processing**

- There are many db systems with different approaches applicable for different applications (many ways of caching, building search indexes etc).
    - Which tools is most appropriate?
    - Difficult to come up with solution that a single tool cannot do alone

### Thinking about Data systems

- Many new tools have emerged that are optimized for various use cases and dont fit in traditional categories
    - Example: redis is a data store that can act as a message queue. Kafka is a message queue with database like durability
- A single tool may not be enough. Instead multiple tools are stiched together using application code.
    - If we have an application managed caching layer (such as memcached) or a text search server like elasticache it is the application code's responsibility to keep everything in sync with the database.
    - When several tools are combined to form a service, the service's interface (API) hides the implementation details from clients.
    - Now you have created a new special purpose data system from several smaller general purpose components. The data system can provide certain guarantees such as:
        - Cache will be correctly invalidated or updated on writes so clients see consistent results
- When designing a data system or service some questions that can arise are:
    - How to ensure data remains correct and complete even when things go wrong internally?
    - How do you provide consistently good performance to clients even wheen parts of the system are degraded
    - How to scale to handle increase in load
    - What does a good API for the service look like?
- The focus is on 3 concerns that are important for most software systems:
    - **Reliability:** The system should continue to work correctly performing the correct function at the desired level of performance even in the face of hardware, software or human error.
    - **Scalability:** As the system grows, there should be reasonable ways of dealing with that growth
    - **Maintainability:** Over time many people will work on the system. The system will have current and new behavior and everyone working on it should be able to do so productively.

### Reliability

- App should do what the user expected, it can tolerate mistakes or user using the app incorrectly, performance is good for the required use case under the expected load and volume and the system prevents unauthorized access/abuse.
    - Reliability is continuing to work correctly even when things go wrong
- The things that can go wrong are called faults and the systems that can cope with them are called **fault tolerant systems**
- Fault != Failure. Fault is when 1 component deviates from its spec. A failure is when the system as a whole stops providing the service
- It is impossible to reduce the probability of a fault to 0 hence we need fault tolerance to avoid failures.
    - In fault tolerant systems, we can deliberately create faults (like randomly killing processes) to see how the system reacts
    - Many bugs are due to poor error handling and by deliberately introducting faults we can ensure that the fault tolerance is continually tested increasing reliability.
        - Example: Netflix Chaos Monkey
    - There are other cases where preventing faults is better than fixing them later
        - Example: Security stuff - this cannot be undone

### Hardware Faults


























