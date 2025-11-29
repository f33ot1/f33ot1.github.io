---
title: Invoke methods from .NET Assemblies using Powershell and Reflection API
date: 2025-11-27
draft: true
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

The malicious sample's code in dnSpy remains obfuscated and highly complex. Through the use of  PowerShell in conjunction with the .NET Reflection API is possible to invoke methods directly from imported assemblies. Rather than relying on static analysis and assumptions about the code’s behavior, this approach will allow me force the execution of the specific method I am interested in.

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

## Approach
By using PowerShell and Reflection:
1. **Load the Malicious Assembly:** Bring the malware into a controlled environment (like a sandbox).
2. **Inspect Its Methods:** Use Reflection to see what functions (methods) are inside the malware code.
3. **Invoke Methods Manually:** Instead of letting the malware run on its own, manually call certain methods to see what they do. 

The code ![[smethod_0.jpg]]
  
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

During the execution of step 1 **Load the assembly**:

![[payload_after_slayer.jpg]]

This means the assembly file was not recognized as a valid .NET assembly for execution, as expected.

![[dnSpy_decompilationrpoblems.jpg]]


Next step will be to reapply the tools to avoid causing as much corruption to the assembly. 

##Re-applying .NET Reactor Slayer

![[slayer_gui.jpg]]

I've attempted to select as minimal items as possible from the list. No matter which items I selected nor I am how items I selected, I got the same result: assembly appears to be corrupted enough to not load by powershell.

Below is a comparison between attempting to load the payload into memory before applying .NET Reactor Slayer versus after its application. 

Before
![[load_payload_before_Slayer.jpg]]

After
![[payload_after_slayer 1.jpg]]

These results have been consistent across trials which suggest that the .NET Reactor Slayer itself causes partial corruption of the assembly.

## References

- https://systemweakness.com/invoke-methods-from-net-assemblies-using-powershell-and-reflection-api-4d8e3e9e93b2

