---
layout: post
title: Resource Requests and Limits in Kubernetes
tags: [ notes, kubernetes, learning ]
---
* * *
Notes on k8s resource requests and limits

Reading up on resource requests and limits in k8s, found [this](https://medium.com/@betz.mark/understanding-resource-limits-in-kubernetes-cpu-time-9eff74d3161b) very interesting
two part blog.

What I learnt:

1. Memory

 * Requests:

   Handled only by the k8s scheduler, this is not passed in to the underlying
   cgroup, or even the docker runtime.

 * Limits:

   These are passed down to the runtime and the cgroup, and define the point at
   which the process gets OOM-killed.

2. CPU

 * Requests:

   Passed down to the runtime and to cgroups, defines how many parts of a 1000
   share cpu are allotted to a specific process. Also used by the k8s scheduler.

   Interesting note: k8s tracks total CPU as 1000 shares, but docker and cgroups
   track it as 1024 shares, so the docker/cgroups number for a corresponding k8s
   number will actually be slightly higher.

 * Limits:

   Passed down to the runtime and the cgroup, inspired by
   [[https://ai.google/research/pubs/pub36669][this paper]]. This is basically set by dividing the CPU shares into slices of
   time, and ensuring that a process does not take more than it's allotted share
   in a specific time period. This means that a process gets throttled when it
   hits the upper bound, instead of eviction, as in the memory case.
