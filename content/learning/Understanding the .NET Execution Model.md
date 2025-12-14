---
title: Understanding the .NET Execution Model
tags:
  - dotnet
  - reflection
  - dnspy
  - malware-analysis
  - learning
---

## Overview
This note is a learning exercise to understand how .NET code is structured, loaded, and executed. I found it difficult to learn each element in isolation or in a small system, so I started with a high-level view of the .NET execution model, illustrated in the following diagram. To better understand each element, I located and examined it in dnSpy.

---

## High-Level Overview

![Diagram](/images/learning_post1_lilly_diagram.png)

---

## Core Concepts


### 1. Assembly
![Assembly](/images/learning_post1_assembly.png)

---

### 2. Modulo
![Module](/images/learning_post1_payload_module.png)

---

### 3. Namespace
![Namespace](/images/learning_post1_namespaces.png)

---

### 4. Class
![Class](/images/learning_post1_class.png)
---

### 5. Method
![Method](/images/learning_post1_method.png)

---

### 6. Method Metadata
![Metadata](/images/learning_post1_Method_VAI_Metadata.png)

![Metadetails](/images/learning_post1_Method_VAI_Detailed_Metadata2.png)
---

## References

[https://learn.microsoft.com/en-us/dotnet/core/introduction?WT.mc_id=dotnet-35129-website](https://learn.microsoft.com/en-us/dotnet/core/introduction?WT.mc_id=dotnet-35129-website)

[https://dotnet.microsoft.com/en-us/learn/dotnet/what-is-dotnet](https://dotnet.microsoft.com/en-us/learn/dotnet/what-is-dotnet)

[https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/reflection](https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/reflection)

[https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/viewing-type-information]
