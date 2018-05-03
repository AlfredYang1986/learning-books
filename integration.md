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



