---
description: >-
  My opponent's reasoning reminds me of the heathen, who, being asked on what
  the world stood, replied, "On a tortoise." But on what does the tortoise
  stand? "On another tortoise" -- Joseph Barker 1854
---

# How to Model Services

## What Makes a Good Service?

### Loose Coupling

When services are loosely coupled, a change to one service should not require a change to another. The whole point of a microservice is being able to make a change to one service and deploy it, without needing to change any other part of the system.

What sort of things cause tight coupling? A classic mistake is to pick an integration style that tightly binds one service to another, causing changes inside the service to require a change to consumers.

 A loosely coupled service knows as little as it needs to about the services with which it collaborates. This also means we probably want to limit the number of different types of calls from one service to another, because beyond the potential performance problem, chatty communication can lead to tight coupling.

### High Cohesion

We want related behavior to set together, and unrelated behavior to sit elsewhere. Making changes in lots of different places is slower, and deploying lots of services at once is risky ---- both of which we want to avoid.

So we want to find boundaries within our problem domain that help ensure that related behavior is in one place, and that communicate with other boundaries as loosely as possible.

## The Bounded Context

Bounded context, the idea is that any given domain consists of multiple bounded contexts, and residing within each are things that do not need to be communicated outside as well as things that are shared externally with other bounded contexts. Each bounded context has an explicit interface, where it decides what models to share with other context.

Another definition of bounded context I like is "a specific responsibility enforced by explicit boundaries." If you want information from a bounded context, or want to make requests of functionality within a bounded context, you communicate with its explicit boundary using models.

### Shared and Hidden Models

Now the finance department doesn't need to know about the detailed inner workings of the warehouse. It does need to know some things, though ---- for example it need to know about stock level to keep the accounts up to date.

The stock item then becomes a shared model between the two contexts. However, note that we don't need to blindly expose everything about the stock item from the warehouse context.

Sometimes we may encounter models with the same name that have very different meanings in different context too.

### Modules and Services

By thinking clearly about what models should be shared, and not sharing our internal representations, we avoid one of the potential pitfalls that can result in tight coupling. We have also identified a boundary within our domain. 

These modular boundaries then become excellent candidates for microservices. In general, microservies should cleanly align to bounded contexts. Once you become ver proficient, you may decide to skip the step of keeping the bounded context modeled as a module within a more monolithic system, and jump straight for a separate service. 

## Business Capabilities

When you start to think about the bounded contexts that exist in your organization, you should be thinking not in terms of data that is shared, but about the capabilities those contexts provide the rest of the domain. The warehouse may provide the capability to get a current stock list, or the finance context may well expose the end-of-month accounts or let you set up payroll for a new recruit. These capabilities may require the interchange of information -- shared models -- but I have seen too often that thinking about data lead to anemic, CRUD-based services. So ask first "What does this context do?", and then "So what data does it need to do that?"

When modeled as services, these capabilities become the key operations that will be exposed over the wire to other collaborators

## Turtles All the Way Down

In general, there isn't a hard-and-fast rule as to what approach makes the most sense. However, whether you choose the nested approach over the full separation approach should be based on your organizational structure. If order fulfillment, inventory management, and goods receiving are managed by different teams, they probably deserve their status as top-level microservices. If, on other hand, all of them are managed by on team, then the nested model makes more sense.

Another reason to prefer the nested approach could be to chunk uyp your architecture to simplify testing. 

## Communication in Terms of Business Concepts







