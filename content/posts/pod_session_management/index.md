---
title: "Pod Session Management with Redis"
date: 2024-09-13T01:43:12+05:30
draft: false
tags: ["Kubernetes", "Redis"]
---

Consider a huge monolith application which is running on a single EC2 and you want to swap it into the microservices architecture.

From the infra perspective, you have to setup cluster with a namespace for these microservices and associate pods as applications to run in.

Now as pods are running, it how do you manage user sessions?
Let's say if a pod gets deleted somehow, the user will be signed out of the application which you do not want to happen.

As **pods are stateless, requests can go to any pod** which you cannot determine and so you cannot manage state like monolith architecture.

To solve this problem, you have to **decouple state management from microservices running in pods**.

For this, you can use a **redis cluster** in kubernetes or **aws redis** which stores the user details for a session.
By this, each pod will have access to the same redis and will be able to handle requests of same user no matter which pod it get's hit.

Here **redis** acts as **external session storage**. **Databases** and **caching**(here redis to cache user data) is done externally. This ensures that session information persists even if a pod is terminated or rescheduled

---

More Concepts-

- **Sticky Session** - Use session affinity (or sticky sessions) at the load balancer level. This ensures that requests from a particular user are always directed to the same pod for the duration of their session. Kubernetes supports session affinity through the use of the **Service** resource and the **sessionAffinity** field.
- **Token based auth** - Implement token-based authentication mechanisms where user sessions are managed using tokens. The tokens can be validated on each request, allowing users to be directed to any pod, and the session information is stored externally. Example - **JWT**
- For managing state, **statefulsets** and centralized identity providers like **OAuth/OpenID** can also be used.

---

### Communication in micro-services

- Communication exists via notifications. Replace synchronous, tight coupling between components with asynchronous communication and message-based patterns.
- Use messaging queues or event-driven architectures to enable decoupled communication between microservices
