---
title: Invoke methods from .NET Assemblies using Powershell and Reflection API
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

The malicious sample's code in dnSpy remains obfuscated and highly complex. Through the use of  PowerShell in conjunction with the .NET Reflection API is possible to invoke methods directly from imported assemblies. Rather than relying on static analysis and assumptions about the code’s behavior, this approach forces the execution of the specific method I am interested in.

## How to import (load) an assembly using PowerShell

When you import (load) an assembly, you are making its compiled classes and methods **available in memory** so you can interact with them.

A **.NET assembly** is just a compiled file that contains code:
- .dll (most common for libraries)
- .exe (can also be loaded as an assembly)


In PowerShell, this can be done with:
_Add-Type -Path "C:\Tools\MyLibrary.dll"_
or
_[Reflection.Assembly]::LoadFrom("C:\Tools\MyLibrary.dll")_

## .NET Reflection API

“You could use PowerShell in conjunction with the .NET Reflection API to invoke methods directly from imported assemblies.”

Reflection = code that can inspect other code at runtime.

In .NET, this lives mainly under:
System.Reflection

**PowerShell has native access to Reflection** because PowerShell itself is built on .NET.

Invoke a method = _Call a function that exists inside a compiled assembly._

## Method
By using PowerShell and Reflection:
1. **Load the Malicious Assembly:** Bring the malware into a controlled environment (like a sandbox).
2. **Inspect Its Methods:** Use Reflection to see what functions (methods) are inside the malware code.
3. **Invoke Methods Manually:** Instead of letting the malware run on its own, manually call certain methods to see what they do. 

The code ![smethod_0](/images/smethod_0.jpg)

  
From the code:
- Class name: Class238
- Namespace: **none** (global namespace)
- Method: smethod_0
- Signature: public static string smethod_0(int int_0)

**# Load the assembly (.exe or .dll)**
$AssemblyPath = "C:\Users\windows\Documents\analysis\purelogsstealer\sample\payload_Slayead-cleaned.exe"
$asm = [System.Reflection.Assembly]::LoadFrom($AssemblyPath)

**# Get the Class238 type (global namespace = just the name)**
$type = $asm.GetType("Class238")

**# Call the public static method smethod_0 with integer 0**
$returnString = $type::smethod_0(0)
Write-Output "Returned smethod_0 Method:"
Write-Output $returnString


## Results
![payload_after_slayer](/images/payload_after_slayer.jpg)


During the execution of step 1 **Load the assembly:

This means the assembly file was not recognized as a valid .NET assembly for execution, as expected.

![dnSpy](/images/dnSpy.jpg)

Next step will be to reapply the tools to avoid causing as much corruption to the assembly. 

#### Re-applying .NET Reactor Slayer

![slayer_gui](/images/slayer_gui.jpg)

I’ve attempted to select as few items as possible from the list. No matter which items I selected, or how many items I selected, I got the same result: the assembly appears to be corrupted enough not to load in PowerShell.

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

