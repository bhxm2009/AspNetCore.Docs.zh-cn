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
ms.openlocfilehash: e1189a0c196e0026f616cc4204e7b6f3c3c06ae1
ms.sourcegitcommit: 79126facbd939cd21bd3efd02dd5daa4985c6d6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715759"
---
# <a name="aspnet-core-razor-component-lifecycle"></a><span data-ttu-id="1139f-103">ASP.NET Core Razor 组件生命周期</span><span class="sxs-lookup"><span data-stu-id="1139f-103">ASP.NET Core Razor component lifecycle</span></span>

<span data-ttu-id="1139f-104">Razor 组件处理一组同步和异步生命周期方法中的 Razor 组件生命周期事件。</span><span class="sxs-lookup"><span data-stu-id="1139f-104">The Razor component processes Razor component lifecycle events in a set of synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="1139f-105">可以替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="1139f-105">The lifecycle methods can be overridden to perform additional operations in components during component initialization and rendering.</span></span>

## <a name="lifecycle-events"></a><span data-ttu-id="1139f-106">生命周期事件</span><span class="sxs-lookup"><span data-stu-id="1139f-106">Lifecycle events</span></span>

<span data-ttu-id="1139f-107">下图展示的是 Razor 组件生命周期事件。</span><span class="sxs-lookup"><span data-stu-id="1139f-107">The following diagrams illustrate Razor component lifecycle events.</span></span> <span data-ttu-id="1139f-108">本文以下部分中的示例定义了与生命周期事件关联的 C# 方法。</span><span class="sxs-lookup"><span data-stu-id="1139f-108">The C# methods associated with the lifecycle events are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="1139f-109">组件生命周期事件：</span><span class="sxs-lookup"><span data-stu-id="1139f-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="1139f-110">如果组件是第一次呈现在请求上：</span><span class="sxs-lookup"><span data-stu-id="1139f-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="1139f-111">创建组件的实例。</span><span class="sxs-lookup"><span data-stu-id="1139f-111">Create the component's instance.</span></span>
   * <span data-ttu-id="1139f-112">执行属性注入。</span><span class="sxs-lookup"><span data-stu-id="1139f-112">Perform property injection.</span></span> <span data-ttu-id="1139f-113">运行 `SetParametersAsync`。</span><span class="sxs-lookup"><span data-stu-id="1139f-113">Run [`SetParametersAsync`](#when-parameters-are-set-setparametersasync).</span></span>
   * <span data-ttu-id="1139f-114">调用 [`OnInitialized{Async}`](#component-initialization-oninitializedasync)。</span><span class="sxs-lookup"><span data-stu-id="1139f-114">Call [`OnInitialized{Async}`](#component-initialization-oninitializedasync).</span></span> <span data-ttu-id="1139f-115">如果返回不完整的 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1139f-115">If an incomplete <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>
1. <span data-ttu-id="1139f-116">调用 [`OnParametersSet{Async}`](#after-parameters-are-set-onparameterssetasync)。</span><span class="sxs-lookup"><span data-stu-id="1139f-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set-onparameterssetasync).</span></span> <span data-ttu-id="1139f-117">如果返回不完整的 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1139f-117">If an incomplete <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>
1. <span data-ttu-id="1139f-118">呈现所有同步工作和完整的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="1139f-118">Render for all synchronous work and complete <xref:System.Threading.Tasks.Task>s.</span></span>

![Blazor 中 Razor 组件的组件生命周期事件](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="1139f-120">文档对象模型 (DOM) 事件处理：</span><span class="sxs-lookup"><span data-stu-id="1139f-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="1139f-121">运行事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1139f-121">The event handler is run.</span></span>
1. <span data-ttu-id="1139f-122">如果返回不完整的 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1139f-122">If an incomplete <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>
1. <span data-ttu-id="1139f-123">呈现所有同步工作和完整的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="1139f-123">Render for all synchronous work and complete <xref:System.Threading.Tasks.Task>s.</span></span>

![文档对象模型 (DOM) 事件处理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="1139f-125">`Render` 生命周期：</span><span class="sxs-lookup"><span data-stu-id="1139f-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="1139f-126">避免对组件进行进一步的呈现操作：</span><span class="sxs-lookup"><span data-stu-id="1139f-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="1139f-127">在第一次呈现后。</span><span class="sxs-lookup"><span data-stu-id="1139f-127">After the first render.</span></span>
   * <span data-ttu-id="1139f-128">当 [`ShouldRender`](#suppress-ui-refreshing-shouldrender) 为 `false` 时。</span><span class="sxs-lookup"><span data-stu-id="1139f-128">When [`ShouldRender`](#suppress-ui-refreshing-shouldrender) is `false`.</span></span>
1. <span data-ttu-id="1139f-129">生成呈现树差异并呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1139f-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="1139f-130">等待 DOM 更新。</span><span class="sxs-lookup"><span data-stu-id="1139f-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="1139f-131">调用 [`OnAfterRender{Async}`](#after-component-render-onafterrenderasync)。</span><span class="sxs-lookup"><span data-stu-id="1139f-131">Call [`OnAfterRender{Async}`](#after-component-render-onafterrenderasync).</span></span>

![呈现生命周期](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="1139f-133">开发人员调用 [`StateHasChanged`](#state-changes-statehaschanged) 会产生呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-133">Developer calls to [`StateHasChanged`](#state-changes-statehaschanged) result in a render.</span></span> <span data-ttu-id="1139f-134">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="1139f-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="when-parameters-are-set-setparametersasync"></a><span data-ttu-id="1139f-135">设置参数时 (`SetParametersAsync`)</span><span class="sxs-lookup"><span data-stu-id="1139f-135">When parameters are set (`SetParametersAsync`)</span></span>

<span data-ttu-id="1139f-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 设置由组件的父组件在呈现树或路由参数中提供的参数。</span><span class="sxs-lookup"><span data-stu-id="1139f-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span>

<span data-ttu-id="1139f-137">每次调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 时，方法的 <xref:Microsoft.AspNetCore.Components.ParameterView> 参数都包含该组件的[组件参数](xref:blazor/components/index#component-parameters)值集。</span><span class="sxs-lookup"><span data-stu-id="1139f-137">The method's <xref:Microsoft.AspNetCore.Components.ParameterView> parameter contains the set of [component parameter](xref:blazor/components/index#component-parameters) values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span> <span data-ttu-id="1139f-138">通过重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，开发人员代码可以直接与 <xref:Microsoft.AspNetCore.Components.ParameterView> 参数交互。</span><span class="sxs-lookup"><span data-stu-id="1139f-138">By overriding the <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method, developer code can interact directly with <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="1139f-139"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 的默认实现使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)（在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中具有对应的值）设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="1139f-139">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="1139f-140">在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中没有对应值的参数保持不变。</span><span class="sxs-lookup"><span data-stu-id="1139f-140">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="1139f-141">如果未调用 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)，则开发人员代码可使用任何需要的方式解释传入的参数值。</span><span class="sxs-lookup"><span data-stu-id="1139f-141">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, developer code can interpret the incoming parameters' values in any way required.</span></span> <span data-ttu-id="1139f-142">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="1139f-142">For example, there's no requirement to assign the incoming parameters to the properties of the class.</span></span>

<span data-ttu-id="1139f-143">如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="1139f-143">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="1139f-144">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="1139f-144">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

<span data-ttu-id="1139f-145">在下面的示例中，如果分析 `Param` 路由参数成功，则 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> 会将 `Param` 参数值分配给 `value`。</span><span class="sxs-lookup"><span data-stu-id="1139f-145">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="1139f-146">如果 `value` 不是 `null`，则由组件显示值。</span><span class="sxs-lookup"><span data-stu-id="1139f-146">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="1139f-147">尽管[路由参数匹配不区分大小写](xref:blazor/fundamentals/routing#route-parameters)，但 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 仅匹配路由模板中区分大小写的参数名称。</span><span class="sxs-lookup"><span data-stu-id="1139f-147">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="1139f-148">以下示例需要使用路由模板中的 `/{Param?}` 来获取具有 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A>（而不是 `/{param?}`）的值。</span><span class="sxs-lookup"><span data-stu-id="1139f-148">The following example requires the use of `/{Param?}` in the route template in order to get the value with <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A>, not `/{param?}`.</span></span> <span data-ttu-id="1139f-149">如果在此方案中使用 `/{param?}`，则 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 返回 `false`，并且 `message` 未设置为任一 `message` 字符串。</span><span class="sxs-lookup"><span data-stu-id="1139f-149">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either `message` string.</span></span>

<span data-ttu-id="1139f-150">`Pages/SetParamsAsync.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-150">`Pages/SetParamsAsync.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

## <a name="component-initialization-oninitializedasync"></a><span data-ttu-id="1139f-151">组件初始化 (`OnInitialized{Async}`)</span><span class="sxs-lookup"><span data-stu-id="1139f-151">Component initialization (`OnInitialized{Async}`)</span></span>

<span data-ttu-id="1139f-152">组件在接收 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 中的初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> are invoked when the component is initialized after having received its initial parameters in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span>

<span data-ttu-id="1139f-153">对于同步操作，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>：</span><span class="sxs-lookup"><span data-stu-id="1139f-153">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

<span data-ttu-id="1139f-154">`Pages/OnInit.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-154">`Pages/OnInit.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

<span data-ttu-id="1139f-155">若要执行异步操作，请替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 并使用 [`await`](/dotnet/csharp/language-reference/operators/await) 运算符：</span><span class="sxs-lookup"><span data-stu-id="1139f-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="1139f-156">[预呈现其内容](xref:blazor/fundamentals/signalr#render-mode)的 Blazor 应用调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 两次：</span><span class="sxs-lookup"><span data-stu-id="1139f-156">Blazor apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="1139f-157">在组件最初作为页面的一部分静态呈现时调用一次。</span><span class="sxs-lookup"><span data-stu-id="1139f-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="1139f-158">浏览器第二次呈现组件时。</span><span class="sxs-lookup"><span data-stu-id="1139f-158">A second time when the browser renders the component.</span></span>

<span data-ttu-id="1139f-159">为了在 Blazor Server 应用中预呈现时防止 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="1139f-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice when prerendering in Blazor Server apps, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span> <span data-ttu-id="1139f-160">尽管本部分中的内容重点介绍 Blazor Server 和有状态 SignalR 重新连接，但在托管 Blazor WebAssembly 应用 (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) 中预呈现的方案涉及相似的条件和防止执行两次开发人员代码的方法。</span><span class="sxs-lookup"><span data-stu-id="1139f-160">Although the content in the section focuses on Blazor Server and stateful SignalR *reconnection*, the scenario for prerendering in hosted Blazor WebAssembly apps (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) involves similar conditions and approaches to prevent executing developer code twice.</span></span> <span data-ttu-id="1139f-161">新状态保留功能是针对 ASP.NET Core 6.0 版本计划的，该功能将改进在预呈现期间对初始化代码执行的管理。</span><span class="sxs-lookup"><span data-stu-id="1139f-161">A *new state preservation feature* is planned for the ASP.NET Core 6.0 release that will improve the management of initialization code execution during prerendering.</span></span>

<span data-ttu-id="1139f-162">在 Blazor 应用进行预呈现时，无法执行调用 JavaScript（JS 互操作）等特定操作。</span><span class="sxs-lookup"><span data-stu-id="1139f-162">While a Blazor app is prerendering, certain actions, such as calling into JavaScript (JS interop), aren't possible.</span></span> <span data-ttu-id="1139f-163">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-163">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="1139f-164">有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="1139f-164">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="1139f-165">如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="1139f-165">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="1139f-166">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="1139f-166">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

## <a name="after-parameters-are-set-onparameterssetasync"></a><span data-ttu-id="1139f-167">设置参数之后 (`OnParametersSet{Async}`)</span><span class="sxs-lookup"><span data-stu-id="1139f-167">After parameters are set (`OnParametersSet{Async}`)</span></span>

<span data-ttu-id="1139f-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 在以下情况下调用：</span><span class="sxs-lookup"><span data-stu-id="1139f-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> are called:</span></span>

* <span data-ttu-id="1139f-169">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中初始化组件后。</span><span class="sxs-lookup"><span data-stu-id="1139f-169">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="1139f-170">当父组件重新呈现并提供以下内容时：</span><span class="sxs-lookup"><span data-stu-id="1139f-170">When the parent component rerenders and supplies:</span></span>
  * <span data-ttu-id="1139f-171">至少一个参数已更改时的已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="1139f-171">Known primitive immutable types when at least one parameter has changed.</span></span>
  * <span data-ttu-id="1139f-172">复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="1139f-172">Complex-typed parameters.</span></span> <span data-ttu-id="1139f-173">框架无法知道复杂类型参数的值是否在内部发生了改变，因此，如果存在一个或多个复杂类型的参数，框架始终将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="1139f-173">The framework can't know whether the values of a complex-typed parameter have mutated internally, so the framework always treats the parameter set as changed when one or more complex-typed parameters are present.</span></span>

<span data-ttu-id="1139f-174">对于以下示例组件，请导航到 URL 中的组件页面：</span><span class="sxs-lookup"><span data-stu-id="1139f-174">For the following example component, navigate to the component's page at a URL:</span></span>

* <span data-ttu-id="1139f-175">有 `StartDate` 收到的开始日期：`/on-parameters-set/2021-03-19`</span><span class="sxs-lookup"><span data-stu-id="1139f-175">With a start date that's received by `StartDate`: `/on-parameters-set/2021-03-19`</span></span>
* <span data-ttu-id="1139f-176">没有开始日期，其中 `StartDate` 分配有当前本地时间的值：`/on-parameters-set`</span><span class="sxs-lookup"><span data-stu-id="1139f-176">Without a start date, where `StartDate` is assigned a value of the current local time: `/on-parameters-set`</span></span>

<span data-ttu-id="1139f-177">`Pages/OnParamsSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-177">`Pages/OnParamsSet.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1139f-178">在组件路由中，无法约束具有[路由约束 `datetime`](xref:blazor/fundamentals/routing#route-constraints) 的 <xref:System.DateTime> 参数，也无法[使参数成为可选参数](xref:blazor/fundamentals/routing#route-parameters)。</span><span class="sxs-lookup"><span data-stu-id="1139f-178">In a component route, it isn't possible to both constrain a <xref:System.DateTime> parameter with the [route constraint `datetime`](xref:blazor/fundamentals/routing#route-constraints) and [make the parameter optional](xref:blazor/fundamentals/routing#route-parameters).</span></span> <span data-ttu-id="1139f-179">因此，以下 `OnParamsSet` 组件使用两个 [`@page`](xref:mvc/views/razor#page) 指令来处理具有和没有 URL 中提供的日期段的路由。</span><span class="sxs-lookup"><span data-stu-id="1139f-179">Therefore, the following `OnParamsSet` component uses two [`@page`](xref:mvc/views/razor#page) directives to handle routing with and without a supplied date segment in the URL.</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

<span data-ttu-id="1139f-180">应用参数和属性值时，异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命周期事件期间发生：</span><span class="sxs-lookup"><span data-stu-id="1139f-180">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

<span data-ttu-id="1139f-181">如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="1139f-181">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="1139f-182">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="1139f-182">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

<span data-ttu-id="1139f-183">有关路由参数和约束的详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="1139f-183">For more information on route parameters and constraints, see <xref:blazor/fundamentals/routing>.</span></span>

## <a name="after-component-render-onafterrenderasync"></a><span data-ttu-id="1139f-184">组件呈现之后 (`OnAfterRender{Async}`)</span><span class="sxs-lookup"><span data-stu-id="1139f-184">After component render (`OnAfterRender{Async}`)</span></span>

<span data-ttu-id="1139f-185"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 在组件完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="1139f-185"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="1139f-186">此时会填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="1139f-186">Element and component references are populated at this point.</span></span> <span data-ttu-id="1139f-187">在此阶段中，可使用呈现的内容执行其他初始化步骤，例如与呈现的 DOM 元素交互的 JS 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="1139f-187">Use this stage to perform additional initialization steps with the rendered content, such as JS interop calls that interact with the rendered DOM elements.</span></span>

<span data-ttu-id="1139f-188"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="1139f-188">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>:</span></span>

* <span data-ttu-id="1139f-189">在第一次呈现组件实例时设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1139f-189">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="1139f-190">可用于确保初始化操作仅执行一次。</span><span class="sxs-lookup"><span data-stu-id="1139f-190">Can be used to ensure that initialization work is only performed once.</span></span>

<span data-ttu-id="1139f-191">`Pages/AfterRender.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-191">`Pages/AfterRender.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

<span data-ttu-id="1139f-192">呈现后立即进行的异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命周期事件期间发生：</span><span class="sxs-lookup"><span data-stu-id="1139f-192">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event:</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

<span data-ttu-id="1139f-193">即使从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。</span><span class="sxs-lookup"><span data-stu-id="1139f-193">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="1139f-194">这是为了避免无限呈现循环。</span><span class="sxs-lookup"><span data-stu-id="1139f-194">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="1139f-195">这与其他生命周期方法不同，后者在返回的<xref:System.Threading.Tasks.Task> 完成后会再安排呈现循环。</span><span class="sxs-lookup"><span data-stu-id="1139f-195">This is different from the other lifecycle methods, which schedule a further render cycle once a returned <xref:System.Threading.Tasks.Task> completes.</span></span>

<span data-ttu-id="1139f-196">在服务器上的预呈现过程中，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="1139f-197">在预呈现后以交互方式呈现组件时，将调用这些方法。</span><span class="sxs-lookup"><span data-stu-id="1139f-197">The methods are called when the component is rendered interactively after prerendering.</span></span> <span data-ttu-id="1139f-198">当应用预呈现时：</span><span class="sxs-lookup"><span data-stu-id="1139f-198">When the app prerenders:</span></span>

1. <span data-ttu-id="1139f-199">组件将在服务器上执行，以在 HTTP 响应中生成一些静态 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="1139f-199">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="1139f-200">在此阶段，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-200">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="1139f-201">当 Blazor 脚本（`blazor.webassembly.js` 或 `blazor.server.js`）在浏览器中启动时，组件将以交互呈现模式重新启动。</span><span class="sxs-lookup"><span data-stu-id="1139f-201">When the Blazor script (`blazor.webassembly.js` or `blazor.server.js`) start in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="1139f-202">组件重新启动后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>，因为应用不再处于预呈现阶段。</span><span class="sxs-lookup"><span data-stu-id="1139f-202">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't in the prerendering phase any longer.</span></span>

<span data-ttu-id="1139f-203">如果在开发人员代码中提供了事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="1139f-203">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="1139f-204">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="1139f-204">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

## <a name="suppress-ui-refreshing-shouldrender"></a><span data-ttu-id="1139f-205">禁止 UI 刷新 (`ShouldRender`)</span><span class="sxs-lookup"><span data-stu-id="1139f-205">Suppress UI refreshing (`ShouldRender`)</span></span>

<span data-ttu-id="1139f-206">每次呈现组件时都会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-206"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time a component is rendered.</span></span> <span data-ttu-id="1139f-207">替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以管理 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="1139f-207">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to manage UI refreshing.</span></span> <span data-ttu-id="1139f-208">如果实现返回 `true`，则刷新 UI。</span><span class="sxs-lookup"><span data-stu-id="1139f-208">If the implementation returns `true`, the UI is refreshed.</span></span>

<span data-ttu-id="1139f-209">即使 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被替代，组件也始终在最初呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-209">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="1139f-210">`Pages/ControlRender.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-210">`Pages/ControlRender.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

<span data-ttu-id="1139f-211">有关与 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 相关的性能最佳做法的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>。</span><span class="sxs-lookup"><span data-stu-id="1139f-211">For more information on performance best practices pertaining to <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes-statehaschanged"></a><span data-ttu-id="1139f-212">状态更改 (`StateHasChanged`)</span><span class="sxs-lookup"><span data-stu-id="1139f-212">State changes (`StateHasChanged`)</span></span>

<span data-ttu-id="1139f-213"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知组件其状态已更改。</span><span class="sxs-lookup"><span data-stu-id="1139f-213"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="1139f-214">如果适用，调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-214">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="1139f-215">将自动为 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-215"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="1139f-216">有关事件回调的详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="1139f-216">For more information on event callbacks, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="1139f-217">有关组件呈现以及何时调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 的详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="1139f-217">For more information on component rendering and when to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="1139f-218">处理呈现时的不完整异步操作</span><span class="sxs-lookup"><span data-stu-id="1139f-218">Handle incomplete async actions at render</span></span>

<span data-ttu-id="1139f-219">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="1139f-219">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="1139f-220">执行生命周期方法时，对象可能为 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="1139f-220">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="1139f-221">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="1139f-221">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="1139f-222">对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="1139f-222">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="1139f-223">在 Blazor 模板的 `FetchData` 组件中，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 以异步接收预测数据 (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="1139f-223">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="1139f-224">当 `forecasts` 为 `null` 时，将向用户显示加载消息。</span><span class="sxs-lookup"><span data-stu-id="1139f-224">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="1139f-225"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-225">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="1139f-226">Blazor Server 模板中的 `Pages/FetchData.razor`：</span><span class="sxs-lookup"><span data-stu-id="1139f-226">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="1139f-227">处理错误</span><span class="sxs-lookup"><span data-stu-id="1139f-227">Handle errors</span></span>

<span data-ttu-id="1139f-228">有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/fundamentals/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="1139f-228">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="1139f-229">预呈现后的有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="1139f-229">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="1139f-230">在 Blazor Server 应用中，当 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> 时，组件最初作为页面的一部分静态呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-230">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="1139f-231">浏览器重新建立与服务器的 SignalR 连接后，将再次呈现组件，并且该组件为交互式。</span><span class="sxs-lookup"><span data-stu-id="1139f-231">Once the browser establishes a SignalR connection back to the server, the component is rendered *again* and interactive.</span></span> <span data-ttu-id="1139f-232">如果存在用于初始化组件的 [`OnInitialized{Async}`](#component-initialization-oninitializedasync) 生命周期方法，则该方法执行两次：</span><span class="sxs-lookup"><span data-stu-id="1139f-232">If the [`OnInitialized{Async}`](#component-initialization-oninitializedasync) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="1139f-233">在静态预呈现组件时执行一次。</span><span class="sxs-lookup"><span data-stu-id="1139f-233">When the component is prerendered statically.</span></span>
* <span data-ttu-id="1139f-234">在建立服务器连接后执行一次。</span><span class="sxs-lookup"><span data-stu-id="1139f-234">After the server connection has been established.</span></span>

<span data-ttu-id="1139f-235">在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。</span><span class="sxs-lookup"><span data-stu-id="1139f-235">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span> <span data-ttu-id="1139f-236">若要避免在 Blazor Server 应用中出现此双重呈现行为，请传递一个标识符以在预呈现期间缓存状态并在预呈现后检索状态。</span><span class="sxs-lookup"><span data-stu-id="1139f-236">To avoid this double-rendering behavior in a Blazor Server app, pass in an identifier to cache the state during prerendering and to retrieve the state after prerendering.</span></span>

<span data-ttu-id="1139f-237">以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现。</span><span class="sxs-lookup"><span data-stu-id="1139f-237">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering.</span></span> <span data-ttu-id="1139f-238">在以下示例中，等待的 <xref:System.Threading.Tasks.Task.Delay%2A> (`await Task.Delay(...)`) 模拟先短暂延迟，然后再从 `GetForecastAsync` 方法返回数据。</span><span class="sxs-lookup"><span data-stu-id="1139f-238">In the following example, the awaited <xref:System.Threading.Tasks.Task.Delay%2A> (`await Task.Delay(...)`) simulates a short delay before returning data from the `GetForecastAsync` method.</span></span>

<span data-ttu-id="1139f-239">`WeatherForecastService.cs`:</span><span class="sxs-lookup"><span data-stu-id="1139f-239">`WeatherForecastService.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

<span data-ttu-id="1139f-240">有关 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 的详细信息，请参阅 <xref:blazor/fundamentals/signalr#render-mode>。</span><span class="sxs-lookup"><span data-stu-id="1139f-240">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

<span data-ttu-id="1139f-241">尽管本部分中的内容重点介绍 Blazor Server 和有状态 SignalR 重新连接，但在托管 Blazor WebAssembly 应用 (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) 中预呈现的方案涉及相似的条件和防止执行两次开发人员代码的方法。</span><span class="sxs-lookup"><span data-stu-id="1139f-241">Although the content in this section focuses on Blazor Server and stateful SignalR *reconnection*, the scenario for prerendering in hosted Blazor WebAssembly apps (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) involves similar conditions and approaches to prevent executing developer code twice.</span></span> <span data-ttu-id="1139f-242">新状态保留功能是针对 ASP.NET Core 6.0 版本计划的，该功能将改进在预呈现期间对初始化代码执行的管理。</span><span class="sxs-lookup"><span data-stu-id="1139f-242">A *new state preservation feature* is planned for the ASP.NET Core 6.0 release that will improve the management of initialization code execution during prerendering.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="1139f-243">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="1139f-243">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="1139f-244">使用 `IDisposable` 处置组件</span><span class="sxs-lookup"><span data-stu-id="1139f-244">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="1139f-245">如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，框架将调用[处置方法](/dotnet/standard/garbage-collection/implementing-dispose)，在 UI 中可以释放非托管资源。</span><span class="sxs-lookup"><span data-stu-id="1139f-245">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="1139f-246">可随时进行处置，包括在[组件初始化](#component-initialization-oninitializedasync)期间。</span><span class="sxs-lookup"><span data-stu-id="1139f-246">Disposal can occur at any time, including during [component initialization](#component-initialization-oninitializedasync).</span></span> <span data-ttu-id="1139f-247">以下组件通过 [`@implements`](xref:mvc/views/razor#implements) Razor 指令来实现 <xref:System.IDisposable>：</span><span class="sxs-lookup"><span data-stu-id="1139f-247">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="1139f-248">如果对象需要释放，则在调用 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> 时，可使用 Lambda 来释放对象。</span><span class="sxs-lookup"><span data-stu-id="1139f-248">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called.</span></span> <span data-ttu-id="1139f-249">以下示例显示在 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> 一文中，并演示如何使用 Lambda 表达式来释放 <xref:System.Timers.Timer>。</span><span class="sxs-lookup"><span data-stu-id="1139f-249">The following example appears in the <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> article and demonstrates the use of a lambda expression for the disposal of a <xref:System.Timers.Timer>.</span></span>

<span data-ttu-id="1139f-250">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-250">`Pages/CounterWithTimerDisposal.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

<span data-ttu-id="1139f-251">对于异步释放任务，请使用 `DisposeAsync` 而不是 <xref:System.IDisposable.Dispose>：</span><span class="sxs-lookup"><span data-stu-id="1139f-251">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="1139f-252">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-252">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="1139f-253"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="1139f-253"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="1139f-254">取消订阅 .NET 事件中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1139f-254">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="1139f-255">下面的 [Blazor 窗体](xref:blazor/forms-validation)示例演示如何取消订阅 `Dispose` 方法中的事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="1139f-255">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="1139f-256">专用字段和 Lambda 方法</span><span class="sxs-lookup"><span data-stu-id="1139f-256">Private field and lambda approach</span></span>

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* <span data-ttu-id="1139f-257">专用方法</span><span class="sxs-lookup"><span data-stu-id="1139f-257">Private method approach</span></span>

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="1139f-258">专用字段和 Lambda 方法</span><span class="sxs-lookup"><span data-stu-id="1139f-258">Private field and lambda approach</span></span>

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* <span data-ttu-id="1139f-259">专用方法</span><span class="sxs-lookup"><span data-stu-id="1139f-259">Private method approach</span></span>

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

<span data-ttu-id="1139f-260">使用[匿名函数](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)、方法或表达式时，无需实现 <xref:System.IDisposable> 和取消订阅委托。</span><span class="sxs-lookup"><span data-stu-id="1139f-260">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods, or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="1139f-261">但是，当公开事件的对象的生存期长于注册委托的组件的生存期时，不能取消订阅委托是一个问题。</span><span class="sxs-lookup"><span data-stu-id="1139f-261">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="1139f-262">发生这种情况时，会导致内存泄漏，因为已注册的委托使原始对象保持活动状态。</span><span class="sxs-lookup"><span data-stu-id="1139f-262">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="1139f-263">因此，仅当你知道事件委托可快速释放时，才使用以下方法。</span><span class="sxs-lookup"><span data-stu-id="1139f-263">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="1139f-264">当不确定需要释放的对象的生存期时，请订阅委托方法并正确地释放委托，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="1139f-264">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the earlier examples show.</span></span>

* <span data-ttu-id="1139f-265">匿名 Lambda 方法（无需显式释放）：</span><span class="sxs-lookup"><span data-stu-id="1139f-265">Anonymous lambda method approach (explicit disposal not required):</span></span>

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

* <span data-ttu-id="1139f-266">匿名 Lambda 表达式方法（无需显式释放）：</span><span class="sxs-lookup"><span data-stu-id="1139f-266">Anonymous lambda expression approach (explicit disposal not required):</span></span>

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

  <span data-ttu-id="1139f-267">前面带有匿名 Lambda 表达式的完整代码示例显示在 <xref:blazor/forms-validation#validator-components> 一文中。</span><span class="sxs-lookup"><span data-stu-id="1139f-267">The full example of the preceding code with anonymous lambda expressions appears in the <xref:blazor/forms-validation#validator-components> article.</span></span>

<span data-ttu-id="1139f-268">有关详细信息，请参阅[清理非托管资源](/dotnet/standard/garbage-collection/unmanaged)以及后续关于实现 `Dispose` 和 `DisposeAsync` 方法的主题。</span><span class="sxs-lookup"><span data-stu-id="1139f-268">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="1139f-269">可取消的后台工作</span><span class="sxs-lookup"><span data-stu-id="1139f-269">Cancelable background work</span></span>

<span data-ttu-id="1139f-270">组件通常会执行长时间运行的后台工作，如进行网络调用 (<xref:System.Net.Http.HttpClient>) 以及与数据库交互。</span><span class="sxs-lookup"><span data-stu-id="1139f-270">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="1139f-271">在几种情况下，最好停止后台工作以节省系统资源。</span><span class="sxs-lookup"><span data-stu-id="1139f-271">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="1139f-272">例如，当用户离开组件时，后台异步操作不会自动停止。</span><span class="sxs-lookup"><span data-stu-id="1139f-272">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="1139f-273">后台工作项可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="1139f-273">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="1139f-274">正在执行的后台任务由错误的输入数据或处理参数启动。</span><span class="sxs-lookup"><span data-stu-id="1139f-274">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="1139f-275">正在执行的一组后台工作项必须替换为一组新的工作项。</span><span class="sxs-lookup"><span data-stu-id="1139f-275">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="1139f-276">必须更改当前正在执行的任务的优先级。</span><span class="sxs-lookup"><span data-stu-id="1139f-276">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="1139f-277">必须关闭应用进行服务器重新部署。</span><span class="sxs-lookup"><span data-stu-id="1139f-277">The app must be shut down for server redeployment.</span></span>
* <span data-ttu-id="1139f-278">服务器资源受到限制，需要重新计划后台工作项。</span><span class="sxs-lookup"><span data-stu-id="1139f-278">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="1139f-279">要在组件中实现可取消的后台工作模式：</span><span class="sxs-lookup"><span data-stu-id="1139f-279">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="1139f-280">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken>。</span><span class="sxs-lookup"><span data-stu-id="1139f-280">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="1139f-281">在[释放组件](#component-disposal-with-idisposable)时，以及需要随时通过手动取消标记进行取消时，请调用 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示应取消后台工作。</span><span class="sxs-lookup"><span data-stu-id="1139f-281">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="1139f-282">异步调用返回后，对该标记调用 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>。</span><span class="sxs-lookup"><span data-stu-id="1139f-282">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="1139f-283">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1139f-283">In the following example:</span></span>

* <span data-ttu-id="1139f-284">`await Task.Delay(5000, cts.Token);` 表示长时间运行的异步后台工作。</span><span class="sxs-lookup"><span data-stu-id="1139f-284">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="1139f-285">`BackgroundResourceMethod` 表示如果在调用方法之前释放 `Resource`，则不应启动的长时间运行的后台方法。</span><span class="sxs-lookup"><span data-stu-id="1139f-285">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

<span data-ttu-id="1139f-286">`Pages/BackgroundWork.razor`:</span><span class="sxs-lookup"><span data-stu-id="1139f-286">`Pages/BackgroundWork.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="1139f-287">Blazor Server 重新连接事件</span><span class="sxs-lookup"><span data-stu-id="1139f-287">Blazor Server reconnection events</span></span>

<span data-ttu-id="1139f-288">本文所述的组件生命周期事件与 [Blazor Server 的重新连接事件处理程序](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)分开运行。</span><span class="sxs-lookup"><span data-stu-id="1139f-288">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="1139f-289">当 Blazor Server 应用断开其与客户端的 SignalR 连接时，只有 UI 更新会被中断。</span><span class="sxs-lookup"><span data-stu-id="1139f-289">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="1139f-290">重新建立连接后，将恢复 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="1139f-290">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="1139f-291">有关线路处理程序事件和配置的详细信息，请参阅 <xref:blazor/fundamentals/signalr>。</span><span class="sxs-lookup"><span data-stu-id="1139f-291">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
