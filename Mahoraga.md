# Project Specification: Load Balancer in Go
`This Spec is HIGHLY Prone to Change`
## Table of Contents 
1. [Introduction](#introduction)
2. [Name Origin](#name-origin)
3. [Scaling](#scaling)
4. [Most Used Strategies (Algorithms) in Load Balancing](#most-used-strategies-algorithms-in-load-balancing)
5. [Motivation](#motivation)
6. [Requirements](#requirements)
7. [Architecture](#architecture)
8. [Key Components](#key-components)
9. [Technologies and Tools](#technologies-and-tools)
10. [Components](#components)
11. [Language Choice](#language-choice)
12. [Hybrid Scalability](#hybrid-scalability)
13. [limits of Virtual_hybrid Scaling](#limits-of-virtual_hybrid-scaling-to-be-added-later)
## Introduction

### Basic overview of the Custom Load Balancer
The purpose of this project is to design and implement a custom **Load Balancer** using the Go programming language. The load balancer will efficiently distribute incoming traffic across a pool of backend servers to ensure optimal resource utilization, scalability, and fault tolerance.

### Importance of load balancing in modern applications
   - **Improved Scalability**
   - **Enhanced Availability and Fault Tolerance**
   - **Optimal Resource Utilization**
   - **Reduced Latency and Faster Response Times**
   - **Flexibility in Deployment and Maintenance**
   - **Disaster Recovery**
   - **Better User Experience**

---

### Name Origin :

`Mahoraga` : represents a mythical creature that can adapt to any and all phenomena.

in this project `Mahoraga` stands for:
- M: Maximized — Reflecting the load balancer's ability to handle the maximum load efficiently.
- A: Adaptive — Emphasizing its flexibility in adapting to varying loads and traffic demands.
- H: High-performance — Ensuring the system can operate with optimal efficiency, no matter the load size.
- O: Optimized — The balancer is designed to optimize resource usage, avoiding bottlenecks while balancing loads.
- R: Resilient — Capable of maintaining performance even under extreme or fluctuating conditions.
- A: Agile — Quickly adjusting to changing network conditions and client requests.
- G: Granular — Able to scale with fine control, distributing loads with precision based on real-time metrics.
- A: Autonomous — Self-managing, capable of making real-time decisions about load distribution and server health.
---

### Scaling
Scaling refers to the process of increasing the capacity of a system to handle growing demand. There are two main types of scaling: vertical and horizontal.

1. Vertical Scaling
Vertical scaling involves adding more resources (such as CPU, memory, or storage) to a single server to improve its performance.
Example: Increasing from 8GB RAM to 16GB RAM.

- Pros
    - Easy to maintain: Managing one server is simpler.
    - Easy to deploy: Scaling up requires fewer steps.
    - Easy to manage: Monitoring one server is less complex.
    - Less complex: No need to handle multiple systems.
    - Lower availability: If the system fails, there's no backup.

- Cons
    - Single point of failure: If the only server (singleton instance) goes down, the entire system is unavailable.

2. Horizontal Scaling
Horizontal scaling involves adding more servers or instances to distribute the workload.
Example: Increasing from 2 servers to 5 servers.

- Pros
    - High availability: With multiple servers, the system can continue running even if one goes down by providing a hot swaping mechanism .
    - Predictable growth headroom: Adding more servers allows for future expansion.
    - Improved performance: Distributing the load across multiple servers can enhance overall performance by draining every bit of power from the servers.

- Cons
    - Hard to deploy: Managing multiple servers can be more complicated.
    - Hard to manage: More servers mean more complexity in maintenance.
    - Highly complex: Orchestrating multiple servers requires more sophisticated tools and strategies.
    - Single point of failure: If a critical singleton instance (like a database) goes down, the system can still be disrupted.

---

### Most Used Strategies (Algorithms) in Load Balancing

In the context of load balancing, different strategies or algorithms are employed to distribute incoming network traffic across servers to optimize resource usage and avoid server overloads. These algorithms can generally be categorized into three types: static, dynamic, and hybrid.

### 1. Static Strategies
Static algorithms use predefined rules to distribute traffic, without considering real-time server conditions. These are simple but may not perform optimally when server loads vary.

- **Round Robin**: Distributes requests sequentially to each server in a loop, ensuring each server gets an equal number of requests.
- **Sticky Round Robin**: Similar to round robin, but it maintains client sessions on the same server for consistent performance during a session.
- **IP/URL Hashing**: Uses a hash of the client’s IP address or URL to assign traffic to a specific server, providing consistent routing for repeat requests from the same source.

### 2. Dynamic Strategies
Dynamic algorithms take real-time server load and performance metrics into account when distributing traffic. This approach adapts to changes in server conditions.

- **Least Connections**: Directs traffic to the server with the fewest active connections, balancing the load based on current usage.
- **Least Time**: Sends traffic to the server that is expected to handle the request the fastest, based on response time and active connections.

### 3. Hybrid Strategies
Hybrid strategies combine static and dynamic methods, using multiple algorithms to achieve better performance and adaptability in complex environments.

- **Two-Tier**: Utilizes a combination of static and dynamic algorithms, where a first-tier strategy routes traffic initially, followed by a second-tier strategy to fine-tune the distribution.
- **Predictive Load**: Uses historical data and predictive analytics to distribute traffic in anticipation of future load, optimizing server use based on expected conditions.

---
### Motivation

As modern applications continue to scale, the need for reliable and efficient distribution of network traffic has become essential. The primary motivation for developing this load balancer is to:

1. **Improve Resource Utilization**
2. **Enhance Scalability**
3. **Increase Fault Tolerance**
4. **Support Sticky Sessions**
5. **Optimize Performance**

---
### Requirements :
### Functional Requirements

1. **Traffic Distribution**
   - The load balancer must route incoming requests to one of the available backend servers using one of the already mentioned algorithms

2. **Health Checks**
   - The system must periodically check the health of all backend servers.
   - Unhealthy servers should be temporarily removed from the pool.
   - Once healthy, servers should be reintroduced into the pool to handle requests.

3. **Session Persistence (Sticky Sessions)**
   - Ensure that requests from the same client are consistently routed to the same backend server using either IP-based hashing or a session token.

4. **Logging and Monitoring**
   - The system should log all incoming requests, including details like the source IP, selected backend server, request duration, and server response status.
   - A dashboard or monitoring interface should be available to track server status, traffic distribution, and health checks.

5. **Dynamic Scaling**
   - Support dynamic scaling of backend servers, allowing for the addition or removal of servers based on traffic load or server health status.

6. **Security Features**
   - Implement basic SSL/TLS termination for secure connections (to be decided later if this will be included).
   - Provide rate-limiting mechanisms to prevent Denial of Service (DoS) attacks.

### Non-Functional Requirements

1. **Performance**
   - The load balancer should be capable of handling at least 10,000 concurrent connections as a baseline, with minimal latency.
   - Response times should remain below 100ms under normal load conditions to ensure a smooth user experience.

2. **Scalability**
   - The system must support horizontal scaling, allowing for the addition of multiple load balancers and backend servers as traffic demands increase.

3. **Reliability**
   - The system should guarantee high availability with failover capabilities to maintain service continuity when backend servers or the load balancer itself experience failures.

4. **Usability**
   - The configuration and setup process for backend servers should be straightforward and user-friendly.
   - Provide both web-based and command-line interfaces for monitoring system performance and health.

---

### Architecture
- it is estimated to use the `modular monolith architecture`

---

### Key Components:
1. **Frontend**: Dashboard
2. **Load Balancer**: The central component that distributes traffic based on the defined algorithms.
3. **Backend Servers**: A pool of servers that handle client requests.

---

### Technologies and Tools

1. **Programming Language**: Go (Golang)
2. **Web Framework**: Go’s `net/http` package
3. **Health Checking**: Go’s `net` package for TCP/HTTP checks
4. **Security**: SSL/TLS via `crypto/tls` package + Rate limiter
5. **Logging**:
   - Log all incoming requests, including source IP, selected backend server, request duration, and server response status.
   - Provide a **Flutter-based dashboard** or interface to monitor server status, traffic distribution, and health checks.
   - Provide a **Flutter-based dashboard** that shows statistiques and ban table .
   - store the monitoring data inside a DB `To be decided later`
6. **Testing**: Go’s built-in testing framework (`testing` package)

---

### Components
The load balancer is divided into three main components:

1. **Rate Limiter**: Controls the rate of incoming requests to prevent overloading the system and mitigate potential Denial of Service (DoS) attacks.
2. **Core Balancer**: The central component responsible for distributing requests to the available backend servers based on the selected load balancing algorithm.
3. **Pool of Servers**: A dynamic pool of backend servers that handle the incoming client requests. The pool can be updated in real-time by adding, removing, or disabling servers based on server health and system load.

---

### Language Choice
For this project, performance is key, and we need a compiled language that efficiently manages memory—especially when dealing with pointers. After reviewing our options, we identified three main contenders: Zig, Rust, and Go. Let’s break down why Go emerges as the most suitable choice.

## Ziglang
    - Pros
        - **Performance**: Currently the fastest language in very specific use cases.
        - **Reliability**: Offers good reliability.
        - **Efficiency**: Efficient in handling low-level memory management.
        - [x] Supports pointers
        - [x] Compiled language
    - Cons
        - **Immaturity**: Zig is still young, and lacks a mature ecosystem.
        - **Instability**: Since it is evolving, its stability cannot be guaranteed for large-scale projects.

## Rustlang
    - Pros
        - **Performance**: Rust provides excellent performance, close to Zig, and is highly robust.
        - **Reliability**: Rust’s strict memory safety guarantees ensure very reliable systems.
        - **Efficiency**: Rust excels at managing resources efficiently, especially with its borrow checker.
        - [x] Supports pointers
        - [x] Compiled language
    - Cons
        - **Steep learning curve**: Rust is notoriously difficult to learn and comes with a lot of complexity.
        - **Development overhead**: The large language and tooling require developers to spend extra time implementing features manually.
        - **Complexity**: While powerful, Rust's complexity can slow down development and make it harder to maintain in the long run.

## Golang
    - Pros
    - **Balanced Performance**: While not as fast as Zig in some niche cases, Go offers excellent, reliable performance that is more than sufficient for most use cases, especially when combined with its native concurrency support.
    - **Simplicity & Developer Productivity**: Go is designed for simplicity. It has a small and clean syntax, which leads to faster development cycles and easier maintenance. You spend less time wrestling with the language and more time building features.
    - **Built-in Concurrency**: Unlike Zig and Rust, Go’s built-in support for goroutines makes it incredibly efficient for handling concurrent tasks, making it ideal for scalable, distributed systems—like the one this project requires.
    - **Mature Ecosystem**: Go has a stable, well-supported ecosystem, with a robust standard library, minimizing the need for third-party packages and allowing you to focus on the core logic of your project.
    - **Ease of Maintenance**: Go’s simplicity ensures that codebases remain clean and maintainable, even as projects grow in size.
    - [x] Supports pointers
    - [x] Compiled language
    - Cons
        - **Less Low-Level Control**: Go doesn’t offer the same level of fine-grained memory control as Rust or Zig, but this is often a trade-off for the ease of use and speed of development it provides. For most high-level use cases, Go’s garbage collector is highly optimized and works efficiently.


### Conclusion (by using the process of elimination)

While Zig offers the best raw performance and Rust excels in safety and memory management, Go strikes the best balance between performance, simplicity, and scalability. Its ease of use, mature ecosystem, and powerful concurrency model make it the ideal choice for building robust, scalable systems quickly and efficiently. When you factor in development speed, maintainability, and scalability, Go stands out as the clear winner for this project.

---

### `Hybrid Scalability`

Hybrid scalability refers to a strategy that leverages both **horizontal scaling** and **vertical scaling** to optimize system performance and resource efficiency. In the context of load balancing, this approach involves not only increasing the number of servers to handle higher traffic (horizontal scaling) but also ensuring that each server operates at peak efficiency (vertical scaling).This combination ensures a more adaptable, robust, and cost-effective scaling strategy.


### Advantages of Hybrid Scalability

- **Cost Efficiency**: Rather than solely relying on adding more servers to manage increased traffic, hybrid scalability ensures that existing resources are fully utilized. This approach can significantly reduce operational costs by optimizing server usage before scaling out horizontally.
  
- **Adaptability**: By combining both scaling methods, the system can adapt to varying traffic patterns, whether dealing with a large, sustained increase in load or sudden spikes. The flexibility provided by hybrid scalability ensures that the system remains both responsive and efficient.
  
- **Increased Resilience**: With the ability to scale both horizontally and vertically, the system is more resilient to failures. If vertical scaling reaches its capacity, horizontal scaling acts as a backup, maintaining system stability and performance even under extreme conditions.
- **minimize costs** 

### Why Hybrid Scalability Is `almost-Optimal`

In environments where performance, reliability, and cost-effectiveness are critical, hybrid scalability offers a comprehensive solution. By efficiently balancing both server capacity and performance, this approach ensures that resources are allocated in the most effective way possible, making it well-suited for highly dynamic and scalable systems.

---

### limits of Virtual_hybrid Scaling 