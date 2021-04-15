---
title: ASP.NET Core Razor 组件生命周期
author: guardrex
description: 了解 ASP.NET Core Razor 组件生命周期以及如何使用生命周期事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/25/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 12cc308f08be0961f1b14579753d653927bcf683
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081554"
---
# <a name="aspnet-core-razor-component-lifecycle"></a>ASP.NET Core Razor 组件生命周期

Razor 组件处理一组同步和异步生命周期方法中的 Razor 组件生命周期事件。 可以替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。

## <a name="lifecycle-events"></a>生命周期事件

下图展示的是 Razor 组件生命周期事件。 本文以下部分中的示例定义了与生命周期事件关联的 C# 方法。

组件生命周期事件：

1. 如果组件是第一次呈现在请求上：
   * 创建组件的实例。
   * 执行属性注入。 运行 `SetParametersAsync`。
   * 调用 [`OnInitialized{Async}`](#component-initialization-oninitializedasync)。 如果返回不完整的 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。
1. 调用 [`OnParametersSet{Async}`](#after-parameters-are-set-onparameterssetasync)。 如果返回不完整的 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。
1. 呈现所有同步工作和完整的 <xref:System.Threading.Tasks.Task>。

![Blazor 中 Razor 组件的组件生命周期事件](lifecycle/_static/lifecycle1.png)

文档对象模型 (DOM) 事件处理：

1. 运行事件处理程序。
1. 如果返回不完整的 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。
1. 呈现所有同步工作和完整的 <xref:System.Threading.Tasks.Task>。

![文档对象模型 (DOM) 事件处理](lifecycle/_static/lifecycle2.png)

`Render` 生命周期：

1. 避免对组件进行进一步的呈现操作：
   * 在第一次呈现后。
   * 当 [`ShouldRender`](#suppress-ui-refreshing-shouldrender) 为 `false` 时。
1. 生成呈现树差异并呈现组件。
1. 等待 DOM 更新。
1. 调用 [`OnAfterRender{Async}`](#after-component-render-onafterrenderasync)。

![呈现生命周期](lifecycle/_static/lifecycle3.png)

开发人员调用 [`StateHasChanged`](#state-changes-statehaschanged) 会产生呈现。 有关详细信息，请参阅 <xref:blazor/components/rendering>。

## <a name="when-parameters-are-set-setparametersasync"></a>设置参数时 (`SetParametersAsync`)

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 设置由组件的父组件在呈现树或路由参数中提供的参数。

每次调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 时，方法的 <xref:Microsoft.AspNetCore.Components.ParameterView> 参数都包含该组件的[组件参数](xref:blazor/components/index#parameters)值集。 通过重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，开发人员代码可以直接与 <xref:Microsoft.AspNetCore.Components.ParameterView> 参数交互。

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 的默认实现使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)（在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中具有对应的值）设置每个属性的值。 在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中没有对应值的参数保持不变。

如果未调用 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)，则开发人员代码可使用任何需要的方式解释传入的参数值。 例如，不要求将传入参数分配给类的属性。

如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。 有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。

在下面的示例中，如果分析 `Param` 路由参数成功，则 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> 会将 `Param` 参数值分配给 `value`。 如果 `value` 不是 `null`，则由组件显示值。

尽管[路由参数匹配不区分大小写](xref:blazor/fundamentals/routing#route-parameters)，但 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 仅匹配路由模板中区分大小写的参数名称。 以下示例需要使用路由模板中的 `/{Param?}` 来获取具有 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A>（而不是 `/{param?}`）的值。 如果在此方案中使用 `/{param?}`，则 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 返回 `false`，并且 `message` 未设置为任一 `message` 字符串。

`Pages/SetParamsAsync.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

## <a name="component-initialization-oninitializedasync"></a>组件初始化 (`OnInitialized{Async}`)

组件在接收 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 中的初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>。

对于同步操作，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>：

`Pages/OnInit.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

若要执行异步操作，请替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 并使用 [`await`](/dotnet/csharp/language-reference/operators/await) 运算符：

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

[预呈现其内容](xref:blazor/fundamentals/signalr#render-mode)的 Blazor 应用调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 两次：

* 在组件最初作为页面的一部分静态呈现时调用一次。
* 浏览器第二次呈现组件时。

为了在 Blazor Server 应用中预呈现时防止 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。 尽管本部分中的内容重点介绍 Blazor Server 和有状态 SignalR 重新连接，但在托管 Blazor WebAssembly 应用 (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) 中预呈现的方案涉及相似的条件和防止执行两次开发人员代码的方法。 新状态保留功能是针对 ASP.NET Core 6.0 版本计划的，该功能将改进在预呈现期间对初始化代码执行的管理。

在 Blazor 应用进行预呈现时，无法执行调用 JavaScript（JS 互操作）等特定操作。 预呈现时，组件可能需要进行不同的呈现。 有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。

如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。 有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。

## <a name="after-parameters-are-set-onparameterssetasync"></a>设置参数之后 (`OnParametersSet{Async}`)

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 在以下情况下调用：

* 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中初始化组件后。
* 当父组件重新呈现并提供以下内容时：
  * 至少一个参数已更改时的已知基元不可变类型。
  * 复杂类型的参数。 框架无法知道复杂类型参数的值是否在内部发生了改变，因此，如果存在一个或多个复杂类型的参数，框架始终将参数集视为已更改。

对于以下示例组件，请导航到 URL 中的组件页面：

* 有 `StartDate` 收到的开始日期：`/on-parameters-set/2021-03-19`
* 没有开始日期，其中 `StartDate` 分配有当前本地时间的值：`/on-parameters-set`

`Pages/OnParamsSet.razor`:

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 在组件路由中，无法约束具有[路由约束 `datetime`](xref:blazor/fundamentals/routing#route-constraints) 的 <xref:System.DateTime> 参数，也无法[使参数成为可选参数](xref:blazor/fundamentals/routing#route-parameters)。 因此，以下 `OnParamsSet` 组件使用两个 [`@page`](xref:mvc/views/razor#page) 指令来处理具有和没有 URL 中提供的日期段的路由。

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

应用参数和属性值时，异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命周期事件期间发生：

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。 有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。

有关路由参数和约束的详细信息，请参阅 <xref:blazor/fundamentals/routing>。

## <a name="after-component-render-onafterrenderasync"></a>组件呈现之后 (`OnAfterRender{Async}`)

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 在组件完成呈现后调用。 此时会填充元素和组件引用。 在此阶段中，可使用呈现的内容执行其他初始化步骤，例如与呈现的 DOM 元素交互的 JS 互操作调用。

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 的 `firstRender` 参数：

* 在第一次呈现组件实例时设置为 `true`。
* 可用于确保初始化操作仅执行一次。

`Pages/AfterRender.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

呈现后立即进行的异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命周期事件期间发生：

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

即使从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。 这是为了避免无限呈现循环。 这与其他生命周期方法不同，后者在返回的<xref:System.Threading.Tasks.Task> 完成后会再安排呈现循环。

在服务器上的预呈现过程中，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。 在预呈现后以交互方式呈现组件时，将调用这些方法。 当应用预呈现时：

1. 组件将在服务器上执行，以在 HTTP 响应中生成一些静态 HTML 标记。 在此阶段，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。
1. 当 Blazor 脚本（`blazor.webassembly.js` 或 `blazor.server.js`）在浏览器中启动时，组件将以交互呈现模式重新启动。 组件重新启动后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>，因为应用不再处于预呈现阶段。

如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。 有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。

## <a name="suppress-ui-refreshing-shouldrender"></a>禁止 UI 刷新 (`ShouldRender`)

每次呈现组件时都会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>。 替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以管理 UI 刷新。 如果实现返回 `true`，则刷新 UI。

即使 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被替代，组件也始终在最初呈现。

`Pages/ControlRender.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

有关与 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 相关的性能最佳做法的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>。

## <a name="state-changes-statehaschanged"></a>状态更改 (`StateHasChanged`)

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知组件其状态已更改。 如果适用，调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会导致组件重新呈现。

将自动为 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。 有关事件回调的详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback>。

有关组件呈现以及何时调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 的详细信息，请参阅 <xref:blazor/components/rendering>。

## <a name="handle-incomplete-async-actions-at-render"></a>处理呈现时的不完整异步操作

在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。 执行生命周期方法时，对象可能为 `null` 或未完全填充数据。 提供呈现逻辑以确认对象已初始化。 对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。

在 Blazor 模板的 `FetchData` 组件中，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 以异步接收预测数据 (`forecasts`)。 当 `forecasts` 为 `null` 时，将向用户显示加载消息。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。

Blazor Server 模板中的 `Pages/FetchData.razor`：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a>处理错误

有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/fundamentals/handle-errors>。

## <a name="stateful-reconnection-after-prerendering"></a>预呈现后的有状态重新连接

在 Blazor Server 应用中，当 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> 时，组件最初作为页面的一部分静态呈现。 浏览器重新建立与服务器的 SignalR 连接后，将再次呈现组件，并且该组件为交互式。 如果存在用于初始化组件的 [`OnInitialized{Async}`](#component-initialization-oninitializedasync) 生命周期方法，则该方法执行两次：

* 在静态预呈现组件时执行一次。
* 在建立服务器连接后执行一次。

在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。 若要避免在 Blazor Server 应用中出现此双重呈现行为，请传递一个标识符以在预呈现期间缓存状态并在预呈现后检索状态。

以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现。 在以下示例中，等待的 <xref:System.Threading.Tasks.Task.Delay%2A> (`await Task.Delay(...)`) 模拟先短暂延迟，然后再从 `GetForecastAsync` 方法返回数据。

`WeatherForecastService.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

有关 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 的详细信息，请参阅 <xref:blazor/fundamentals/signalr#render-mode>。

尽管本部分中的内容重点介绍 Blazor Server 和有状态 SignalR 重新连接，但在托管 Blazor WebAssembly 应用 (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) 中预呈现的方案涉及相似的条件和防止执行两次开发人员代码的方法。 新状态保留功能是针对 ASP.NET Core 6.0 版本计划的，该功能将改进在预呈现期间对初始化代码执行的管理。

## <a name="detect-when-the-app-is-prerendering"></a>检测应用何时预呈现

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>使用 `IDisposable` 处置组件

如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，框架将调用[处置方法](/dotnet/standard/garbage-collection/implementing-dispose)，在 UI 中可以释放非托管资源。 可随时进行处置，包括在[组件初始化](#component-initialization-oninitializedasync)期间。 以下组件通过 [`@implements`](xref:mvc/views/razor#implements) Razor 指令来实现 <xref:System.IDisposable>：

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

如果对象需要释放，则在调用 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> 时，可使用 Lambda 来释放对象。 以下示例显示在 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> 一文中，并演示如何使用 Lambda 表达式来释放 <xref:System.Timers.Timer>。

`Pages/CounterWithTimerDisposal.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

对于异步释放任务，请使用 `DisposeAsync` 而不是 <xref:System.IDisposable.Dispose>：

```csharp
public async ValueTask DisposeAsync()
{
    await ...
}
```

> [!NOTE]
> 不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。

取消订阅 .NET 事件中的事件处理程序。 下面的 [Blazor 窗体](xref:blazor/forms-validation)示例演示如何取消订阅 `Dispose` 方法中的事件处理程序：

::: moniker range=">= aspnetcore-5.0"

* 专用字段和 Lambda 方法

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* 专用方法

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* 专用字段和 Lambda 方法

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* 专用方法

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

使用[匿名函数](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)、方法或表达式时，无需实现 <xref:System.IDisposable> 和取消订阅委托。 但是，当公开事件的对象的生存期长于注册委托的组件的生存期时，不能取消订阅委托是一个问题。 发生这种情况时，会导致内存泄漏，因为已注册的委托使原始对象保持活动状态。 因此，仅当你知道事件委托可快速释放时，才使用以下方法。 当不确定需要释放的对象的生存期时，请订阅委托方法并正确地释放委托，如前面的示例所示。

* 匿名 Lambda 方法（无需显式释放）：

  ::: moniker range=">= aspnetcore-5.0"

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

  ::: moniker-end

* 匿名 Lambda 表达式方法（无需显式释放）：

  ::: moniker range=">= aspnetcore-5.0"

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  ::: moniker-end

  前面带有匿名 Lambda 表达式的完整代码示例显示在 <xref:blazor/forms-validation#validator-components> 一文中。

有关详细信息，请参阅[清理非托管资源](/dotnet/standard/garbage-collection/unmanaged)以及后续关于实现 `Dispose` 和 `DisposeAsync` 方法的主题。

## <a name="cancelable-background-work"></a>可取消的后台工作

组件通常会执行长时间运行的后台工作，如进行网络调用 (<xref:System.Net.Http.HttpClient>) 以及与数据库交互。 在几种情况下，最好停止后台工作以节省系统资源。 例如，当用户离开组件时，后台异步操作不会自动停止。

后台工作项可能需要取消的其他原因包括：

* 正在执行的后台任务由错误的输入数据或处理参数启动。
* 正在执行的一组后台工作项必须替换为一组新的工作项。
* 必须更改当前正在执行的任务的优先级。
* 必须关闭应用进行服务器重新部署。
* 服务器资源受到限制，需要重新计划后台工作项。

要在组件中实现可取消的后台工作模式：

* 使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken>。
* 在[释放组件](#component-disposal-with-idisposable)时，以及需要随时通过手动取消标记进行取消时，请调用 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示应取消后台工作。
* 异步调用返回后，对该标记调用 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>。

如下示例中：

* `await Task.Delay(5000, cts.Token);` 表示长时间运行的异步后台工作。
* `BackgroundResourceMethod` 表示如果在调用方法之前释放 `Resource`，则不应启动的长时间运行的后台方法。

`Pages/BackgroundWork.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

## <a name="blazor-server-reconnection-events"></a>Blazor Server 重新连接事件

本文所述的组件生命周期事件与 [Blazor Server 的重新连接事件处理程序](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)分开运行。 当 Blazor Server 应用断开其与客户端的 SignalR 连接时，只有 UI 更新会被中断。 重新建立连接后，将恢复 UI 更新。 有关线路处理程序事件和配置的详细信息，请参阅 <xref:blazor/fundamentals/signalr>。
