---
title: Understanding micro-services
author: Julien Verlaguet
category: Microservices
layout: post
---

### Understanding Microservices: A Developer's Perspective

What exactly is a microservice architecture, and how does it compare
to traditional approaches like monolithic architectures? Let’s break
it down in simple terms.

#### The Traditional Monolithic Architecture

Before diving into microservices, it's essential to understand what a
typical monolithic architecture looks like. In a monolithic setup, you
have a client interacting with a single, large server. This server
handles everything—processing requests, interacting with the database,
and executing all the business logic. It’s a one-stop-shop for your
application’s backend.

![alt text](../img/growing_server.jpg)

While this approach works well initially, as your application grows,
so does the complexity. All your code is bundled together, making it
harder to manage and scale. Eventually, this can lead to slower
development cycles, as teams have to navigate through large,
intertwined codebases. This is usually when companies start thinking
about microservices.

#### Transitioning to Microservices

Microservices come into play when a monolithic system becomes too
complex to manage. Instead of one large application, microservices
break it down into smaller, independent services. Each service handles
a specific piece of functionality and can be developed, deployed, and
scaled independently.

For example, in a monolithic architecture, you might have one giant
application handling user authentication, product catalogs, and
payment processing. In a microservice architecture, these would be
separate services, each with its own database and API.

This separation not only makes it easier for teams to work
independently but also allows the system to scale more efficiently.

#### Horizontal vs. Vertical Scaling

When you split a monolith into microservices, there are two main ways
to approach it: horizontal scaling and vertical scaling.

##### Horizontal Scaling

This is the easier method, where you take different APIs or endpoints
from your monolith and turn them into separate services. For example,
you might break up a single large API into smaller, focused services,
each responsible for a specific part of your application.

![alt text](../img/monolith_split.jpg)

While horizontal scaling simplifies development, it can make
discoverability a challenge. As the number of services grows, it
becomes harder to find the right one. This requires good documentation
and organizational discipline to manage.

##### Vertical Scaling

This approach involves consolidating shared logic into a separate
service. For instance, if several services need to calculate a user’s
score for rating movies, you might create a dedicated service for this
task.

![alt text](../img/microservice_split.jpg)

Unfortunately, vertical scaling introduces latency, as services now
have to wait for responses from other services. Techniques like
asynchronous programming and caching can help mitigate some of this
latency, but they introduce their own complexities and trade-offs.

#### Dealing with Latency and Dependencies

Latency is one of the biggest challenges in a microservices
architecture. When services depend on each other, you end up with a
chain of dependencies where each service must wait for the previous
one to respond. This can slow down your application.

To combat this, developers often use caching, where data is
temporarily stored to avoid repeated calls to a service. However,
caching comes with its own trade-offs, such as stale data and memory
overhead.

Another significant issue is **cascading failures**. In a microservice
architecture, if one service fails, it can cause a ripple effect,
leading to multiple services timing out or crashing, potentially
bringing down the entire system.

#### Debugging Microservices

Debugging in a microservice architecture is notoriously
difficult. Unlike a monolithic system where everything runs on a
single machine, microservices are distributed across multiple
machines, making it hard to get a complete picture of what’s
happening. You can’t just take a snapshot of the whole system’s
state. Instead, you often have to rely on extensive logging and spend
hours piecing together logs from different services to understand what
went wrong.

#### The Complexity of Changing Microservices

One of the selling points of microservices is agility—the ability to
make changes quickly. However, this is only true if you don’t need to
change the system's overall structure. If you need to modify how
services interact, it can become a complex and time-consuming
task. Adding a new service or changing dependencies between services
often requires significant effort and can introduce new points of
failure and latency.

#### The Frontend Developer's Dilemma

Frontend developers often face the most challenges with
microservices. They need data from multiple services to build new
features or views, which can mean interacting with numerous teams and
APIs. This can be slow and frustrating, especially if those services
weren’t designed with the frontend’s needs in mind. This has led to
the concept of **backend-for-frontend (BFF)**, where a dedicated
backend service aggregates the necessary data for the frontend.

![alt text](../img/growing_server.jpg)

However, this BFF approach adds more complexity to the system,
creating yet another service that needs to be maintained and scaled.

#### Conclusion

Microservices can greatly improve scalability and flexibility in your
system, but they come with significant challenges, including increased
latency, debugging complexity, and potential for cascading
failures. They work best when the overall architecture remains stable
and services don’t need to change frequently. However, when changes to
the topology are required, or when frontend needs drive unexpected
data dependencies, microservices can become a source of pain rather
than a solution.

As developers, it’s crucial to weigh these trade-offs and carefully
consider whether microservices are the right fit for your
project. While they offer many benefits, they also introduce new
layers of complexity that require careful planning and management.
