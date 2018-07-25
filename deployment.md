# Deployment

Deploying a monolithic application is fairly straight forward process. Microservices, with their interdependence, are a different kettle of fish altogether. If you don't approach deployment right, it's one of those areas where the complexity can make your life a misery. In this chapter, we're going to look at some techniques and technology that can help us when deploying microservices into fine-grained architectures.

We're going to start off,  through, by taking a look at continuous integration and continuous delivery. These related but different concepts will help shape the other decisions we'll make when thinking about what to build, how to build it , and how to deploy it.

## A Brief Introduction to Continuous Integration

Continuous integration \(CI\) has been around for a number of years at this point. It's worth spending a bit of time going over the basics, however, as especially when we think about the mapping between microservices, builds, and version control repositories, there are some different options to consider.

With CI, the core goal is to keep everyone in sync with each other, which we achieve by making sure that newly checked-in code properly integrates with existing code. To do this, a CI server detects that the code has been committed, checks it out, and carries out some verification like making sure the code compiles and that tests pass.

As part of this process, we often create artifacts that are used for further validation, such as deploying a running service to run tests against it. Ideally, we want to build these artifacts once and once only, and use them for all deployments of that version of the code. This is in order to avoid doing the same thing over and over again, and so that we can confirm that the artifact e deployed is the one we tested. To enable these artifacts to be reused, we place them in a repository of we sort, either provideded by the CI tool itself or on a separate system.

CI has a number of benefits. We get some level of fast feedback as to the quality of our code. It allows us to automate the creation of out binary artifacts. All the code required to build the artifact is itself version controlled, so we can re-create the artifact if needed. We also get some level of traceability from a deployed artifact back to the code, and depending other capabilities of the CI tool itself, can see what tests were run on the code and artifact too.

### Are you Really Doing It?

I suspect you are probably using continuous integration in your own organization. If not, you should start. It is a key practice that allows us to make changes quickly and easily, and without which the journey into microservices will be painful. That said, I have worked with many teams who, despite saying that they do CI, aren't actually doing it at all. They confuse the use of a CI tool with adopting the practice of CI. The tool is just something that enables the approach.

> Do you check in to mainline once per day?

You need to make sure your code integrates. If you don't check your code together with everyone else's changes frequently, you end up making future integration harder. Even if you are using short-lived branches to manage changes, integrate as frequently as you can into a single mainline branch.

> Do you have a suite of tests to validate your changes?

Without tests, we just know that syntactically our integration has worked, but we don't know if we have broken the behavior of the system. CI without some verification that our code behaves as expected isn't CI.

> When the build is broken, is it the \#1 priority of the team to fix it?

A passing green build means our changes have safely been integrated. A red build means the last changes possibly did not integrate. You need to stop all further check-ins that aren't involved in fixing the builds to get it passing again. If you let more changes pile up, the time it takes to fix the build will increase drastically. 

## Mapping Continuous Integration to Microservices

When thinking about microservices and continuous integration, we need to think about how our CI builds map to individual microservices. As I have said many times, we want to ensure that we can make a change to single service and deploy it independently of the rest. With this in mind, how should we map individual microservices to CI builds and source code?

If we start with the simplest option, we could lump everything in together. We have a single, giant repository storing all our code, and have one single build. Any check-in to this source code repository will cause our build to trigger, where we will run all the verification steps associated with all out microservices, and produce multiple artifacts, all tied back to the same build.

This seems much simpler on the surface than other approaches: fewer repositories to worry about, and a conceptually simpler build. From a developer point of view, things are pretty straightforward too. I just check code in. If i have to work on multiple services at once, I just have to worry about one commit.

This model can work perfectly well if you buy into the idea of lock-step releases, where you don't mind deploying multiple services at once. In general, this is absolutely a pattern to avoid, but very early on in a project, especially if only one team is working on everything, this might make sense for short periods of time.

However, there are some signigicant downsides. If I make a one-line change to a single service, changing the behavior in the user service, all the other service get verified and built. This could take more time than needed. This impacts our cycle time, the speed at which we can move a single change from development to live. More troubling, though, is knowing what artifacts should or shouldn't be deployed. Do I new need to deploy all the build services to push my small change into production? It can be hard to tell; trying to guess which service really changed just by reading the commit messages is difficult.Organizations using this approach often fall back to just deploying everything together, which we really want to avoid.

Furthermore, if my one-line change to the user service breaks the build, no other changes can be made to the other services until that break is fixed. And think about a scenario where you have multiple teams all sharing this giant build. Who is in charge?

A variation of this approach is to have one single source tree with all of the code in it, with multiple CI builds mapping to parts of this source tree. With well-defined structure, you can easily map the builds to certain parts of the source tree. In general, I amp not a fan of this approach, as this model can be a mixed blessing. On the one hand, my check-in/check-out process can be simpler as I have only one repository to worry about. On the other hand, it becomes very easy to get into the habit of checking in source code for multiple services at once, which can make it equally easy to slip into making changes that couple services together. I would gereatly prefer this approach, however, over having a single build for multiple services.

So is there another alternative? The approach I prefer is to have a single CI build per microservice, to allow us to quickly make and validate a change prior to deployment into production. Here each microservice has its own source code repository, mapped to its own CI build. When making a change, I run only the build and tests I need to. I get a single artifact to deploy. Alignment to team ownership is more clear too. If you own the service, you own the repository and the build. Making changes across repositories can be more difficult in this world, but I'd maintain this is easier to resolve than the downside of the monolithic source control and build process.

## Build Pipelines and Continuous Delivery

Very early on in using continuous integration, we realized the value in sometimes having multiple stages inside a build. Tests are a very common case where this comes into play. I may have a lot of fast, small-scoped tests, and a small number of large-scoped, slow tests. If we run all the tests together, we may not be able to get fast feedback when your fast test fail if we're waiting for our long-scoped slow tests to finally finish. And if the fast tests fail, there probably isn't much sense in running the slower tests anyway! A solution to this problem is to have different stages in our build, creating what is known as a build pipeline. One stage for the faster tests, one for the slower tests.

This build pipiline concept gives us a nice way of tracking the progress of our software as it clears each stage, helping give us insight into the quality of our software. We build our artifact, and that artifact is used throughout the pipeline. As our artifact moves through these stages, we feel more and more confident that the software will work in production.

Continuous delivery \(CD\) builds on this concept, and then some, which is the approach whereby we get constant feedback on the production readiness of each and every check-in and furthermore treat each and every check-in as a release candidate.

To fully embrace this concept, we need to model all the processes involved in getting our software from check-in to production, and know where any given version of the software is in terms of being cleared for release. 

![](.gitbook/assets/screen-shot-2018-07-25-at-8.35.45-am.png)

Here we really want a tool that embraces CD as a first-class concept. I have seen many people try to hack and extend CI tools to make them do CD, often resulting in complex systems that are nowhere as easy to use as tools that build in CD from the beginning. Tools that fully support CD allow you to define and visualize these pipelines, modeling the entire path to production for your software. 

By modeling the entire path to production for our software, we greatly improve visibility of the quality of our soft ware, and can also greatly reduce the time taken between releases, as we have one place to observe our build and release process, and an obvious focal point fro introducing improvements.

In a microservices world, where we want to ensure we can release our service independently of each other, it follows that as with CI, we'll want one pieline per service. In our pipelines, it is an artifact that we want to create and move through our path to production. As always, it turns out our artifacts can come in lots of sizes and shapes. 

### And the Inevitable Exceptions

As with all good rules, there are exceptions we need to consider too. The "one microservice per build" approach is absolutely something you should aim for,  but are there times when something else makes sense? When a team is starting our with a new project especially a green field one where they are working with a blank sheet of paper, it is quite likely that there will be a large amount of churn in terms of working our where the service boundaries lie. This is a good reason, in fact, for keeping your initial services on the larger side until your understanding of the domain stabilizes.

During this time of churn, changes across service boundaries are more likely, and what is in or not in a given service is likely to change frequently. During this period, having all service in a single build to reduce the cost of cross-service changes may make sense.

It does follow, though, that in this case you need to buy into releasing all the services as a bundle. It also absolutely needs to be a transitionary step. As service APIs stabilize, start moving them out into their own builds. If after a few weeks \(or a very small number of months\) you are unable to get stability in service boundaries in order to properly sepqrte them, merge them back in to a more monolithic service and give yourself tiem to get to grips with the domain.

## Platform-Specific Artifacts

Most technology stacks have some sort of first-class artifact, along with tools to support creating and installing them. From the point of view of a microservice, though, depending on your technology stack, this artifact may not be enough by itself. So we may need some way of installing and configuring other software that we need in order to deploy and lunch our artifacts. 

Another downfall here is that these artifacts are specific to a certain technology stack, which may make deployment more difficult when we have a mix of technologies in play. Think of it from the point of view of someone trying to deploy multiple services together. They could be a developer or tester wanting to test some functionality, or it could be some one managing a production deployment. Now imagine that those services use three completely different deployment mechanisms. 

Automation can go a long way toward hiding the differences in the deployment 

## Operating System Artifacts

One way to avoid the problems associated with technology-specific artifacts is to create artifacts that are native  to the underlying operating system. The advantage of using OS-specific artifacts is that from a deployment point of view we don't care what the underlying technology is. We just use the tools native to the OS to install the package. The OS tools can also help us uninstall and get information about the package too, and may even provide package reposistories that our CI tools can push to . much of the work done by the OS package manager can also

