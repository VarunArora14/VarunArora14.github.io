---
title: "Load Balancer vs Reverse Proxy"
date: 2024-09-20T11:43:12+05:30
draft: false
tags: ["Networking", "Load balancer", "Reverse Proxy", "Networking"]
---

https://www.upguard.com/blog/reverse-proxy-vs-load-balancer

**Load Balancer** and **reverse proxy** are often same but not always.
The similarity between the 2 is in the fact that **they handle inbound requests across two or more web servers to spread the load.**
A reverse proxy, however, typically has any number of features:

- **load balancing**: as discussed above

- **caching**: it can cache content from the web server(s) behind it and thereby reduce the load on the web server(s) and return some static content back to the requester without having to get the data from the web server(s)

- **security**: it can protect the web server(s) by preventing direct access from the internet; it might do this through simple means by just obfuscating the web server(s) or it may have some more active components that actually review inbound requests looking for malicious code

- **SSL acceleration**: when SSL is used; it may serve as a termination point for those SSL sessions so that the workload of dealing with the encryption is offloaded from the web server(s)

**A load balancer can balance traffic from layer 3 upwards to layer 7, but a reverse proxy is HTTP specific**.

Also, a **reverse proxy is specific to web servers**.

Load balancers however can deal with a lot of other protocols. While the web (HTTP) is the big idea nowadays, things like DNS, mail (SMTP, IMAP), etc. can be load balanced as well. It's just nowadays when most people think "Internet" or "IP network" they think of the web. There's a bunch more stuff out there that may be more obscure, or more of a niche.

## Difference

Reverse proxies and load balancers both enhance the performance of application delivery networks, but the roles they play in this optimization aren’t quite the same.

**A reverse proxy is specifically a Level 7 load balancer**, dealing exclusively with **web requests**. A load balancer can operate on **Levels 3-7** of the OSI model, handling **numerous types of requests on top of web requests,** e.g., DNS, SSL, TCP.

A reverse proxy can perform additional roles to that of a load balancer. For example, a reverse proxy can also:

- **Operate as a WAF**
- Perform web acceleration, e.g. caching, TLS/SSL offloading, compression
- Provide cybersecurity mechanisms, e.g. threat protection, IP concealment, web filtering

---

## Layer 4 vs layer 7 load balancing

Layer 7 load balancing allows the load balancer to route a request based on information in the request itself, such as what **kind of content is being requested**. So now a request for an image or video can be routed to the servers that store it and are highly optimized to serve up multimedia content. Requests for transactional information such as a discounted price can be routed to the application server responsible for managing pricing. With Layer 7 load balancing, **network and application architects can create a highly tuned and optimized server infrastructure or application delivery network that is both reliable and efficiently scales to meet demand**.

Layer 7 load balancing enables the load balancer to make smarter load‑balancing decisions, and to apply optimizations and changes to the content (such as **compression and encryption**). It uses buffering to offload slow connections from the **upstream servers**, which improves performance.

**Upstream servers proxy request to other servers. Upstream defines a cluster of that you can proxy requests to. It's commonly used for defining either a web server cluster for load balancing, or an app server cluster for routing / load balancing.**

Nginx has upstream module for this - https://nginx.org/en/docs/http/ngx_http_upstream_module.html

Example

```
http {
  upstream myproject {
    server 127.0.0.1:8000 weight=3;
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
    server 127.0.0.1:8003;
  }

  server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }
}
```

**This means all requests for / go to the any of the servers listed under upstream XXX, with a preference for port 8000**.

---

Layer 4 Examples -

- TCP/IP - When you connect to website, it forms a TCP connection which is a stable connection
- UDP is another example for streaming video content, less data loss

Layer 7 Examples -

- User input into browser
- DNS resolution - browser uses DNS (Domain Name System) at the Application Layer to resolve the human-readable URL into an IP address.
- HTTP request and response occurs at layer 7
- Rendering - all rendering occurs at layer 6(presentation) and application layer

These occur at layer 7 and their connections are supported by layer 4

---

## Layer 4 Networking

- TCP/IP is suite of protocols for layer 4. UDP is another one. They define how devices communicate with each other
- Data is sent in form of packets(data + sender ip + recipient ip + control info)
- They are routed via router(which are at layer 3) to find the efficient path
- TCP connection is formed -
  - TCP handshake - 3 step(syn + ack + synack)
  - TCP ensures data is intact
- IP protocol(layer 3) handles the **addressing and routing of packets. There are 2 versions(IPv4 and IPv6)**

## Layer 7 networking

It consists of

- human interaction with software like browser, online game etc all are layer 7
- This layer 7 defines application communication over a network. Examples of protocols are - SMTP, FTP, DNS, HTTP/HTTPS
- It determines data formatting for data sent and recieves by applications

---

## TCP/IP with HTTPS working

Certainly! Let's simplify the process of sending a request to a website using TCP/IP and HTTPS encryption:

1. **You initiate a request:**

   - You type a website's address (URL) into your web browser and press Enter.

2. **TCP Connection Establishment (Layer 4):**

   - Your computer establishes a TCP connection with the web server using a process called the TCP handshake.
   - TCP ensures that the data you send and receive is reliable and in the correct order.

3. **HTTP/HTTPS Request (Layer 7):**

   - If the website uses HTTP, your request is sent in plain text.
   - If the website uses HTTPS, your request is encrypted for security. HTTPS uses SSL/TLS (Secure Sockets Layer/Transport Layer Security) protocols to encrypt data between your computer and the server.

4. **Server Processes the Request (Layer 7):**

   - The web server receives your request and processes it. If it's a simple HTTP request, the server processes it directly.
   - If it's an HTTPS request, the server uses its private key to decrypt the request. The server and your computer exchange public and private keys to establish a secure connection.

5. **Server Sends Response (Layer 7):**

   - The server processes your request and sends back a response.
   - If it's an HTTPS website, the response is encrypted using SSL/TLS and sent back to your computer.

6. **TCP Connection Termination (Layer 4):**
   - After the data transfer is complete, the TCP connection is closed, ensuring resources are freed up on both your computer and the server.

In simple terms, when you visit a website, your computer and the web server establish a secure and reliable connection using TCP. If the website uses HTTPS, your data is encrypted for security. The server processes your request and sends back the response, which is decrypted on your end if it's HTTPS. Finally, the connection is closed. This entire process ensures that your data is transmitted securely and accurately over the internet.

## SSH is layer 7

SSH (Secure Shell) operates at **Layer 7**, the **Application Layer**, of the OSI model. It is a cryptographic network protocol that provides a secure way to access and manage remote devices over an unsecured network. SSH allows encrypted communication between two computers, typically connecting an SSH client (like your computer) to an SSH server (remote device), enabling secure remote login, command execution, file transfer, and other network services.

While SSH uses encryption methods that ensure secure communication, it does so within the framework of the Application Layer. It provides application-level services for secure remote access and management.

---

## Layer 4 vs layer 7 data packet headers

Let's take an example to illustrate the differences between Layer 4 and Layer 7 with the context of a web browser making a request to a web server.

**Layer 4 (Transport Layer) Example:**

Suppose you're using a web browser to access a website over HTTPS, which uses the HTTP protocol over TCP. Here's a simplified breakdown of the communication at Layer 4:

1. **Data Packet (Segment):**
   - **Header Information:** Contains details like source and destination port numbers, sequence numbers, acknowledgment numbers, flags (such as SYN, ACK), and window size.
   - **Payload:** Includes a portion of the HTTP request or response data.

For instance, a TCP segment might look like this:

```
Source Port: 49152
Destination Port: 443
Sequence Number: 1001
Acknowledgment Number: 2002
Flags: SYN, ACK
Window Size: 8192
Payload: [HTTP Data]
```

This is a simplified representation, and the actual segment structure can be more complex.

**Layer 7 (Application Layer) Example:**

Now, let's dive into the details of the HTTP request at Layer 7:

1. **Data Packet (HTTP Request):**
   - **Header Information:** Contains details like the request method (GET, POST), URI (Uniform Resource Identifier), headers (such as Host, User-Agent), and cookies.
   - **Payload:** Includes the actual content of the HTTP request, like the parameters for a form submission.

For example:

```
GET /example/page.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
```

In this example, the HTTP request is made by the browser to retrieve a specific page from the server.

In summary, Layer 4 deals with the transport of segments between devices, including flow control and error checking, while Layer 7 is concerned with the content and semantics of the application data which are the details of the HTTP request at the application layer.
