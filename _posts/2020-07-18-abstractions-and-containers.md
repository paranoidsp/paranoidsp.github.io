---
layout: post
title: Abstractions and Containers
tags: [ abstractions, concepts, containers, rambling, notes, docker ]
---
* * * 
Containers are abstractions. (Yes, I know, it's in the name, but it's worth
exploring that concept further)

Most of language is just that, abstractions. They connect the map with the
territory, and provide us with a reasonable way of making decisions given that
the resolution of our maps do not allow us to comprehend all the details of the
territory.

In essence, abstractions simplify, and let us make assumptions that will help
decision making.

More thoughts on that in a separate post on abstractions later.

### The search for a cross-platform interface

Modern software development is based around using platform level abstractions to
perform actions and guarantee reliability and compatibility.

The availability of a variety of platforms, quite naturally, leads to a desire
for something to smooth over the edges, and gives birth to a new class of tools
that provide a common interface that abstracts platform irregularities, the
cross-platform toolchains.

There is a seductive appeal to these, the idea of compatibility and seamless
integration leads many to greatly value the ability of these tools to guarantee
a stable and reliable foundation. It also provides the promise of efficient
focus on optimizing a single workflow, without split focus problems that can
make progress difficult in both small and large teams.

This manifests itself in the repeated appearance of large cross-platform efforts
across different levels of software development, from the JVM, to QT, to
browsers and the modern JavaScript playgrounds, to virtualization and our
current topic, containers.

### Containers are abstractions

What you get out of a container is not particularly different from the
guarantees of a JVM, from this point of view. They're both abstractions that
promise stable, cross-platform support, and give you tools that ease your
development process.

In the end, what you need is output that the platform can handle, so this
necessitates a component that generates that output, and provides an API for you
to manipulate.

This can be done at either run-time, or with a build step.

The container pattern follows the expected model of a cross-platform interface,
with an API for stating your desire, and a middleman that turns that into
cross-platform objects via a build step that you can then run on underlying
platforms, in this case via a translation layer (the container runtime).

Containers are effectively large bundles of cross-platform code that you can
carry around and plop in wherever you want. You're now building and versioning
artifacts that are cross-platform, and building them via a minimal declarative
API that results in reproducible builds.

What makes them interesting is the meta aspect of their nature. They don't just
give you a cross platform interface, they also come with a simple way to
customize that interface to look like any specific platform that you like.
They're effectively an interface that can melt into the background, one that
does not restrict you or require you to do anything different from building on
your favourite platform.

This flexibility is what makes them extremely popular, and drives the immense
ecosystem that has sprung up around them, from runtimes to registries to
orchestration platforms. It's not an exaggeration to say that they've caused a
tectonic shift in how modern systems are architected. This adoption ensures that
containers are here to stay, and there will be years of effort spent on
optimizing container runtimes and building functionality around them.

### Leaky containers? Breaking metaphors? Too meta for recursion?

Of course, like all abstractions, this container is leaky too. (I honestly wrote
this section of the post just to laugh at this pun/heading)

Putting your application in a container doesn't entirely absolve you of the
responsibility to handle platform level details, and that can catch up with you
when you least expect it.

Networking, bugs in the runtime, stability issues with the platform etc. now
cause a new layer of problems that typically have lesser support and require
greater skill and knowledge to debug. The battle against incompatibile
interfaces now merely moves to a new frontier, the one between the
cross-platform application and the platform itself.

The assumption is that this will concentrate problem solving ability in one
place, and thereby provide focused effort in improving the abstraction. In
practice, this generally works, with great amounts of effort expended by the
larger players in ensuring platform compatibility, which the smaller players can
then take advantage of to create disruptions.

This system therefore is a great equalizer, easing developers access to
capabilities that would typically require a great deal of effort, which is the
hallmark of a good software platform. There has been legitimate criticism, but
containers have proven themselves to be a useful abstraction, and in the end,
that's all an abstraction has ever needed to be to stick around.


