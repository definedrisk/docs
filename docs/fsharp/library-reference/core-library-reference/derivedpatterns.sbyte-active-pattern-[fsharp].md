---
title: DerivedPatterns.SByte Active Pattern (F#)
description: DerivedPatterns.SByte Active Pattern (F#)
keywords: visual f#, f#, functional programming
author: dend
manager: danielfe
ms.date: 05/16/2016
ms.topic: language-reference
ms.prod: visual-studio-dev14
ms.technology: devlang-fsharp
ms.assetid: 8f0370f7-6b8d-4c1d-af3e-5926881ab705 
---

# DerivedPatterns.SByte Active Pattern (F#)

Recognizes constant signed byte expressions.

**Namespace/Module Path**: Microsoft.FSharp.Quotations.DerivedPatterns

**Assembly**: FSharp.Core (in FSharp.Core.dll)


## Syntax

```fsharp
// Signature:
( |SByte|_| ) : (input:Expr) -> sbyte option
```

#### Parameters
*input*
Type: [Expr](https://msdn.microsoft.com/library/ed6a2caf-69d4-45c2-ab97-e9b3be9bce65)


The input expression to match against.

## Return Value

The result of a successful match is the `sbyte` value.

## Remarks
This function is named `SBytePattern` in the .NET Framework assembly. If you are accessing the member from a .NET Framework language other than F#, or through reflection, use this name.


## Platforms
Windows 8, Windows 7, Windows Server 2012, Windows Server 2008 R2


## Version Information
**F# Core Library Versions**

Supported in: 2.0, 4.0, Portable

## See Also
[Quotations.DerivedPatterns Module](Quotations.DerivedPatterns-Module-%5BFSharp%5D.md)

[Microsoft.FSharp.Quotations Namespace](Microsoft.FSharp.Quotations-Namespace-%5BFSharp%5D.md)