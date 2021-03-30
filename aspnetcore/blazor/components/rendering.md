---
title: ASP.NET Core Blazor 组件呈现
author: guardrex
description: 了解 ASP.NET Core Blazor 应用中的 Razor 组件呈现，包括何时调用 StateHasChanged。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/rendering
ms.openlocfilehash: 1d244434cd3aa7e1a49839cc0c6cecb61abbcdff
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711329"
---
# <a name="aspnet-core-blazor-component-rendering"></a>ASP.NET Core Blazor 组件呈现

当组件第一次通过父组件添加到组件层次结构时，它们必须呈现。 只有在这种情况下，组件才必须呈现。 在其他情况下，组件可以按照各自的逻辑和约定呈现。

## <a name="rendering-conventions-for-componentbase"></a>`ComponentBase` 的呈现约定

默认情况下，Razor 组件继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基类，该基类包含在以下时间触发重新呈现的逻辑：

* 从父组件应用一组已更新的[参数](xref:blazor/components/data-binding#binding-with-component-parameters)之后。
* 为[级联参数](xref:blazor/components/cascading-values-and-parameters)应用已更新的值之后。
* 通知事件并调用其自己的某个[事件处理程序](xref:blazor/components/event-handling)之后。
* 调用自己的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 方法之后（请参阅 [Blazor 生命周期：状态更改](xref:blazor/components/lifecycle#state-changes)）。

如果满足以下任一条件，则继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的组件会跳过因参数更新而触发的重新呈现：

* 所有参数值都是已知的不可变基元类型（例如，`int`、`string`、`DateTime`），并且自上一组参数设置后就没有改变过。
* 组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法返回 `false`。

有关 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。

## <a name="control-the-rendering-flow"></a>控制呈现流

在大多数情况下，<xref:Microsoft.AspNetCore.Components.ComponentBase> 约定会在某个事件发生后导致重新呈现正确的组件子集。 开发人员通常不需要提供手动逻辑来告诉框架，要重新呈现哪些组件以及何时重新呈现它们。 框架约定的总体效果如下：接收事件的组件重新呈现自身，从而递归触发参数值可能已更改的后代组件的重新呈现。

有关框架约定对性能的影响以及如何针对呈现优化应用的组件层次结构的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>。

## <a name="when-to-call-statehaschanged"></a>何时调用 `StateHasChanged`

通过调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，可以随时触发呈现。 但请注意，不必要时，不要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，这是一个常见错误，会产生不必要的呈现成本。

对于以下情况，代码不需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：

* 定期处理事件（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发大多数常规事件处理程序的呈现。
* 实现 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 等典型生命周期逻辑（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发典型生命周期事件的呈现。

但是，在本文以下部分所述的情况下，可能适合调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：

* [异步处理程序涉及多个异步阶段](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [从 Blazor 呈现和事件处理系统外部接收调用](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [在由特定事件重新呈现的子树之外呈现组件](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>异步处理程序涉及多个异步阶段

由于在 .NET 中定义任务的方式，<xref:System.Threading.Tasks.Task> 的接收方只能观察到其最终完成状态，而观察不到中间异步状态。 因此，仅在第一次返回 <xref:System.Threading.Tasks.Task> 且 <xref:System.Threading.Tasks.Task> 最终完成时，<xref:Microsoft.AspNetCore.Components.ComponentBase> 才能触发重新呈现。 框架无法知道要在其他中间点重新呈现组件。 如果要在中间点重新呈现，请在这些点调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。

以下面的 `CounterState1` 组件为例，该组件在每次单击时都会更新计数四次：

* 自动呈现在 `currentCount` 第一次和最后一次递增之后发生。
* 当框架未在 `currentCount` 递增的中间处理点自动触发重新呈现时，通过调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 触发手动呈现。

`Pages/CounterState1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a>从 Blazor 呈现和事件处理系统外部接收调用

<xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道其自身的生命周期方法和 Blazor 触发的事件。 <xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道代码中可能发生的其他事件。 例如，Blazor 不知道自定义数据存储引发的任何 C# 事件。 为了使此类事件触发重新呈现，从而在 UI 中显示已更新的值，请调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。

以下面的 `CounterState2` 组件为例，该组件使用 <xref:System.Timers.Timer?displayProperty=fullName> 定期更新计数并调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 来更新 UI：

* `OnTimerCallback` 在 Blazor 管理的任何呈现流或事件通知之外运行。 因此，`OnTimerCallback` 必须调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，因为 Blazor 不知道回调中的 `currentCount` 更改。
* 组件实现 <xref:System.IDisposable>，当框架调用 `Dispose` 方法时，其中的 <xref:System.Timers.Timer> 将释放。 有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

由于回调是在 Blazor 的同步上下文之外调用的，因此组件必须将 `OnTimerCallback` 的逻辑包装在 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 中，以将其移到呈现器的同步上下文中。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能从呈现器的同步上下文调用，否则会引发异常。 这等效于封送到其他 UI 框架中的 UI 线程。

`Pages/CounterState2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>在由特定事件重新呈现的子树之外呈现组件

UI 可能涉及：

1. 向一个组件调度事件。
1. 更改某种状态。
1. 重新呈现完全不同的组件（该组件不是接收事件的组件的后代）。

处理此情况的一种方法是，提供状态管理类（通常作为依赖关系注入 (DI) 服务）注入到多个组件。 当一个组件在状态管理器上调用方法时，状态管理器引发 C# 事件，然后由独立组件接收该事件。

由于这些 C# 事件位于 Blazor 呈现管道之外，因此，请对要呈现的其他组件调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，以响应状态管理器的事件。

这与前面的 <xref:System.Timers.Timer?displayProperty=fullName> 案例（[上一部分](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)中）类似。 由于执行调用堆栈通常保留在呈现器的同步上下文中，因此通常不需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>。 仅当逻辑转义同步上下文时才需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>，例如，对 <xref:System.Threading.Tasks.Task> 调用 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 或使用 [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 等待 <xref:System.Threading.Tasks.Task>。
