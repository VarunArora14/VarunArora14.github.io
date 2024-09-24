---
title: "Kubernetes Design Patterns"
date: 2024-09-13T01:02:12+05:30
draft: false
tags: ["Kubernetes", "MongoDB", "StatefulSet"]
---

## Multi-container design patterns

**Sidecar pattern**
An extra container in your pod to enhance or extend the functionality of the main container.

**Ambassador pattern**
A container that proxy the network connection to the main container.

**Adapter pattern**
A container that transform output of the main container.

**Reference -** https://kubernetes.io/blog/2015/06/the-distributed-system-toolkit-patterns/

---

The idea for a **sidecar** container is to **add some functionality not present in the main container**. Rather than bloating code, which may not be necessary in other deployments, adding a container to handle a function such as logging solves the issue, while remaining decoupled and scalable. **Prometheus monitoring and Fluentd logging leverage sidecar containers to collect data**.

The basic purpose of an **adapter** container is to **modify data, either on ingress or egress, to match some other need**. Perhaps, an existing enterprise-wide monitoring tools has particular data format needs. An adapter would be an efficient way to standardize the output of the main container to be ingested by the monitoring tool, without having to modify the monitor or the containerized application. An adapter container transforms multiple applications to singular view.

**Ambassador** containers allows for access to the outside world without having to implement a service or another entry in an ingress controller: **proxy local connection, reverse proxy, limits HTTP requests, re-route from the main container to the outside world**.

---

https://youtu.be/toLAU_QPF6o?si=dbpM5WhNb0yxk7YG&t=22073

**Ambassador pattern**

- Used for multi-container pod
- first container is called **main** container, other is **ambassador**
- used to proxy request run by main container to other server
- tightly coupled with the main container

**Sidecar**

- extremely useful and good to extend features of main containers
- acts as extension or helper for main container
- aims to bring more functionality without changing anything in it
- main container may be unaware of sidecar, unlike ambassador
- useful for monitoring and log forwarder patterns
  - Decide the main directory where logs are written
  - Create volume to make this directory accessible to log forwarder sidecar
  - launch sidecar with proper config, no impact will/should come on main container
