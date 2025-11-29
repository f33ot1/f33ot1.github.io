---
title: "PE Header Basics"
date: 2025-11-26
draft: false
summary: "A quick visual way to remember the main parts of a PE header."
tags: ["learning", "windows", "pe-file"]
---
## Core Idea

This note is about the basic structure of a Windows PE (Portable Executable) header.

## Explanation

A PE file starts with the **MZ header**, then later has the **PE header** at an offset stored in the MZ header.

Key pieces:
- `MZ` header: old DOS header.
- `e_lfanew`: offset that points to the PE header.
- `PE\0\0`: PE signature.
- Then: COFF header + Optional header + section headers.

## Visual / Mental Model

I imagine:
- A **book cover** (MZ header),
- With a **sticky note inside** (e_lfanew) that tells me where the main **table of contents** starts (PE header).

## Notes

- `e_lfanew` is like the bookmark telling me where to jump.
- The PE header starts at `e_lfanew`.

## References

- Microsoft PE/COFF spec (official docs).

