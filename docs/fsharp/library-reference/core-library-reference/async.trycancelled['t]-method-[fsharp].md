---
title: Async.TryCancelled<'T> Method (F#)
description: Async.TryCancelled<'T> Method (F#)
keywords: visual f#, f#, functional programming
author: dend
manager: danielfe
ms.date: 05/16/2016
ms.topic: language-reference
ms.prod: visual-studio-dev14
ms.technology: devlang-fsharp
ms.assetid: 2396431d-2c04-4b49-9817-38050cdfd84a 
---

# Async.TryCancelled<'T> Method (F#)

Creates an asynchronous computation that executes the specified computation function. If this computation is cancelled before it completes then the computation generated by running the compensation function is executed.

**Namespace/Module Path**: Microsoft.FSharp.Control

**Assembly**: FSharp.Core (in FSharp.Core.dll)

## Syntax

```fsharp
// Signature:
static member TryCancelled : Async<'T> * (OperationCanceledException -> unit) -> Async<'T>

// Usage:
Async.TryCancelled (computation, compensation)
```

#### Parameters

*computation*
Type: [Async](https://msdn.microsoft.com/library/e0b28ea2-dea5-4021-b2b9-d7d4761babde)**&lt;'T&gt;**

The input asynchronous computation.

*compensation*
Type: **System.OperationCanceledException -&gt; [unit](https://msdn.microsoft.com/library/00b837c2-6c8a-483a-87d3-0479c64037a7)**

The function to be run if the computation is cancelled.

## Return Value

Returns an asynchronous computation that runs the compensation function if the input computation is cancelled.

## Remarks

The following code example illustrates how to use `Async.TryCancelled` to run a cancellable computation.

```fsharp
open System
open System.Windows.Forms

let form = new Form(Text = "Test Form", Width = 400, Height = 400)
let panel1 = new Panel(Dock = DockStyle.Fill)
panel1.DockPadding.All <- 10
let spacing = 5
let startAsyncButton = new Button(Text = "Start", Enabled = true)
let controlHeight = startAsyncButton.Height
let button2 = new Button(Text = "Start Invalid", Top = controlHeight + spacing)
let cancelAsyncButton = new Button(Text = "Cancel",
                                   Top = 2 * (controlHeight + spacing),
                                   Enabled = false)
let updown1 = new System.Windows.Forms.NumericUpDown(Top = 3 * (controlHeight + spacing), 
                                                     Value = 20m, Minimum = 0m,
                                                     Maximum = 1000000m)
let label1 = new Label (Text = "", Top = 4 * (controlHeight + spacing),
                        Width = 300, Height = 2 * controlHeight)
let progressBar = new ProgressBar(Top = 6 * (controlHeight + spacing),
                                  Width = 300)
panel1.Controls.AddRange [| startAsyncButton; button2; cancelAsyncButton;
                            updown1; label1; progressBar; |]
form.Controls.Add(panel1)

// Recursive isprime function. 
let isprime number =
    let rec check count =
        count > number/2 || (number % count <> 0 && check (count + 1))
    check 2

let isprimeBigInt number =
    let rec check count =
        count > number/2I || (number % count <> 0I && check (count + 1I))
    check 2I

let computeNthPrime (number) =
     if (number < 1) then
         invalidOp <| sprintf "Invalid input for nth prime: %s." (number.ToString())
     let mutable count = 0
     let mutable num = 1I
     let isDone = false 
     while (count < number) do
         num <- num + 1I
         if (num < bigint System.Int32.MaxValue) then 
             while (not (isprime (int num))) do
                 num <- num + 1I
         else 
             while (not (isprimeBigInt num)) do
                 num <- num + 1I
         count <- count + 1
     num

let async1 context value =
    let asyncTryWith =
        async {
                    try 
                        let nthPrime = ref 0I
                        for count in 1 .. value - 1 do 
                            // The cancellation check is implicit and 
                            // cooperative at for!, do!, and so on.
                            nthPrime := computeNthPrime(count)
                            // Report progress as a percentage of the total task. 
                            let percentComplete = (int)((float)count /
                                                        (float)value * 100.0)
                            do! Async.SwitchToContext(context)
                            progressBar.Value <- percentComplete
                            do! Async.SwitchToThreadPool()
                        // Handle the case in which the operation succeeds. 
                        do! Async.SwitchToContext(context)
                        label1.Text <- sprintf "%s" ((!nthPrime).ToString())
                    with 
                        | e -> 
                            // Handle the case in which an exception is thrown. 
                            do! Async.SwitchToContext(context)
                            MessageBox.Show(e.Message) |> ignore
                }
    async {
        try 
            do! Async.TryCancelled(asyncTryWith,
                                   (fun oce -> 
                                      // Handle the case in which the user cancels the operation.
                                      context.Post((fun _ ->
                                          label1.Text <- "Canceled"), null)))
        finally 
            context.Post((fun _ ->
                updown1.Enabled <- true
                startAsyncButton.Enabled <- true
                cancelAsyncButton.Enabled <- false),
                null)
    }

startAsyncButton.Click.Add(fun args -> 
    cancelAsyncButton.Enabled <- true 
    let context = System.Threading.SynchronizationContext.Current
    Async.Start(async1 context (int updown1.Value)))
button2.Click.Add(fun args ->
   let context = System.Threading.SynchronizationContext.Current
   Async.Start(async1 context (int (-updown1.Value))))
cancelAsyncButton.Click.Add(fun args -> Async.CancelDefaultToken())
Application.Run(form)
```

## Platforms

Windows 8, Windows 7, Windows Server 2012, Windows Server 2008 R2

## Version Information

**F# Core Library Versions**

Supported in: 2.0, 4.0, Portable

## See Also

[Control.Async Class](Control.Async-Class-%5BFSharp%5D.md)

[Microsoft.FSharp.Control Namespace](Microsoft.FSharp.Control-Namespace-%5BFSharp%5D.md)