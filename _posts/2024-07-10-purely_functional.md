---
title: Purely functional RP
author: Julien Verlaguet
category: Reactive programming
layout: post
---

### Should Reactive Programming Be Purely Functional?

A recurring question among developers is whether a purely functional
language is the most suitable choice for implementing a reactive
system. This article dives into the depths of this question. But
before we start, if you are a functional programmer, you should be
emotionally prepared: **I am going to say bad things about monads.**
Since most of my friends are functional programmers, I know how I am
going to be greeted the next time we meet... Oh dear :)

![alt text](../img/angry_mob.png)

### Understanding Reactive Systems

In essence, a reactive system can be seen as a graph of computation
where nodes represent values or states and arrows symbolize
functions. When a value updates, all dependent functions (or arrows)
are activated and recompute accordingly. In short, a reactive system
is nothing other than a graph of computation.

### Advantages of Purely Functional Languages for Reactive Systems

1. **Control Over Effects:** In a purely functional setting, functions
   must be devoid of side effects. This characteristic simplifies the
   semantics of reactive systems and makes it easier to predict system
   behavior.

2. **Immutable Values:** Purely functional languages offer
   immutability, which is invaluable when caching objects. If objects
   are immutable, there's no need to create additional copies when
   placing them in or extracting them from caches.

### Reactive Framework and User Logic

To effectively implement a reactive system, one can view it in two
main segments: the framework and the user logic.

- **The Framework:** Responsible for managing dependencies and
  recalculating values as they change. If using a purely functional
  language, this framework would need to be stateful, probably
  leveraging a monad like ST or State.

- **User Logic:** This defines the actions (functions) taken in
  response to value changes. If the framework is monadic, then the
  user must also write their logic within that same monad. This
  restriction could become complex, especially if users wish to
  introduce monads of their own.

### Monads

The framework's inherently stateful nature demands that it be
monadic. A reactive framework's primary task is to manage dependencies
and orchestrate recomputation as values change. This means it must
constantly track, maintain, and update an evolving state, remembering
past values to efficiently respond to new ones. Monads, particularly
in the context of functional programming, are the only way to manage
and encapsulate mutable state in an otherwise immutable
environment. By being monadic, the framework can represent stateful
computations in a pure, controlled, and predictable manner, ensuring
that the complexities and side effects associated with mutable state
are effectively managed and isolated.

![alt text](../img/monads.png)

### Monads... Again

Because a reactive system's framework is inherently monadic, it poses
challenges for developers writing the user logic, especially if the
user logic requires its own monadic context. Monads, by design,
encapsulate and manage side effects in functional programming. When
the user logic demands its own monad, developers have to navigate the
intricacies of composing and nesting these monadic contexts. This
composition can be complex and non-intuitive, leading to convoluted
code that's hard to understand.

You can read more about monadic composition
[here](https://web.cecs.pdx.edu/~mpj/pubs/RR-1004.pdf).

### Monads... Finally

Monads, while adept at handling side effects in functional
programming, introduce complexities when used in multi-threaded
environments. The primary challenge stems from the way monads
encapsulate state. The ST monad is inherently single-threaded in its
design. Using it in a multi-threaded context can demand intricate
workarounds, making the code more cumbersome and potentially
introducing performance bottlenecks or synchronization issues.

### Conclusion

While it is entirely feasible to build a reactive system with a purely
functional language, the complexity escalates when effects and mutable
values are introduced. Such systems demand a nuanced grasp of monads,
especially in multi-threaded scenarios. Can it be done? Certainly! Is
that a system I would want to use? Probably not...

![alt text](../img/until_next_time.png){:width="100%"}{:.centered}
