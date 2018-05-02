---
description: 重新定义架构师
---

#                        Evolutionary Architect

Take a fairly opinionated view of what role of an architect is.

## Inaccurate Comparisons

Architects have an important job. They are in charge of making sure we have a joined-up technical vision, one that should help us deliver the system our customers need. We borrow the word "architects" from other professions, but we don't have a rigor and discipline which are important.

We borrow the word for recognition, but this can be doubly harmful. First, it implies we know what we are doing, when we plainly don't. Second, the analogies break down very quickly when given even a cursory glance. Software isn't constrained by the same physical rules that real architects or engineers have to deal with, and what we create is designed to flex and adapt and evolve with user requirements.

## An Evolutionary Vision for the Architect

We have to accept that once the software gets into the hands of our customers we will have to react and adapt, rather than it being a never-changing artifact. Thus, out architects need to shift their thinking away from creating the perfect end product, and instead focus on helping create a framework in which the right systems can emerge, and continue to grow as we learn more.

A town planner, just lick an architect, also needs to know when this play isn't being followed. As he is less prescriptive, the number of times he needs to get involved to correct direction should be minimal, but if someone decides to build a sewage plant in a residential area, he needs to be able to shut it down. So our architects as town planners need to set direction in broad strokes, and only get involved in being highly specific about implementation detail in limited cases. They need to ensure that the system is fit for purpose now, but also a platform for the future. And they need to ensure that it is a system that makes users and developers equally happy.

## Zoning

As architects, we need to worry much less about what happens inside the zone than what happens between the zones. That means we need to spend time thinking about how our services talk to each other, or ensuring that we can properly monitor the overall health of our system. 

## A Principled Approach

> Rules are for the obedience of fools and the guidance of wise men

Making decisions in system design is all about trade-offs, and micro-service architectures give us lots of trade-offs to make! 

### Strategic Goals

Strategic goals should speak to where your company is going, and how it sees itself as best making its customers happy. If you're the person defining the company's technical vision, this may mean you'll need to spend more time with the nontechnical parts of your organization.

### Principles

Principles are rules you have make in order to align what you are doing to some larger goal, and will sometimes change. A constraint is really something that is very hard to change, whereas principles are things we decide to choose.

### Practices

Our practices are how we ensure our principles are being carried out. They are a set of detailed, practical guidance of performing tasks. They will often be technology-specific and should be low level enough that any developer can understand them. Including coding guidelines, the fact that all log data needs tobe captured centrally, or that HTTP/REST is the standard integration style.

### Combining Principles and Practices

One person's principles are another's practices. For a small enough group, perhaps a single team, combining principles and practices might be OK. However, for larger organizations, where the technology and working practices may differ, you may want a different set of practices in different places, as long as they both map to a common set of principles.

## The Required Standard

Defining clear attributes that each service should have is one way of being clear as to where that balance sits.

### Monitoring

It is essential that we are able to draw up coherent, cross-service views of our system health. Knowing the health of individual service is useful, but often only when you're trying to diagnose a wider problem or understand a larger trend. To make this as easy as possible, I would suggest ensuring that all services emit health and general monitoring-related metrics in the same way.

You might choose to adopt a push mechanism, where each service needs to push this data into a central location. For your metrics this might be Graphite, and for your health it might be Nagios. Or you might decide to use polling systems that scrape data from the nodes themselves. But whatever you pick, try to keep it standardized. Make the technology inside the box opaque, and don't require that your monitoring systems change in order to support it.

### Interfaces

Picking a small number of defined interface technologies help integrate new consumers. Having one standard is a good number. Two isn't too bad, either. Having 20 different styles of integration is bad.

### Architectural Safety

We have to ensure that our services shield themselves accordingly from unhealthy, downstream calls. The more services we have that do not  properly handle the potential failure of down stream calls, the more fragile our systems will be. This means you will probably want to mandate as a minimum that each downstream service get its own connection pool, and you may even go as far as to say that each also uses a circuit breaker.

## Governance Through Code

Getting together and agreeing on how things can be done is a good idea, But spending time making sure people are following these guidelines is less fun, as is placing a burden on developers to implement all these standard things you expect each service to do.

### Exemplars

Written documentation is good, and useful. But developers also like code, and code they can run and explore. If you have a set of standards or best practices you would lick to encourage, then having exemplars that you can point people to is useful. By ensuring your exemplars are actually being used, you ensure that all the principles you have actually make sense.

### Tailored Service Template

By tailoring such a service template for your own set of development practices, you ensure that teams can get going faster, and also that developers have to go out of their way to make their services badly behaved. 

## Technical Debt

We are often put in situations where we cannot follow through to the letter on our technical version. Often, we need to make a choice to cut a few corners to get some urgent features out. This is just on more trade-off that we'll find ourselves having to make. Our technical vision exists for a reason. If we deviate from this reason, it might have a shot-term benefit but a long-term cost. A concept that helps us understand this trade-off is technical debt.

Sometimes technical debt isn't just something we cause by taking shortcuts. What happens if our vision for the system changes, but not all of our system matches. 

## Exception Handling

If enough exceptions are found, it may eventually make sense to change the principle or practice to reflect a new understanding of the world. But then we see compelling reasons to use Cassandra for highly scalable storage, at which point we change our practice to say, "Use MySQL for most storage requirements, unless you expect large growth in volumes, in which case use Cassandra".

## Governance and Leading from the Center

Part of what architects need to handle is governance.

> Governance ensures that enterprise objectives are achieved by evaluating stakeholder needs, conditions and options; setting direction through prioritisation and decision making; and monitoring performance, compliance and progress against agreed-on direction and objectives.

Architects are responsible for a lot of things. They need to ensure there is a set of principles that can guide development, and that these principles match the organization's strategy. They need to make sure as well that these principles don't require working practices that make developers miserable. They need to keep up to date with new technology, and know when to make the right trade-offs. This is an awful lot of responsibility. All that, and they also need to carry people with them to ensure that the colleagues they are working with understand the decisions being make and are brought in to carry them out. They need to spend some time with the team to understand the impact of their decisions, and perhaps even code too.

Normally, governance is a group activity. It could be an informal chat with a small enough team, or a more structured regular meeting with formal group membership for a larger scope. This is where I think the principles we covered earlier should be discussed and changed as required. This group needs to be led by a technologist, and to consist predominantly of people who are executing the work being governed. This group should also be responsible for tracking and managing technical risks.

A model I greatly favor is having the architect chair the group, but having the bulk of the group drawn from the technologists of each delivery team ---- the leads of each team at a minimum. The architect is responsible for making sure the group works, but the group as a whole is responsible for governance. This shares the load, and ensures that there is a higher level of buy-in. It also ensures that information flows freely from the teams into the group, and as a result, the decision making is much more sensible and informed.

Sometimes, the group may make decisions with which the architect disagrees. This is one of the most challenging situations to face. Often, I take the approach that I should go with the group decision. I take the view that I've done my best to convince people, but ultimately I was't convincing enough. The group is often much wiser than the individual, and I've been proven wrong more than once! And imagine how disempowering it can be for a group to have been given space to come up with a decision, and then ultimately be ignored.

Think about teaching children to ride a bike. You can't ride it for them. You watch them wobble, but if you stepped in every item it looked like they might fall off, then they'd never learn, and in any case they fall off far less than you think they will! But if you see them about to veer into traffic, or into a nearby duck pond, then you have to step in. Likewise, as an architect, you need to have a firm grasp of when, figuratively, your team is steering into a duck pond. You also need to be aware that even if you know you are right and overrule the team, this can undermine your position and also make the team feel that they don't have a say. Sometimes the right thing is to go along with a decision you don't agree with. Knowing when to do this and when not to is tough, but is sometimes vital.

## Building a Team

Much of the role of the technical leader is about helping grow them ---- to help them understand the vision themselves ---- and also ensuring that they can be active participants in shaping and implementing the vision too.

## Summary

Core responsibilities of the evolutionary architect:

Vision

Ensure there is a clearly communicated technical vision for the system that will help your system meet the  requirements of your customers and organization.

Empathy

Understand the impact of your decisions on your customers and colleagues.

Collaboration

Engage with as many of your peers and colleagues as possible to help define, refine, and execute the vision.

Adaptability

Make sure that the technical vision changes as your customers or organization requires it.

Autonomy

Find the right balance between standardizing and enabling autonomy for your teams

Governance

Ensure that the system being implemented fits the technical vision.

The evolutionary architect is one who understands that pulling off this feat is a constant balancing arg. Forces are always pushing you one way or another, and understanding where to push back or where to go with the flow is often something that comes only with experience. 



