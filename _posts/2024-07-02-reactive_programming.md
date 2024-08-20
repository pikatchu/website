---
title: Reactive Programming
author: Julien Verlaguet
category: Reactive programming
layout: post
---

![alt text](../img/magic_card.png){:.centered}

## What is Reactive Programming?

In simplest terms, reactive programming is a different way of
conceptualizing systems. Consider an updating system, like a user
interface (UI). Initially, your UI is in a specific state.  When a
user interacts, say by clicking or modifying something, the UI needs
to reflect these changes. It updates.

Traditionally, programmers would write logic manually to update the
state. But that's a recipe for a disaster. If there's a mistake in the
logic, it is extremely hard to debug, because the multitude of
potential states resulting from every user interaction is
overwhelming. Nobody has the brain to hold so much state in their
head.

## Enter Reactive Programming: freezing time

Rather than manually handling these updates, reactive programming
encourages you to write your logic as if time was frozen. From the
programmer's perspective, it’s like pretending the application is
always initializing. The real magic happens when a reactive
programming framework takes over: it manages updates for you! When
there's a change, the framework reruns the logic incrementally,
efficiently managing the updates without starting from scratch.

Benefits? No explosion of possible states. Your job is to worry about
what the logic should do at initialization time, while updates are
handled for you. Sounds too good to be true? Well ... there are a few
conditions for this to work.

## Immutability

One of the most important aspect of reactive programming is using
immutable objects. Immutable objects have two important properties:

1. They can be stored and loaded in a cache without a copy. As you
   can imagine, reactive programming will require some caching.  How
   else are we going to incrementally update if not by keeping around
   the objects that do not change? For caches to be efficient, you
   will have to store immutable objects in them. Why? Because we don't
   want to have to pay for a copy every time we retrieve an object out
   of a cache. That would be too inefficient.

2. Older versions of objects can be kept around cheaply, allowing to
   compare old versions with the new one. As stated before, a reactive
   framework needs to incrementally recompute the state of the
   application. For that to be efficient, the framework needs to
   determine which parts of the state have changed. Immutable objects
   are perfectly suited for that.

The best way to make sure the objects you need are immutable is
through a type-system. That's why, at SkipLabs, all of our reactive
programs are written in [SKIP](https://skiplang.com). SKIP is
programming language has been specifically created to provide all the
mutability guarantees required in a reactive system. In SKIP,
immutable is the default, mutable the exception. Moreover,
immutability is transitive: for an object to be immutable the
transitive closure of the pointers present in the object needs to be
immutable.

## Managing dependencies

For a reactive framework to partially recompute the state of a
program, the code must be structured accordingly. It means carefully
using loops or other constructions that create dependencies. That's
because you could end up recomputing a lot more than you intended to
when an update occurs. Instead, specific data structures should be
employed, at least when many objects are involved (small loops don't
matter).

### SKStore

At SkipLabs, we developed a Framework called `SKStore` to structure
reactive programs easily. `SKStore` is a reactive key/value store. It
let's you define relationships between directories, forming a graph of
computation. In the following snippet of code we associate a directory
called "added1" to an existing directory that was containing integers.

```javascript
  dirOfInts.map(
    SKStore.IID.keyType,    // The type of the key
    SKStore.IntFile.type,   // The type of the Files
    context,
    SKStore.DirName::create("/add1/"),
    (context, writer, key, values) ~> {
      for(value in values) {
        writer.write(key, value + 1)
      }
    }
  )
```

The directory `/add1/` is now bound to `dirOfInts`, every time one
changes, the other will follow. You can lookup values in any directory
you want, perform all the typical operations you normally would on a
key/value store, and everything will be tracked, in other words, the
system will know how to recompute things incrementally when a change
occured.

Wonder where the magic is? The magic is in the type of closure we
used. Normal closures use a different syntax `->`, but in this case we
used a pure closure `~>`. Pure closure guarantee that no effect can
escape the scope of the closure. Meaning, nothing that is captured by
the closure can mutate. However, it does not mean that the closure has
no effect, it could mutate parameters that have been declared as
mutable. In this case, the context is actually mutable, but that's ok
because the context is specifically designed to track operations
affecting the state of the store.

## Examples outside of Skip

There are many places where reactive programming has been successful.

- **Spreadsheets:** Believe it or not, spreadsheets are a classic
  example of reactive programming. The long-lasting objects, the cells
  in spreadsheets, update and react based on changes in values.

- **ReactJS:** A renowned JavaScript framework, React operates on the
  principles of reactive programming. Developers model the UI as if
  it's static, but when changes are required, immutable properties are
  used to determine the necessary recalculations.

## The Future: Expanding Horizons with Reactive Programming

At Skiplabs, the potential of reactive programming is being further
tapped into. Take 'SKDB,' a reactive SQL database. But the
vision goes beyond this. Imagine applying reactive programming to
distributed systems. These systems are essentially vast programs,
their states scattered across multiple machines. Reactive programming
could abstract the complexities of the network, from latency to
potential race conditions, offering a more streamlined and efficient
approach.

In conclusion, reactive programming offers a fresh perspective on
managing and updating systems. It's a paradigm shift that has profound
implications for the future of coding and system management.

![alt text](../img/until_next_time.png){:width="100%"}{:.centered}
