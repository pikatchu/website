---
title: Rust for RP
author: Julien Verlaguet
category: Reactive programming
layout: post
---

![alt text](../img/rusted_r.png)

## Should You Write Your Next Reactive System in Rust?

The allure of using Rust, with its strong type system and ownership
tracking, for reactive programming is evident. The question is: should
you dive deep into Rust for your next reactive project? The answer, as
with many questions in software development, is: it depends.

### Differentiating Reactive Systems

Before delving deep into this topic, let's establish a basic
understanding. Reactive systems can broadly be categorized into two:

1. **Streaming systems**: Here, updates are composed as streams using
   various programming constructs.

2. **Memoization systems**: These leverage previously computed data by
   saving it for later use.

### Memoization in Rust: The Challenge of Ownership

When discussing memoization-based systems, Rust's ownership tracking
system poses a unique set of challenges. With ownership tracking,
sharing values, especially in a cache, becomes a complex affair. The
cache's essence is to store and share data for optimal reuse. But the
very nature of Rust's ownership model is such that shared state is
avoided, making traditional caching difficult.

There are ways around this. One could borrow values, transfer
ownership, or make copies. However, each solution comes with its set
of challenges. For instance, transferring ownership in a
multi-threaded environment is not ideal. Making a copy every time we
need data from the cache is inefficient.

The seemingly viable option is to utilize reference-counted immutable
data structures. Yet, this approach has its pitfalls. It can
inadvertently lead to a style where most data structures are
reference-counted, leading to inefficiencies. Essentially, you end up
working in a manner resembling a purely functional language, but with
the overhead of atomic reference counting. This is not the most
efficient or painless way to code.

### Streaming Systems: Where Rust Shines

Streaming systems, on the other hand, have a brighter outlook when
implemented in Rust, provided the data being streamed is not overly
complex or symbolic. If you're dealing with simple data types (e.g.,
integers), Rust's ownership model doesn't pose a significant
challenge. The problem arises when the data being streamed is
intricate, containing numerous pointers. An example would be a
reactive compiler that has to deal with streams of file changes,
abstract syntax trees, and type checking. Such systems require shared
values, bringing us back to the challenges posed by Rust's ownership
model.

### Conclusion: To Rust or Not to Rust?

There's no denying the potential of Rust in building reactive systems,
especially with its robust type and ownership systems. However,
whether Rust is the right choice hinges on the nature of the system
you're building.

- **Go for Rust if:** You're building a streaming system with
  non-complex values. Such systems can harness Rust's strengths and
  can be very efficient, leveraging parallelized computation
  pipelines.

- **Think twice if:** Your system requires complex, pointer-rich data
  streams or heavily relies on memoization. The inherent challenges
  posed by Rust's ownership model in these scenarios might not be
  worth the trade-offs.

Lastly, while the allure of `unsafe` Rust is tempting, it essentially
puts you back in the realms of C, negating Rust's benefits.

So, should you write your next reactive system in Rust? Weigh the
trade-offs, understand the nature of your system, and make an informed
decision. Once you have done all that, you will get to the conclusion
that you should write your next reactive program in
[SKIP](http://skiplang.com) ;)

![alt text](../img/until_next_time.png){:width="100%"}{:.centered}
