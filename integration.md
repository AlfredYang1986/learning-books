---
description: >-
  Getting integration right is the single most important aspect of the
  technology associated with microservices in my opinion.
---

# Integration

## Looking for the Ideal Integration Technology

There is a bewildering array of options out there for how one microservice can talk to another. But which is the right one

* SOAP
* XML-RPC
* REST
* Protocol buffers

### Avoid Breaking Changes

Every now and then, we may make a change that requires our consumers to also change. We want to pick technology that ensures this happens as rarely as possible. For example, if a microservice adds new fields to a piece of data it sends out, existing consumers shouldn't be impacted.

### Keep Your APIs Technology-Agnostic

This means avoiding integration technology that dictates what technology stacks we can use to implement our microservices.

### Make Your Service Simple for Consumers

We want to make it easy for consumers to use our service. Ideally, we'd like to allow our clients full freedom in their technology choice, but on the other hand, providing a client library can ease adoption. Often, however, such libraries are incompatible with other things we want to achieve.

### Hide Internal Implementation Detail

We don't want our consumers to be bound to our internal implementation. This leads to increased coupling. This means that if we want to change something inside our microservice, we can break our consumers by requiring them to also change. That increases the cost of change -- the exact result we are trying to avoid. It also means we are less likely to want to make a change for fear of having to upgrade our consumers, which can lead to increased technical debt within the service. So any technology that pushes us to expose internal representation detail should be avoided.

## Interfacing with Customers

## The Shared Database

By far the most common form of integration that I see in the industry is database integration. 

First, we are allowing external parties to view and bind to internal implementation details. The data structures I store in the DB are fair game to all; they are shared in their entirety with all other parties with access to the database. If I decide to change my schema to better represent my data, or make my system easier to maintain, I can break my consumers. The DB is effectively a very large, shared API that is also quite brittle. If I want to change the logic associated with, say, how the help desk manages customers and this requires a change to the database, I have to be extremely careful that I don't break parts of the schema used by other services. This situation normally results in requiring a large amount of regression testing.

Second, my consumers are tied to a specific technology choice. 

Finally, let's think about behavior for a moment. There is going to be logic associated with how a customer is changed. If consumers are directly manipulating the DB, then they have to own the associated logic. The logic to perform the same sorts of manipulation to a customer may now be spread among multiple consumers. Goodbye, cohesion.

Database integration makes it easy for services to share data, but does nothing about sharing behavior. Our internal representation is exposed over the wire to our consumers, and it can be very difficult to avoid making breaking changes, which inevitably leads to a fear of any change at all. 

## Synchronous Versus Asynchronous

With synchronous communication, a call is made to a remote server, which blocks until the operation completes. With asynchronous communication, the caller doesn't wait for the operation to complete before returning, and may not even care whether or not the operation completes at all. 

Synchronous communication can be easier to reason about. We know when things have completed successfully or not. Asynchronous communication can be very useful for long-running jobs, where keeping a connection open for a long period of time between the client and server is impractical. It also works very well when you need low latency, where blocking a call while waiting for the result can slow things down. Due to the nature of mobile networks and devices, firing off requests and assuming this have worked can ensure that the UI remains responsive even if the network is highly laggy. On the flipside, the technology to handle asynchronous communication can be a bit more involved.

These two different modes of communication can enable two different idiomatic style of collaboration: request/response or event-based. 

With request/response, a client initiates a request and waits for the response. This model clearly aligns well to synchronous communication, but can work for asynchronous communication too. I might kick off an operation and register a callback, asking the server to let me know when my operation has completed.

With an event-based collaboration, we invert things. Instead of a client initiating requests asking for things to be done, it instead says this thing happened and expects other parties to know what to do. We never tell anyone else what to do. Event-based systems by their nature are asynchronous. The smarts are more evenly distributed -- that is, the business logic is not centralized into core brains, but instead pushed out more evenly to the various collaborators. Event-based collaboration is also highly decoupled. The client that emits an event doesn't have any way of knowing who or what will react to it, which also means that you can add new subscribers to these events without the client ever needing to know.

So are there any other drivers that might push us to pick one style over another? One important factor to consider is how well these styles are suited for solving an often complex problem: how do we handle processes that span service boundaries and may be long running?

## Orchestration Versus Choreography

As we start to model more and more complex logic, we have to deal with the problem of managing business processes that stretch across the boundary of individual services. And with microservices, we'll hit this limit sooner than usual. 

When it comes to actually implementing this flow, there are tow styles of architecture we could follow. With orchestration, we rely on a central brain to guide and drive the process, much lick the conductor in an orchestra. With choreography, we inform each part of the system of its job, and let it work out the details, like dances all finding their way and reacting to others around them in a ballet.

In general, I have found that systems that tend more toward the choreographed approach are more loosely coupled, and are more flexible and amenable to change. You do need to do extra work to monitor and track the processes across system boundaries, however, I have found most heavily orchestrated implementations to be extremely brittle, with a higher cost of change. With that in mind, I strongly prefer aiming for a choreographed system, where each service is smart enough to understand its role in the whole dance.

There are quite a few factors to unpack here. Synchronous calls are simpler, and we get ot know if things worked straightaway. If we like the semantics of request/response but are dealing with longer-lived processes, we could just initiate asynchronous requests and wait for callbacks. On the other hand,  asynchronous event collaboration help us adopt a choreographed approach, which can yield significantly more decoupled services ---- something we want to strive for to ensure our services are independently releasable.

We are, of course, free to mix and match. Some technologies will fir more naturally into on style or another. We do, however, need to appreciate some of the different technical implementation details that will further help us make the right call.

## Remote Procedure Calls \(RPC\)

Remote procedure calls refers to the technique of making a local call and having it execute on a remote service somewhere. There are a number of different types of RPC technology out there. Some of this technology relies on having an inter face definition \(SOAP, Thrift, Protocol buffers\). The use of a separate interface definition can make it easier to generate client and server stubs for different technology stacks. Definition Language \(WSDL\) definition of the interface. Other technology, like Java RMI, calls for a tighter coupling between the client and server, requiring that both use the same underlying technology but avoid the need for a shared interface definition. All these technologies, however, have the same, core characteristic in that make a local call look like a remote call.

Many of these technologies are binary in nature, while SOAP uses XML for its message formats. Some implementations are tied to a specific networking protocol, whereas others might allow you to use different types of networking protocols, which themselves can provide additional features.



