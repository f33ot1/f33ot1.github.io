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
An assembly is a **collection of types and resources** that are built to work together and form a **logical unit of functionality**.  
Assemblies are the **fundamental units of deployment, versioning, reuse, activation scoping, and security permissions** for .NET applications.

![Assembly](/images/learning_post1_assembly.png)

---

### 2. Module
A module is a **portable executable file**, such as a `.dll` or `.exe`, that contains **Intermediate Language (IL) code and metadata**.  
An assembly can consist of **one or more modules**.

![Module](/images/learning_post1_payload_module.png)

---

### 3. Namespace
A namespace is a **logical grouping of types**.  
Namespaces are used to **organize code** and to **avoid name collisions**.

![Namespace](/images/learning_post1_namespaces.png)

---

### 4. Class
A class is a **reference type** that can contain **data members, function members, constructors, nested types, and type operators**.

![Class](/images/learning_post1_class.png)
---

### 5. Method
A method is a **code block** that contains a series of statements.  
A method can be **invoked** to perform an action.

![Method](/images/learning_post1_method.png)

---

### 6. Method Metadata
Metadata (in general) describes the **types defined in an assembly**, including **their members**, the **parameters of methods**, and other information required at runtime.  
The Common Language Runtime uses metadata to **locate and load classes**, **lay out instances in memory**, and **invoke methods**.

![Metadata](/images/learning_post1_Method_VAI_Metadata.png)

![Metadetails](/images/learning_post1_Method_VAI_Detailed_Metadata2.png)
---

## References

https://learn.microsoft.com/en-us/dotnet/standard/assembly/
https://learn.microsoft.com/en-us/dotnet/standard/assembly/#assemblies-and-modules
https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/namespaces/
https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/classes
https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods
https://learn.microsoft.com/en-us/dotnet/standard/metadata-and-self-describing-components
https://learn.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/reflection
https://learn.microsoft.com/en-us/dotnet/standard/clr

