---
title: Streaming vs Memoization
author: Julien Verlaguet
category: Reactive programming
layout: post
---

### Streaming vs Memoization: A Deep Dive

Reactive programming revolves around the capability to subscribe to
changes. Think of a reactive program as something that calculates
values, and you want to observe and know how these values change when
the inputs they use change. This whole concept differs slightly from
incremental computing, which focuses on minimal re-computation upon
any change. However, these two approaches often work in tandem. If
you're striving for efficient reactive programming, you'll likely need
to embrace some form of incremental computing. Why? Because the most
straightforward reactive model could re-evaluate everything upon a
change – and that's not particularly efficient. Thus, one cannot talk
about reactive programming without worrying about performance.

There are two prevalent methodologies or "schools of thought" in
implementing reactive programming:

### Streaming

![alt text](../img/pipes.png){:width="100%"}{:.centered}

Think of this as a network where updates or changes (visualize them as
streams of water in interconnected pipes) drive
computations. Essentially, when an input receives a change, it emits
an update that travels through these "pipes", causing other dependent
computations to adjust accordingly. Streaming is eager; it immediately
updates.

### Memoization

With memoization, computations are stored or "cached". When the
program requires a value, it first checks if it's been previously
computed and cached. If not, it calculates it. Memoization drives
computation based on the need for specific values. It's more of a lazy
approach, using stored values when required and updating them based on
changes.

### Which one should you choose?

![Why not both](../img/why-not-both-why-not.gif)

When do you employ one over the other? In reactive programs that are
complex enough, you'll likely need a mix of both. Streaming, being
eager, is quintessential for tasks where immediate updates are needed,
like constructing an index (what's the point in building an index
lazily?). Conversely, for processes like determining a transitive
closure (i.e., a set of all reachable nodes), the lazy, cached (or
memoized) approach is more efficient.

At SkipLabs, all of our reactive programs are written in
[SKIP](http://skiplang.com). And SKIP defines a Reactive Store called
`SKStore` to structure the graph of computation. `SKStore` has two
types of directories: 'eager' and 'lazy'. Eager directories are the
moral equivalent of streaming; they update quickly but take up more
memory, like fast-acting storage spaces. Lazy directories, on the
other hand, work like memoization; they update slower, waiting until
you ask for something, and use less memory because they can "forget"
values that aren't needed.

Let's take an example. First, we will need an input directory. All
input directories are eager (by definition).

```javascript
  // Keys are integers and values also
  myInput = SKStore.mkdir(
    context,
    SKStore.DirName::create("/input/")
  );
```

Now, we can create an index out of this input directory, using the
"map" method. The result is an eager directory called `/index/`.

```javascript
  // To build an index we need an eager (streaming) approach
  index = myInput.map(
    SKStore.IID::keyType,     // The type of the keys
    SKStore.IntFile::type,    // The type of the values
    context,
    SKStore.DirName::create("/index/"),
    (context, writer, key, values) ~> {
      for(value in values) {
        writer.write(
  	SKStore.IID(value.value),
  	SKStore.IntFile(key.value));
      }
    }
  );
```

That went well, because we want the data in `myInput` to "drive" the
computation. When `myInput` changes, we want the index to update
accordingly. But that is not always going to be the case. Some times,
it is much more convenient to let the values drive the computation.
For example, to compute the transitive closure of the key/value pairs
involved. What would the code look like with an eager directory? It
would be rather unnatural. You would have to keep on iterating to find
a fixpoint.

The code is a lot more natural using a lazy directory. Because they
let us look up the objects we need directly.

```javascript
  // To build the transitive closure, lazy is better
  transitiveClosure = LHandle::create(
    SKStore.IID::keyType,     // The type of the keys
    SKStore.IntFile::type,    // The type of the values
	context,
	SKStore.DirName::create("/transClosure/"),
	(context, self, key) ~> {
	  assocs = myInput.getArray(key);
	  result = mutable Vector[];
	  for(file in assocs) {
	    result.extend(self.getArray(file.value));
	  };
	  result.toArray();
	}
  );
```

### Conclusion

In the world of reactive programming, there's a distinct difference
between 'eager' (streaming) and 'lazy' (memoization). Eager
directories are primarily driven by updates, meaning they actively
respond and adjust to changes in their input directories. Think of
them as always on their toes, ready to adapt the moment something
changes. On the other hand, lazy directories, instead of being
proactive, react only when a user specifically requests a value. This
distinction helps in optimizing memory usage and responsiveness based
on the specific needs of the system.

In simple words, a reactive program is a program that changes when its
inputs do. With `SKStore`, programmers can mix both streaming and
memoization methods, using the same abstraction, to build fast and
effective systems.

![alt text](../img/until_next_time.png){:width="100%"}{:.centered}
