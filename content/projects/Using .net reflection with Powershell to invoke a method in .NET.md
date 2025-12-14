---
title: Using .net reflection with Powershell to invoke a method in .NET
date: 2025-11-27
draft: false
summary: Learn how to use Powershell and Reflection API to invoke methods from .NET Assemblies
tags:
  - reverse-engineering
  - malware
  - dotnet
  - windows
  - dnspy
  - powershell
tools:
  - dnSpy
  - powershell
---
## Overview

The malicious sample [hash] I am analyzing in dnSpy remains heavily obfuscated and highly complex. By using PowerShell in conjunction with the .NET Reflection API, I am able to invoke methods directly from imported assemblies. Rather than relying solely on static analysis and assumptions about the code’s behavior, this approach allows me to execute the specific methods I am interested in.

Before moving forward, I felt a need to refresh key concepts used in this technique. In Part 1, I briefly describe them.

### PART 1: THE CONCEPTS

Here's a high level diagram.

![diagram](/images/learning_post1_lilly_diagram.png)


### How to import (load) an assembly using PowerShell

When importing (or loading) an assembly, its compiled classes and methods are made **available in memory**, allowing interaction with them at runtime.

A **.NET assembly** is a compiled file that contains code, typically in one of the following formats:
- .dll (most common for libraries)
- .exe (can also be loaded as an assembly)

In PowerShell, this can be done with:

```Add-Type -Path "C:\Tools\MyLibrary.dll" ```

or

``` [Reflection.Assembly]::LoadFrom("C:\Tools\MyLibrary.dll")```

**PowerShell has native access to Reflection** because PowerShell itself is built on .NET.

### REFLECTION IN .NET
Reflection allows one part of a program to dynamically query and invoke another, in terms of assemblies, types, and members.
Assemblies contain modules, modules contain types, and types contain members. Reflection provides objects that encapsulate assemblies, modules, and types.

The classes in the `System.Reflection` namespace, together with `System.Type`, allow us to:

- Obtain information about loaded assemblies and the types defined within them, such as classes, interfaces, and value types (that is, structures and enumerations).
    
- Create type instances at runtime, and invoke or access their members.
    
- Create an instance of a type, bind the type to an existing object, or get the type from an existing object. Then, the user can invoke the type’s methods or access its fields and properties.

The ```System.Type``` class is central to reflection. The common language runtime creates the Type for a loaded type when reflection requests it. You can use a Type object's methods, fields, properties, and nested classes to find out everything about that type.

### PART 2: THE METHOD

1. **Load the Malicious Assembly:** Bring the malware into a controlled environment (like a sandbox).
2. **Inspect Its Methods:** Use Reflection to see what functions (methods) are inside the malware code.
3. **Invoke Methods Manually:** Instead of letting the malware run on its own, manually call certain methods to see what they do. 

```
#Load the assemby (.exe or .dll)  
$AssemblyName = "ReflectionAPI.exe"
[Reflection.Assembly]::LoadFile($AssemblyName) | Out-Null  
  
# Get the ReflectionUsage class Format: [namespace.class]  
$class = [ReflectionAPI.ReflectionUsage]  
  
# Call the static method 'staticEncryptText' with the input "W1C3Posts"  
$returnStaticEncryptText = $class::staticEncryptText("W1C3Posts")  
  
Write-Output "Returned staticEncryptText Method:"  
Write-Output $returnStaticEncryptText

```

## PART 3: CODE

The class to be analysed: ![smethod_0](/images/smethod_0.jpg)
From the code:
- Class name: Class238
- Namespace: **none** (global namespace)
- Method: smethod_0
- Signature: public static string smethod_0(int int_0)

```
**# Load the assembly (.exe or .dll)**
$AssemblyPath = "C:\Users\windows\Documents\analysis\purelogsstealer\sample\payload_Slayead-cleaned.exe"
$asm = [System.Reflection.Assembly]::LoadFrom($AssemblyPath)

**# Get the Class238 type (global namespace = just the name)**
$type = $asm.GetType("Class238")

**# Call the public static method smethod_0 with integer 0**
$returnString = $type::smethod_0(0)
Write-Output "Returned smethod_0 Method:"
Write-Output $returnString
```

## RESULTS

![payload_after_slayer](/images/payload_after_slayer.jpg)


I had previously applied .NET Reactor Slayer, and when I opened the file in dnSpy, I was able to see some level of corruption in the assembly, which led me to believe that the current method might fail.

During the execution of Step 1, **Load the assembly**, as expected, the assembly file was not recognized as a valid .NET assembly for execution.

As expected, **the assembly file was not recognized as a valid .NET assembly** for execution.

![dnSpy](/images/dnSpy.jpg)

#### Re-applying .NET Reactor Slayer

I will reapply .NET Reactor Slayer to the original obfuscated sample in an attempt to minimize corruption of the assembly and then try the method again.

![slayer_gui](/images/slayer_gui.jpg)

I attempted to select as few items as possible from the .NET Reactor Slayer GUI list. Regardless of which or how many items I selected, the result was the same: the assembly appears to be corrupted to the point that it cannot be loaded in PowerShell.

Below is a comparison between attempting to load the payload into memory **before** using .NET Reactor Slayer versus **after** its utilization.


#### Before
![payload_before_slayer.jpg](/images/payload_before_slayer.jpg)


#### After
![payload_after_slayer.jpg](/images/payload_after_slayer.jpg)


These results have been consistent across trials, which suggests that the use of .NET Reactor Slayer on the analyzed binary itself causes partial corruption of the assembly.

### Contribution 
This portfolio is maintained as part of my learning journey as a student in cybersecurity and malware reverse engineering. While I strive for accuracy and clarity, some content may contain errors or be incomplete.

If you notice any issues, have suggestions for improvement, or would like to contribute, please feel free to reach out to me at **f33ot1@gmail.com**. Feedback is always welcome and appreciated.

## References

- https://systemweakness.com/invoke-methods-from-net-assemblies-using-powershell-and-reflection-api-4d8e3e9e93b2
- [https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/reflection](https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/reflection)
- https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/viewing-type-information

