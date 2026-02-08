# CST8917 – Assignment 1  
**Name:** Saizal Saini  
**Student Number:** 041168394  
**Course Code:** CST8917  
**Assignment Title:** Analysis of Serverless Computing and Azure Durable Functions  

---

## Part 1: Paper Summary

In *“Serverless Computing: One Step Forward, Two Steps Back”*, Hellerstein et al. (2019) critically examine first-generation serverless computing platforms and argue that while serverless represents a major advance in cloud usability, it also reintroduces many limitations that earlier distributed systems had already overcome. The authors describe serverless as “one step forward” because it simplifies deployment, scaling, and resource management for developers. However, it is also “two steps back” because it restricts system capabilities and performance in ways that hinder advanced data-intensive and distributed applications.

The central thesis of the paper is that current Function-as-a-Service (FaaS) platforms prioritize ease of use and operational simplicity at the expense of efficiency, flexibility, and system-level optimization. While developers benefit from automatic scaling and pay-per-use pricing, they lose control over execution environments, networking, and hardware resources. As a result, many workloads that previously ran efficiently on clusters or virtual machines become inefficient or impractical in serverless environments.

One major limitation identified is execution time constraints. Most serverless platforms impose strict time limits on function execution, typically ranging from a few seconds to several minutes. These limits make it difficult to run long-running analytics, machine learning, or streaming applications. Developers are forced to decompose tasks into smaller units, increasing coordination overhead.

The paper also highlights communication and network limitations. Serverless functions lack direct network addressability and cannot easily communicate with each other. Instead, they must rely on external storage services such as object stores or databases for data exchange. This creates bottleneck and increases latency. Related to this is the “data shipping” anti-pattern, where large datasets are repeatedly moved to stateless functions rather than executing computation close to where data is stored. This leads to high network costs and poor performance.

Another limitation is restricted hardware access. Serverless platforms generally provide only basic CPU and memory resources. Developers cannot access GPUs, FPGAs, high-speed networking, or specialized storage. This prevents the use of hardware acceleration and limits performance for scientific computing, artificial intelligence, and big data workloads.

The authors also discuss challenges for distributed and stateful computing. FaaS platforms treat functions as independent, stateless units, making it difficult to build workflows, coordinate parallel tasks, and maintain consistent state. Developers must manually implement orchestration and fault tolerance using external services, increasing complexity.

To address these issues, Hellerstein et al. propose several future directions for cloud programming. First, they advocate for fluid code and data placement, where systems automatically move computation to data rather than moving data to code. Second, they recommend richer communication models that support direct function interaction and low-latency messaging. Third, they propose providing access to specialized hardware and customizable execution environments. Finally, they suggest higher-level programming abstractions that integrate state, coordination, and fault tolerance into the platform itself.

---

## Part 2: Azure Durable Functions Deep Dive

### 1. Orchestration Model

Azure Durable Functions extends basic Azure Functions by introducing an orchestration model based on three function types: client, orchestrator, and activity functions. Client functions initiate orchestration instances. Orchestrator functions define workflow logic and coordinate execution, while activity functions perform individual tasks. Unlike basic FaaS, where functions operate independently, Durable Functions allow developers to define long-running, stateful workflows using code. The orchestrator schedules activities, handles retries, and manages dependencies. This model partially addresses the paper’s criticism that serverless lacks coordination mechanisms.

**Source:** https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview

---

### 2. State Management

Durable Functions manages state using event sourcing and checkpointing. Every action performed by an orchestrator is recorded in Azure Storage. When an orchestrator resumes, it replays its execution history to reconstruct state. This process is called deterministic replay. Developers do not manually manage databases for workflow state. This addresses the criticism that serverless functions are stateless, although performance depends on storage services.

**Source:** https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-orchestrations

---

### 3. Execution Timeouts

Durable Functions bypass standard function timeouts by persisting state and resuming execution. Orchestrations can run for days or months. However, individual activity functions are still subject to normal timeout limits. Large tasks must still be broken into smaller units, which partially resolves the paper’s concerns.

**Source:** https://learn.microsoft.com/en-us/azure/azure-functions/functions-scale

---

### 4. Communication Between Functions

Orchestrators and activity functions communicate through messages stored in Azure Storage. This simplifies coordination and fault tolerance. However, it still relies on storage intermediaries, which does not fully resolve latency and bandwidth issues.

**Source:** https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-task-hubs

---

### 5. Parallel Execution (Fan-out/Fan-in)

Durable Functions supports fan-out/fan-in patterns where multiple activity functions run in parallel and synchronize results. This enables distributed processing without manual coordination. Performance is still limited by storage and hardware constraints.

**Source:** https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-cloud-backup

---

## Part 3: Critical Evaluation

One unresolved limitation is restricted hardware access. Durable Functions runs on general-purpose infrastructure and does not support GPUs, FPGAs, or specialized networking. High-performance workloads still require traditional cloud resources. This reflects serverless platforms’ focus on uniformity and scalability over specialization.

Another partially addressed issue is storage-based communication. Durable Functions relies heavily on Azure Storage for coordination and state. This reinforces the data shipping pattern criticized in the paper and limits performance for data-intensive workloads.

Durable Functions aligns with the authors’ vision by integrating orchestration and state management into the platform. Developers can write complex workflows without building custom systems. Fan-out/fan-in patterns also support distributed computing.

However, Durable Functions mainly works around fundamental limitations rather than eliminating them. Statefulness is simulated through replay, communication remains indirect, and hardware access is restricted. These constraints stem from core serverless design principles.

Overall, Azure Durable Functions represents incremental progress rather than a full realization of the authors’ proposed future. It improves usability and reliability but does not fundamentally change data movement, networking, or execution models. It is best viewed as a practical compromise between simplicity and capability.

---

## References

Hellerstein, J. M., et al. (2019). *Serverless Computing: One Step Forward, Two Steps Back*.  
https://www.cidrdb.org/cidr2019/papers/p119-hellerstein-cidr19.pdf  

Microsoft. Azure Durable Functions Overview.  
https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview  

Microsoft. Durable Orchestrations.  
https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-orchestrations  

Microsoft. Azure Functions Scaling.  
https://learn.microsoft.com/en-us/azure/azure-functions/functions-scale  

Microsoft. Task Hubs in Durable Functions.  
https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-task-hubs  

Microsoft. Fan-out/Fan-in Pattern.  
https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-cloud-backup  

---

## AI Disclosure Statement

AI tools were used in the preparation of this assignment. ChatGPT was used to assist with organizing the analysis, and drafting initial versions of each section. The final content was reviewed and edited by me only.

---
---

## THANKS