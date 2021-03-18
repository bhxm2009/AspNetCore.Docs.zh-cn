---
title: 处理 ASP.NET Core Blazor 应用中的错误
author: guardrex
description: 了解 ASP.NET Core Blazor 如何管理未经处理的异常以及如何开发用于检测和处理错误的应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395092"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="269c5-103">处理 ASP.NET Core Blazor 应用中的错误</span><span class="sxs-lookup"><span data-stu-id="269c5-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="269c5-104">本文介绍 Blazor 如何管理未经处理的异常以及如何开发用于检测和处理错误的应用。</span><span class="sxs-lookup"><span data-stu-id="269c5-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="269c5-105">开发过程中的错误详细信息</span><span class="sxs-lookup"><span data-stu-id="269c5-105">Detailed errors during development</span></span>

<span data-ttu-id="269c5-106">当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。</span><span class="sxs-lookup"><span data-stu-id="269c5-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="269c5-107">出现错误时，Blazor 应用会在屏幕底部显示一个浅黄色条框：</span><span class="sxs-lookup"><span data-stu-id="269c5-107">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="269c5-108">在开发过程中，这个条框会将你定向到浏览器控制台，你可在其中查看异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-108">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="269c5-109">在生产过程中，这个条框会通知用户发生了错误，并建议刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="269c5-109">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="269c5-110">此错误处理体验的 UI 属于 [Blazor 项目模板](xref:blazor/project-structure)。</span><span class="sxs-lookup"><span data-stu-id="269c5-110">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="269c5-111">在 Blazor WebAssembly 应用的 `wwwroot/index.html` 文件中自定义体验：</span><span class="sxs-lookup"><span data-stu-id="269c5-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="269c5-112">`blazor-error-ui` 元素通常是隐藏的，因为应用样式表 (`wwwroot/css/app.css`) 中存在 `blazor-error-ui` CSS 类的 `display: none` 样式。</span><span class="sxs-lookup"><span data-stu-id="269c5-112">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the app's stylesheet (`wwwroot/css/app.css`).</span></span> <span data-ttu-id="269c5-113">当发生错误时，框架将 `display: block` 应用于该元素。</span><span class="sxs-lookup"><span data-stu-id="269c5-113">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="269c5-114">在开发人员代码中管理未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="269c5-114">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="269c5-115">若要在出现错误后继续运行应用，该应用必须具备错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="269c5-115">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="269c5-116">本文后面的部分将介绍未经处理的异常出现的潜在原因。</span><span class="sxs-lookup"><span data-stu-id="269c5-116">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="269c5-117">在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-117">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="269c5-118">呈现异常消息或堆栈跟踪信息可能导致：</span><span class="sxs-lookup"><span data-stu-id="269c5-118">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="269c5-119">向最终用户公开敏感信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-119">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="269c5-120">帮助恶意用户发现应用中可能会危及应用、服务器或网络安全的弱点。</span><span class="sxs-lookup"><span data-stu-id="269c5-120">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="269c5-121">全局异常处理</span><span class="sxs-lookup"><span data-stu-id="269c5-121">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="269c5-122">使用永久性提供程序记录错误信息</span><span class="sxs-lookup"><span data-stu-id="269c5-122">Log errors with a persistent provider</span></span>

<span data-ttu-id="269c5-123">在发生未经处理的异常时，将异常记录到在服务容器中配置的 <xref:Microsoft.Extensions.Logging.ILogger> 实例。</span><span class="sxs-lookup"><span data-stu-id="269c5-123">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="269c5-124">默认情况下，Blazor 应用使用控制台日志记录提供程序记录到控制台输出中。</span><span class="sxs-lookup"><span data-stu-id="269c5-124">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="269c5-125">考虑将日志记录到服务器上保存时间更长久的位置，方法是将错误信息发送到后端 Web API，并且该 API 使用具有日志大小管理和日志轮替功能的日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="269c5-125">Consider logging to a more permanent location on the server by sending error information to a backend web API that uses a logging provider with log size management and log rotation.</span></span> <span data-ttu-id="269c5-126">或者，后端 Web API 应用可以使用应用程序性能管理 (APM) 服务（如 [Azure Application Insights (Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview)）来记录从客户端接收的错误信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-126">Alternatively, the backend web API app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview), to record error information that it receives from clients.</span></span>

<span data-ttu-id="269c5-127">必须确定要记录的事件以及已记录的事件的严重性级别。</span><span class="sxs-lookup"><span data-stu-id="269c5-127">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="269c5-128">恶意用户也许能刻意触发错误。</span><span class="sxs-lookup"><span data-stu-id="269c5-128">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="269c5-129">例如，若显示产品详细信息的组件的 URL 中提供了未知的 `ProductId`，则请勿记录错误中的事件。</span><span class="sxs-lookup"><span data-stu-id="269c5-129">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="269c5-130">并非所有的错误都应被视为需要记录的事件。</span><span class="sxs-lookup"><span data-stu-id="269c5-130">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="269c5-131">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="269c5-131">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

<span data-ttu-id="269c5-132">&dagger;用于支持 Blazor WebAssembly 应用的本机 [Application Insights](/azure/azure-monitor/app/app-insights-overview) 功能和对 [Google Analytics](https://analytics.google.com/analytics/web/) 的本机 Blazor 框架支持可能会在这些技术的未来版本中推出。</span><span class="sxs-lookup"><span data-stu-id="269c5-132">&dagger;Native [Application Insights](/azure/azure-monitor/app/app-insights-overview) features to support Blazor WebAssembly apps and native Blazor framework support for [Google Analytics](https://analytics.google.com/analytics/web/) might become available in future releases of these technologies.</span></span> <span data-ttu-id="269c5-133">有关更多信息，请参阅[在 Blazor WASM 客户端支持 App Insights (microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) 和 [Web 分析和诊断（包含社区实现链接）(dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461)。</span><span class="sxs-lookup"><span data-stu-id="269c5-133">For more information, see [Support App Insights in Blazor WASM Client Side (microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) and [Web analytics and diagnostics (includes links to community implementations) (dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461).</span></span> <span data-ttu-id="269c5-134">同时，客户端 Blazor WebAssembly 应用可以结合使用 [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript) 和 [JS互操作](xref:blazor/call-javascript-from-dotnet)直接将错误从客户端应用记录到 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="269c5-134">In the meantime, a client-side Blazor WebAssembly app can use the [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript) with [JS interop](xref:blazor/call-javascript-from-dotnet) to log errors directly to Application Insights from a client-side app.</span></span>

<span data-ttu-id="269c5-135">&Dagger;适用于服务器端 ASP.NET Core 应用，这些应用是 Blazor 应用的 Web API 后端应用。</span><span class="sxs-lookup"><span data-stu-id="269c5-135">&Dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span> <span data-ttu-id="269c5-136">客户端应用捕获错误信息，并发送到 Web API，该 API 将错误信息记录到持久日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="269c5-136">Client-side apps trap and send error information to a web API, which logs the error information to a persistent logging provider.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="269c5-137">可能发生错误的位置</span><span class="sxs-lookup"><span data-stu-id="269c5-137">Places where errors may occur</span></span>

<span data-ttu-id="269c5-138">框架和应用代码可能会在以下任何位置触发未处理异常，相关的具体内容将在本文后续部分介绍：</span><span class="sxs-lookup"><span data-stu-id="269c5-138">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="269c5-139">组件实例化</span><span class="sxs-lookup"><span data-stu-id="269c5-139">Component instantiation</span></span>](#component-instantiation-webassembly)
* [<span data-ttu-id="269c5-140">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="269c5-140">Lifecycle methods</span></span>](#lifecycle-methods-webassembly)
* [<span data-ttu-id="269c5-141">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="269c5-141">Rendering logic</span></span>](#rendering-logic-webassembly)
* [<span data-ttu-id="269c5-142">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="269c5-142">Event handlers</span></span>](#event-handlers-webassembly)
* [<span data-ttu-id="269c5-143">组件处置</span><span class="sxs-lookup"><span data-stu-id="269c5-143">Component disposal</span></span>](#component-disposal-webassembly)
* [<span data-ttu-id="269c5-144">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="269c5-144">JavaScript interop</span></span>](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly"><span data-ttu-id="269c5-145">组件实例化</span><span class="sxs-lookup"><span data-stu-id="269c5-145">Component instantiation</span></span></h3>

<span data-ttu-id="269c5-146">当 Blazor 创建某组件的实例时：</span><span class="sxs-lookup"><span data-stu-id="269c5-146">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="269c5-147">会调用该组件的构造函数。</span><span class="sxs-lookup"><span data-stu-id="269c5-147">The component's constructor is invoked.</span></span>
* <span data-ttu-id="269c5-148">会调用通过 [`@inject`](xref:mvc/views/razor#inject) 指令或 [`[Inject]` 特性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供给组件构造函数的非单一 DI 服务的构造函数。</span><span class="sxs-lookup"><span data-stu-id="269c5-148">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="269c5-149">已执行构造函数中或任何 `[Inject]` 属性的 setter 中发生错误，可能会引起未处理异常，并阻止框架对组件进行实例化。</span><span class="sxs-lookup"><span data-stu-id="269c5-149">An error in an executed constructor or a setter for any `[Inject]` property results in an unhandled exception and stops the framework from instantiating the component.</span></span> <span data-ttu-id="269c5-150">如果构造函数逻辑可能引发异常，应用应使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-150">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-webassembly"><span data-ttu-id="269c5-151">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="269c5-151">Lifecycle methods</span></span></h3>

<span data-ttu-id="269c5-152">在组件的生命周期内，Blazor 会调用 [ 生命周期方法 ](xref:blazor/components/lifecycle#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="269c5-152">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="269c5-153">若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="269c5-153">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="269c5-154">在下面的示例中，<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 会调用方法来获取产品：</span><span class="sxs-lookup"><span data-stu-id="269c5-154">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="269c5-155">`ProductRepository.GetProductByIdAsync` 方法中引发的异常由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句处理。</span><span class="sxs-lookup"><span data-stu-id="269c5-155">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="269c5-156">在执行 `catch` 块时：</span><span class="sxs-lookup"><span data-stu-id="269c5-156">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="269c5-157">`loadFailed` 设置为 `true`，用于向用户显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="269c5-157">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="269c5-158">错误会被记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-158">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly"><span data-ttu-id="269c5-159">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="269c5-159">Rendering logic</span></span></h3>

<span data-ttu-id="269c5-160">Razor 组件文件 (`.razor`) 中的声明性标记被编译到名为 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 的 C# 方法中。</span><span class="sxs-lookup"><span data-stu-id="269c5-160">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="269c5-161">当组件呈现时，<xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 会执行并构建一个数据结构，该结构描述所呈现组件的元素、文本和子组件。</span><span class="sxs-lookup"><span data-stu-id="269c5-161">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="269c5-162">呈现逻辑可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-162">Rendering logic can throw an exception.</span></span> <span data-ttu-id="269c5-163">例如评估了 `@someObject.PropertyName`，但 `@someObject` 为 `null` 时，就会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="269c5-163">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span>

<span data-ttu-id="269c5-164">为防止呈现逻辑中出现 <xref:System.NullReferenceException>，请在访问其成员之前检查是否存在 `null` 对象。</span><span class="sxs-lookup"><span data-stu-id="269c5-164">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="269c5-165">在以下示例中，如果 `person.Address` 为 `null`，则不访问 `person.Address` 属性：</span><span class="sxs-lookup"><span data-stu-id="269c5-165">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="269c5-166">上述代码假定 `person` 不是 `null`。</span><span class="sxs-lookup"><span data-stu-id="269c5-166">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="269c5-167">通常，代码的结构保证了呈现组件时存在对象。</span><span class="sxs-lookup"><span data-stu-id="269c5-167">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="269c5-168">在这些情况下，不需要检查呈现逻辑中是否存在 `null`。</span><span class="sxs-lookup"><span data-stu-id="269c5-168">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="269c5-169">在前面的示例中，由于在实例化组件时创建了 `person`，因此可保证存在 `person`，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="269c5-169">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly"><span data-ttu-id="269c5-170">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="269c5-170">Event handlers</span></span></h3>

<span data-ttu-id="269c5-171">使用以下内容创建事件处理程序时，客户端代码将触发 C# 代码调用：</span><span class="sxs-lookup"><span data-stu-id="269c5-171">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="269c5-172">其他 `@on...` 特性</span><span class="sxs-lookup"><span data-stu-id="269c5-172">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="269c5-173">在这些情况下，事件处理程序代码可能会引发未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-173">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="269c5-174">如果应用调用可能因外部原因而失败的代码，请使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-174">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="269c5-175">如果用户代码不会捕获和处理异常，则框架将记录异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-175">If user code doesn't trap and handle the exception, the framework logs the exception.</span></span>

<h3 id="component-disposal-webassembly"><span data-ttu-id="269c5-176">组件处置</span><span class="sxs-lookup"><span data-stu-id="269c5-176">Component disposal</span></span></h3>

<span data-ttu-id="269c5-177">例如，可从 UI 中删除组件，因为用户已导航到其他页面。</span><span class="sxs-lookup"><span data-stu-id="269c5-177">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="269c5-178">当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 <xref:System.IDisposable.Dispose%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="269c5-178">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="269c5-179">如果处置逻辑可能引发异常，应用应使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-179">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="269c5-180">要详细了解组件处置，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="269c5-180">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-webassembly"><span data-ttu-id="269c5-181">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="269c5-181">JavaScript interop</span></span></h3>

<span data-ttu-id="269c5-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允许 .NET 代码在用户浏览器中对 JavaScript 运行时进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="269c5-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="269c5-183">以下条件适用于带有 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的错误处理：</span><span class="sxs-lookup"><span data-stu-id="269c5-183">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="269c5-184">如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行同步调用，则会发生 .NET 异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-184">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="269c5-185">例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，因为不能序列化提供的自变量。</span><span class="sxs-lookup"><span data-stu-id="269c5-185">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="269c5-186">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-186">Developer code must catch the exception.</span></span>
* <span data-ttu-id="269c5-187">如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行异步调用，则 .NET <xref:System.Threading.Tasks.Task> 会失败。</span><span class="sxs-lookup"><span data-stu-id="269c5-187">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="269c5-188">例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，这是因为 JavaScript 端代码会引发异常或返回完成状态为 `rejected` 的 `Promise`。</span><span class="sxs-lookup"><span data-stu-id="269c5-188">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="269c5-189">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-189">Developer code must catch the exception.</span></span> <span data-ttu-id="269c5-190">如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 运算符，请考虑使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句包装方法调用，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-190">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>
* <span data-ttu-id="269c5-191">默认情况下，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。</span><span class="sxs-lookup"><span data-stu-id="269c5-191">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="269c5-192">超时会保护代码免受网络连接丢失的影响，或者保护永远不会发回完成消息的 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="269c5-192">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="269c5-193">如果调用超时，则生成的 <xref:System.Threading.Tasks> 将失败，并出现 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="269c5-193">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="269c5-194">捕获异常，并进行异常处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-194">Trap and process the exception with logging.</span></span>

<span data-ttu-id="269c5-195">同样，JavaScript 代码可以对 [`[JSInvokable]` 特性](xref:blazor/call-dotnet-from-javascript)指示的 .NET 方法发起调用。</span><span class="sxs-lookup"><span data-stu-id="269c5-195">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="269c5-196">如果这些 .NET 方法引发未处理异常，则 JavaScript 端 `Promise` 将遭到拒绝。</span><span class="sxs-lookup"><span data-stu-id="269c5-196">If these .NET methods throw an unhandled exception, the JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="269c5-197">可选择在方法调用的 .NET 端或 JavaScript 端使用错误处理代码。</span><span class="sxs-lookup"><span data-stu-id="269c5-197">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="269c5-198">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="269c5-198">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a><span data-ttu-id="269c5-199">高级方案</span><span class="sxs-lookup"><span data-stu-id="269c5-199">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="269c5-200">递归呈现</span><span class="sxs-lookup"><span data-stu-id="269c5-200">Recursive rendering</span></span>

<span data-ttu-id="269c5-201">组件能以递归方式嵌套。</span><span class="sxs-lookup"><span data-stu-id="269c5-201">Components can be nested recursively.</span></span> <span data-ttu-id="269c5-202">这适用于表示递归数据结构。</span><span class="sxs-lookup"><span data-stu-id="269c5-202">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="269c5-203">例如，`TreeNode` 组件可以为节点的每个子级呈现更多 `TreeNode` 组件。</span><span class="sxs-lookup"><span data-stu-id="269c5-203">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="269c5-204">以递归方式呈现时，请避免采用会导致无限递归的编码模式：</span><span class="sxs-lookup"><span data-stu-id="269c5-204">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="269c5-205">请勿以递归方式呈现包含循环的数据结构。</span><span class="sxs-lookup"><span data-stu-id="269c5-205">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="269c5-206">例如，请勿呈现其子级包含其自身的树节点。</span><span class="sxs-lookup"><span data-stu-id="269c5-206">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="269c5-207">请勿创建包含循环的布局链。</span><span class="sxs-lookup"><span data-stu-id="269c5-207">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="269c5-208">例如，请勿创建布局为其本身的布局。</span><span class="sxs-lookup"><span data-stu-id="269c5-208">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="269c5-209">请勿允许最终用户通过恶意数据输入或 JavaScript 互操作调用违反递归固定协定（规则）。</span><span class="sxs-lookup"><span data-stu-id="269c5-209">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="269c5-210">呈现过程中的无限循环：</span><span class="sxs-lookup"><span data-stu-id="269c5-210">Infinite loops during rendering:</span></span>

* <span data-ttu-id="269c5-211">会导致呈现过程永久地继续下去。</span><span class="sxs-lookup"><span data-stu-id="269c5-211">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="269c5-212">相当于创建不终止的循环。</span><span class="sxs-lookup"><span data-stu-id="269c5-212">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="269c5-213">在这些情况下，线程通常会尝试：</span><span class="sxs-lookup"><span data-stu-id="269c5-213">In these scenarios, the thread usually attempts to:</span></span>

* <span data-ttu-id="269c5-214">在操作系统允许范围内无限期地消耗 CPU 时间。</span><span class="sxs-lookup"><span data-stu-id="269c5-214">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="269c5-215">消耗不限量的客户端内存。</span><span class="sxs-lookup"><span data-stu-id="269c5-215">Consume an unlimited amount of client memory.</span></span> <span data-ttu-id="269c5-216">消耗不限量的内存相当于不终止的循环在每次迭代时向集合添加条目的情况。</span><span class="sxs-lookup"><span data-stu-id="269c5-216">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="269c5-217">若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。</span><span class="sxs-lookup"><span data-stu-id="269c5-217">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="269c5-218">自定义呈现器树逻辑</span><span class="sxs-lookup"><span data-stu-id="269c5-218">Custom render tree logic</span></span>

<span data-ttu-id="269c5-219">大多数 Blazor 组件都实现为 Razor 组件文件 (`.razor`)，并由框架编译以生成在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 上运行的逻辑，从而呈现其输出。</span><span class="sxs-lookup"><span data-stu-id="269c5-219">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="269c5-220">但是，开发人员可使用程序 C# 代码手动实现 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑。</span><span class="sxs-lookup"><span data-stu-id="269c5-220">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="269c5-221">有关详细信息，请参阅 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="269c5-221">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="269c5-222">手动呈现树生成器逻辑被视为一种高级且不安全的方案，不建议开发人员在常规组件开发工作中采用。</span><span class="sxs-lookup"><span data-stu-id="269c5-222">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="269c5-223">如果编写 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 代码，开发人员必须保证代码的正确性。</span><span class="sxs-lookup"><span data-stu-id="269c5-223">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="269c5-224">例如，开发人员必须确保：</span><span class="sxs-lookup"><span data-stu-id="269c5-224">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="269c5-225">对 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的调用已正确均衡。</span><span class="sxs-lookup"><span data-stu-id="269c5-225">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="269c5-226">仅将特性添加到正确的位置。</span><span class="sxs-lookup"><span data-stu-id="269c5-226">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="269c5-227">若手动呈现树生成器逻辑不正确，则可能出现任意未定义的行为，包括崩溃、应用挂起以及安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="269c5-227">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, app hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="269c5-228">请知悉：手动呈现树生成器逻辑的复杂程度和 *危险* 程度与手动编写程序集代码或 [Microsoft 中间语言 (MSIL)](/dotnet/standard/managed-code) 指令是一样的。</span><span class="sxs-lookup"><span data-stu-id="269c5-228">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="269c5-229">其他资源</span><span class="sxs-lookup"><span data-stu-id="269c5-229">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

<span data-ttu-id="269c5-230">&dagger;适用于客户端 Blazor WebAssembly 应用用于日志记录的后端 ASP.NET Core Web API 应用。</span><span class="sxs-lookup"><span data-stu-id="269c5-230">&dagger;Applies to backend ASP.NET Core web API apps that client-side Blazor WebAssembly apps use for logging.</span></span>

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="269c5-231">开发过程中的错误详细信息</span><span class="sxs-lookup"><span data-stu-id="269c5-231">Detailed errors during development</span></span>

<span data-ttu-id="269c5-232">当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。</span><span class="sxs-lookup"><span data-stu-id="269c5-232">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="269c5-233">出现错误时，Blazor 应用会在屏幕底部显示一个浅黄色条框：</span><span class="sxs-lookup"><span data-stu-id="269c5-233">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="269c5-234">在开发过程中，这个条框会将你定向到浏览器控制台，你可在其中查看异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-234">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="269c5-235">在生产过程中，这个条框会通知用户发生了错误，并建议刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="269c5-235">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="269c5-236">此错误处理体验的 UI 属于 [Blazor 项目模板](xref:blazor/project-structure)。</span><span class="sxs-lookup"><span data-stu-id="269c5-236">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="269c5-237">在 Blazor Server 应用的 `Pages/_Host.cshtml` 文件中自定义体验：</span><span class="sxs-lookup"><span data-stu-id="269c5-237">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="269c5-238">`blazor-error-ui` 元素通常是隐藏的，因为站点样式表 (`wwwroot/css/site.css`) 中存在 `blazor-error-ui` CSS 类的 `display: none` 样式。</span><span class="sxs-lookup"><span data-stu-id="269c5-238">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the site's stylesheet (`wwwroot/css/site.css`).</span></span> <span data-ttu-id="269c5-239">当发生错误时，框架将 `display: block` 应用于该元素。</span><span class="sxs-lookup"><span data-stu-id="269c5-239">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="269c5-240">Blazor Server 详细线路错误</span><span class="sxs-lookup"><span data-stu-id="269c5-240">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="269c5-241">客户端错误不包括调用堆栈，也不提供有关错误原因的详细信息，但服务器日志的确包含此类信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-241">Client-side errors don't include the call stack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="269c5-242">出于开发目的，可通过启用详细错误向客户端提供敏感线路错误信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-242">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="269c5-243">将 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="269c5-243">Set <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> to `true`.</span></span> <span data-ttu-id="269c5-244">有关详细信息及示例，请参阅<xref:blazor/fundamentals/signalr#circuit-handler-options>。</span><span class="sxs-lookup"><span data-stu-id="269c5-244">For more information and an example, see <xref:blazor/fundamentals/signalr#circuit-handler-options>.</span></span>

<span data-ttu-id="269c5-245">另一种设置 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> 的方法是在应用开发环境设置文件 (`appsettings.Development.json`) 中将 `DetailedErrors` 配制键设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="269c5-245">An alternative to setting <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> is to set the `DetailedErrors` configuration key to `true` in the app's Development environment settings file (`appsettings.Development.json`).</span></span>  <span data-ttu-id="269c5-246">此外，[SignalR 服务器端日志记录](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) 可设置为[调试](xref:Microsoft.Extensions.Logging.LogLevel)或[跟踪](xref:Microsoft.Extensions.Logging.LogLevel)，以用于详细 SignalR 日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-246">Additionally, set [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="269c5-247">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="269c5-247">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

<span data-ttu-id="269c5-248">在开发/过渡环境服务器或本地系统上，还可以使用 `ASPNETCORE_DETAILEDERRORS` 环境变量和值 `true` 将 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> 配置键设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="269c5-248">The <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> configuration key can also be set to `true` using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true` on Development/Staging environment servers or on your local system.</span></span>

> [!WARNING]
> <span data-ttu-id="269c5-249">始终避免向 Internet 上的客户端公开错误信息，这会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="269c5-249">Always avoid exposing error information to clients on the Internet, which is a security risk.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="269c5-250">Blazor Server 应用如何应对未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="269c5-250">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="269c5-251">Blazor Server 是一种有状态框架。</span><span class="sxs-lookup"><span data-stu-id="269c5-251">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="269c5-252">用户与应用进行交互时，会与服务器保持名为“线路”的连接。</span><span class="sxs-lookup"><span data-stu-id="269c5-252">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="269c5-253">线路包含活动组件实例，以及状态的许多其他方面，例如：</span><span class="sxs-lookup"><span data-stu-id="269c5-253">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="269c5-254">最新呈现的组件输出。</span><span class="sxs-lookup"><span data-stu-id="269c5-254">The most recent rendered output of components.</span></span>
* <span data-ttu-id="269c5-255">可由客户端事件触发的事件处理委托的当前集合。</span><span class="sxs-lookup"><span data-stu-id="269c5-255">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="269c5-256">如果用户在多个浏览器标签页中打开应用，则用户就会创建多条独立线路。</span><span class="sxs-lookup"><span data-stu-id="269c5-256">If a user opens the app in multiple browser tabs, the user creates multiple independent circuits.</span></span>

<span data-ttu-id="269c5-257">Blazor 将大部分未经处理的异常视为发生该异常的线路的严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-257">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="269c5-258">如果线路由于未经处理的异常而终止，则用户只能重新加载页面来创建新线路，从而继续与应用进行交互。</span><span class="sxs-lookup"><span data-stu-id="269c5-258">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="269c5-259">终止的线路以外的其他线路（即其他用户或其他浏览器标签页的线路）不会受到影响。</span><span class="sxs-lookup"><span data-stu-id="269c5-259">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="269c5-260">此场景类似于出现故障的桌面应用。</span><span class="sxs-lookup"><span data-stu-id="269c5-260">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="269c5-261">出现故障的应用必须重新启动，但其他应用不受影响。</span><span class="sxs-lookup"><span data-stu-id="269c5-261">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="269c5-262">当发生未处理异常时，框架会出于以下原因终止线路：</span><span class="sxs-lookup"><span data-stu-id="269c5-262">The framework terminates a circuit when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="269c5-263">未经处理的异常通常会将线路置于未定义状态。</span><span class="sxs-lookup"><span data-stu-id="269c5-263">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="269c5-264">发生未经处理的异常后，应用可能无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="269c5-264">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="269c5-265">如果线路继续保持未定义状态，应用中可能会出现安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="269c5-265">Security vulnerabilities may appear in the app if the circuit continues in an undefined state.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="269c5-266">在开发人员代码中管理未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="269c5-266">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="269c5-267">若要在出现错误后继续运行应用，该应用必须具备错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="269c5-267">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="269c5-268">本文后面的部分将介绍未经处理的异常出现的潜在原因。</span><span class="sxs-lookup"><span data-stu-id="269c5-268">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="269c5-269">在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-269">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="269c5-270">呈现异常消息或堆栈跟踪信息可能导致：</span><span class="sxs-lookup"><span data-stu-id="269c5-270">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="269c5-271">向最终用户公开敏感信息。</span><span class="sxs-lookup"><span data-stu-id="269c5-271">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="269c5-272">帮助恶意用户发现应用中可能会危及应用、服务器或网络安全的弱点。</span><span class="sxs-lookup"><span data-stu-id="269c5-272">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="269c5-273">全局异常处理</span><span class="sxs-lookup"><span data-stu-id="269c5-273">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

<span data-ttu-id="269c5-274">由于本部分中的方法使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句处理错误，因此在发生错误时，客户端和服务器之间的 SignalR 连接不会中断，并且线路保持活跃状态。</span><span class="sxs-lookup"><span data-stu-id="269c5-274">Because the approaches in this section handle errors with a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, the SignalR connection between the client and server isn't broken when an error occurs and the circuit remains alive.</span></span> <span data-ttu-id="269c5-275">任何未处理异常对于线路而言都是致命错误。</span><span class="sxs-lookup"><span data-stu-id="269c5-275">Any unhandled exception is fatal to a circuit.</span></span> <span data-ttu-id="269c5-276">有关更多信息，请参阅上文关于 [Blazor Server 应用如何对未处理异常做出反应](#how-a-blazor-server-app-reacts-to-unhandled-exceptions)的部分。</span><span class="sxs-lookup"><span data-stu-id="269c5-276">For more information, see the preceding section on [how a Blazor Server app reacts to unhandled exceptions](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="269c5-277">使用永久性提供程序记录错误信息</span><span class="sxs-lookup"><span data-stu-id="269c5-277">Log errors with a persistent provider</span></span>

<span data-ttu-id="269c5-278">在发生未经处理的异常时，将异常记录到在服务容器中配置的 <xref:Microsoft.Extensions.Logging.ILogger> 实例。</span><span class="sxs-lookup"><span data-stu-id="269c5-278">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="269c5-279">默认情况下，Blazor 应用使用控制台日志记录提供程序记录到控制台输出中。</span><span class="sxs-lookup"><span data-stu-id="269c5-279">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="269c5-280">请考虑使用管理日志大小和日志轮替的提供程序将日志记录到服务器上保存时间更长久的位置。</span><span class="sxs-lookup"><span data-stu-id="269c5-280">Consider logging to a more permanent location on the server with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="269c5-281">或者，应用也可以使用应用程序性能管理 (APM) 服务，如 [Azure Application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="269c5-281">Alternatively, the app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview).</span></span>

<span data-ttu-id="269c5-282">在开发过程中，Blazor Server 应用通常会将异常的完整详细信息发送到浏览器的控制台，以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="269c5-282">During development, a Blazor Server app usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="269c5-283">在生产环境中，详细错误不会发送到客户端，但异常的完整详细信息会记录在服务器上。</span><span class="sxs-lookup"><span data-stu-id="269c5-283">In production, detailed errors aren't sent to clients, but an exception's full details are logged on the server.</span></span>

<span data-ttu-id="269c5-284">必须确定要记录的事件以及已记录的事件的严重性级别。</span><span class="sxs-lookup"><span data-stu-id="269c5-284">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="269c5-285">恶意用户也许能刻意触发错误。</span><span class="sxs-lookup"><span data-stu-id="269c5-285">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="269c5-286">例如，若显示产品详细信息的组件的 URL 中提供了未知的 `ProductId`，则请勿记录错误中的事件。</span><span class="sxs-lookup"><span data-stu-id="269c5-286">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="269c5-287">并非所有的错误都应被视为需要记录的事件。</span><span class="sxs-lookup"><span data-stu-id="269c5-287">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="269c5-288">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="269c5-288">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="269c5-289">&dagger;适用于服务器端 ASP.NET Core 应用，这些应用是 Blazor 应用的 Web API 后端应用。</span><span class="sxs-lookup"><span data-stu-id="269c5-289">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="269c5-290">可能发生错误的位置</span><span class="sxs-lookup"><span data-stu-id="269c5-290">Places where errors may occur</span></span>

<span data-ttu-id="269c5-291">框架和应用代码可能会在以下任何位置触发未处理异常，相关的具体内容将在本文后续部分介绍：</span><span class="sxs-lookup"><span data-stu-id="269c5-291">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="269c5-292">组件实例化</span><span class="sxs-lookup"><span data-stu-id="269c5-292">Component instantiation</span></span>](#component-instantiation-server)
* [<span data-ttu-id="269c5-293">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="269c5-293">Lifecycle methods</span></span>](#lifecycle-methods-server)
* [<span data-ttu-id="269c5-294">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="269c5-294">Rendering logic</span></span>](#rendering-logic-server)
* [<span data-ttu-id="269c5-295">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="269c5-295">Event handlers</span></span>](#event-handlers-server)
* [<span data-ttu-id="269c5-296">组件处置</span><span class="sxs-lookup"><span data-stu-id="269c5-296">Component disposal</span></span>](#component-disposal-server)
* [<span data-ttu-id="269c5-297">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="269c5-297">JavaScript interop</span></span>](#javascript-interop-server)
* [<span data-ttu-id="269c5-298">Blazor Server 重新呈现</span><span class="sxs-lookup"><span data-stu-id="269c5-298">Blazor Server rerendering</span></span>](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server"><span data-ttu-id="269c5-299">组件实例化</span><span class="sxs-lookup"><span data-stu-id="269c5-299">Component instantiation</span></span></h3>

<span data-ttu-id="269c5-300">当 Blazor 创建某组件的实例时：</span><span class="sxs-lookup"><span data-stu-id="269c5-300">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="269c5-301">会调用该组件的构造函数。</span><span class="sxs-lookup"><span data-stu-id="269c5-301">The component's constructor is invoked.</span></span>
* <span data-ttu-id="269c5-302">会调用通过 [`@inject`](xref:mvc/views/razor#inject) 指令或 [`[Inject]` 特性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供给组件构造函数的非单一 DI 服务的构造函数。</span><span class="sxs-lookup"><span data-stu-id="269c5-302">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="269c5-303">如果任何已执行的构造函数或任何 `[Inject]` 属性的资源库引发了未经处理的异常，则 Blazor Server 线路会失败。</span><span class="sxs-lookup"><span data-stu-id="269c5-303">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="269c5-304">这是严重异常，因为框架无法实例化组件。</span><span class="sxs-lookup"><span data-stu-id="269c5-304">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="269c5-305">如果构造函数逻辑可能引发异常，应用应使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-305">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-server"><span data-ttu-id="269c5-306">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="269c5-306">Lifecycle methods</span></span></h3>

<span data-ttu-id="269c5-307">在组件的生命周期内，Blazor 会调用 [ 生命周期方法 ](xref:blazor/components/lifecycle#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="269c5-307">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="269c5-308">如果任何生命周期方法以同步或异步方式引发异常，则该异常对于 Blazor Server 线路而言是严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-308">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="269c5-309">若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="269c5-309">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="269c5-310">在下面的示例中，<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 会调用方法来获取产品：</span><span class="sxs-lookup"><span data-stu-id="269c5-310">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="269c5-311">`ProductRepository.GetProductByIdAsync` 方法中引发的异常由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句处理。</span><span class="sxs-lookup"><span data-stu-id="269c5-311">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="269c5-312">在执行 `catch` 块时：</span><span class="sxs-lookup"><span data-stu-id="269c5-312">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="269c5-313">`loadFailed` 设置为 `true`，用于向用户显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="269c5-313">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="269c5-314">错误会被记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-314">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server"><span data-ttu-id="269c5-315">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="269c5-315">Rendering logic</span></span></h3>

<span data-ttu-id="269c5-316">Razor 组件文件 (`.razor`) 中的声明性标记被编译到名为 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 的 C# 方法中。</span><span class="sxs-lookup"><span data-stu-id="269c5-316">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="269c5-317">当组件呈现时，<xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 会执行并构建一个数据结构，该结构描述所呈现组件的元素、文本和子组件。</span><span class="sxs-lookup"><span data-stu-id="269c5-317">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="269c5-318">呈现逻辑可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-318">Rendering logic can throw an exception.</span></span> <span data-ttu-id="269c5-319">例如评估了 `@someObject.PropertyName`，但 `@someObject` 为 `null` 时，就会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="269c5-319">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="269c5-320">呈现逻辑引发的未经处理的异常对于 Blazor Server 线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-320">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="269c5-321">为防止呈现逻辑中出现 <xref:System.NullReferenceException>，请在访问其成员之前检查是否存在 `null` 对象。</span><span class="sxs-lookup"><span data-stu-id="269c5-321">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="269c5-322">在以下示例中，如果 `person.Address` 为 `null`，则不访问 `person.Address` 属性：</span><span class="sxs-lookup"><span data-stu-id="269c5-322">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="269c5-323">上述代码假定 `person` 不是 `null`。</span><span class="sxs-lookup"><span data-stu-id="269c5-323">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="269c5-324">通常，代码的结构保证了呈现组件时存在对象。</span><span class="sxs-lookup"><span data-stu-id="269c5-324">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="269c5-325">在这些情况下，不需要检查呈现逻辑中是否存在 `null`。</span><span class="sxs-lookup"><span data-stu-id="269c5-325">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="269c5-326">在前面的示例中，由于在实例化组件时创建了 `person`，因此可保证存在 `person`，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="269c5-326">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server"><span data-ttu-id="269c5-327">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="269c5-327">Event handlers</span></span></h3>

<span data-ttu-id="269c5-328">使用以下内容创建事件处理程序时，客户端代码将触发 C# 代码调用：</span><span class="sxs-lookup"><span data-stu-id="269c5-328">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="269c5-329">其他 `@on...` 特性</span><span class="sxs-lookup"><span data-stu-id="269c5-329">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="269c5-330">在这些情况下，事件处理程序代码可能会引发未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-330">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="269c5-331">如果事件处理程序引发未经处理的异常（例如数据库查询失败），则该异常对于 Blazor Server 线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-331">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="269c5-332">如果应用调用可能因外部原因而失败的代码，请使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-332">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="269c5-333">如果用户代码不会捕获和处理异常，则框架将记录异常并终止线路。</span><span class="sxs-lookup"><span data-stu-id="269c5-333">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

<h3 id="component-disposal-server"><span data-ttu-id="269c5-334">组件处置</span><span class="sxs-lookup"><span data-stu-id="269c5-334">Component disposal</span></span></h3>

<span data-ttu-id="269c5-335">例如，可从 UI 中删除组件，因为用户已导航到其他页面。</span><span class="sxs-lookup"><span data-stu-id="269c5-335">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="269c5-336">当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 <xref:System.IDisposable.Dispose%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="269c5-336">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="269c5-337">如果组件的 `Dispose` 方法引发未经处理的异常，则该异常对于 Blazor Server 线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-337">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="269c5-338">如果处置逻辑可能引发异常，应用应使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-338">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="269c5-339">要详细了解组件处置，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="269c5-339">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-server"><span data-ttu-id="269c5-340">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="269c5-340">JavaScript interop</span></span></h3>

<span data-ttu-id="269c5-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允许 .NET 代码在用户浏览器中对 JavaScript 运行时进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="269c5-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="269c5-342">以下条件适用于带有 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的错误处理：</span><span class="sxs-lookup"><span data-stu-id="269c5-342">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="269c5-343">如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行同步调用，则会发生 .NET 异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-343">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="269c5-344">例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，因为不能序列化提供的自变量。</span><span class="sxs-lookup"><span data-stu-id="269c5-344">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="269c5-345">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-345">Developer code must catch the exception.</span></span> <span data-ttu-id="269c5-346">如果事件处理程序或组件生命周期方法中的应用代码未处理异常，则该异常对于 Blazor Server 线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-346">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="269c5-347">如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行异步调用，则 .NET <xref:System.Threading.Tasks.Task> 会失败。</span><span class="sxs-lookup"><span data-stu-id="269c5-347">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="269c5-348">例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，这是因为 JavaScript 端代码会引发异常或返回完成状态为 `rejected` 的 `Promise`。</span><span class="sxs-lookup"><span data-stu-id="269c5-348">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="269c5-349">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-349">Developer code must catch the exception.</span></span> <span data-ttu-id="269c5-350">如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 运算符，请考虑使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句包装方法调用，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-350">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="269c5-351">否则，失败的代码会导致未经处理的异常，这对于 Blazor Server 线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-351">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="269c5-352">默认情况下，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。</span><span class="sxs-lookup"><span data-stu-id="269c5-352">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="269c5-353">超时会保护代码免受网络连接丢失的影响，或者保护永远不会发回完成消息的 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="269c5-353">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="269c5-354">如果调用超时，则生成的 <xref:System.Threading.Tasks> 将失败，并出现 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="269c5-354">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="269c5-355">捕获异常，并进行异常处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-355">Trap and process the exception with logging.</span></span>

<span data-ttu-id="269c5-356">同样，JavaScript 代码可以对 [`[JSInvokable]` 特性](xref:blazor/call-dotnet-from-javascript)指示的 .NET 方法发起调用。</span><span class="sxs-lookup"><span data-stu-id="269c5-356">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="269c5-357">如果这些 .NET 方法引发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="269c5-357">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="269c5-358">此异常不会被视为 Blazor Server 线路的严重异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-358">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="269c5-359">JavaScript 端 `Promise` 会被拒绝。</span><span class="sxs-lookup"><span data-stu-id="269c5-359">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="269c5-360">可选择在方法调用的 .NET 端或 JavaScript 端使用错误处理代码。</span><span class="sxs-lookup"><span data-stu-id="269c5-360">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="269c5-361">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="269c5-361">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server"><span data-ttu-id="269c5-362">Blazor Server 预呈现</span><span class="sxs-lookup"><span data-stu-id="269c5-362">Blazor Server prerendering</span></span></h3>

<span data-ttu-id="269c5-363">Blazor 组件可使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)进行预呈现，以便在用户的初始 HTTP 请求过程中返回其呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="269c5-363">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="269c5-364">实现方式如下：</span><span class="sxs-lookup"><span data-stu-id="269c5-364">This works by:</span></span>

* <span data-ttu-id="269c5-365">为属于同一页面的所有预呈现组件创建新的线路。</span><span class="sxs-lookup"><span data-stu-id="269c5-365">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="269c5-366">生成初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="269c5-366">Generating the initial HTML.</span></span>
* <span data-ttu-id="269c5-367">将线路视为 `disconnected`，直到用户浏览器与同一服务器重新建立起 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="269c5-367">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="269c5-368">建立该连接后，将恢复线路的交互性，并更新组件的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="269c5-368">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="269c5-369">如果任何组件在预呈现期间引发未经处理的异常，例如在生命周期方法或呈现逻辑中：</span><span class="sxs-lookup"><span data-stu-id="269c5-369">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="269c5-370">则该异常对线路是严重的。</span><span class="sxs-lookup"><span data-stu-id="269c5-370">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="269c5-371">此异常将从 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序中的调用堆栈引发。</span><span class="sxs-lookup"><span data-stu-id="269c5-371">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="269c5-372">这将导致整个 HTTP 请求失败，除非开发人员代码显式捕获该异常。</span><span class="sxs-lookup"><span data-stu-id="269c5-372">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="269c5-373">在正常情况下，如果预呈现失败，则继续生成和呈现组件都将没有作用，因为无法呈现工作组件。</span><span class="sxs-lookup"><span data-stu-id="269c5-373">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="269c5-374">若要容许在预呈现期间可能发生的错误，必须将错误处理逻辑置于可能引发异常的组件中。</span><span class="sxs-lookup"><span data-stu-id="269c5-374">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="269c5-375">请使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="269c5-375">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="269c5-376">请勿将 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序包装在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中，而是将错误处理逻辑放在由 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序呈现的组件中。</span><span class="sxs-lookup"><span data-stu-id="269c5-376">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="269c5-377">高级方案</span><span class="sxs-lookup"><span data-stu-id="269c5-377">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="269c5-378">递归呈现</span><span class="sxs-lookup"><span data-stu-id="269c5-378">Recursive rendering</span></span>

<span data-ttu-id="269c5-379">组件能以递归方式嵌套。</span><span class="sxs-lookup"><span data-stu-id="269c5-379">Components can be nested recursively.</span></span> <span data-ttu-id="269c5-380">这适用于表示递归数据结构。</span><span class="sxs-lookup"><span data-stu-id="269c5-380">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="269c5-381">例如，`TreeNode` 组件可以为节点的每个子级呈现更多 `TreeNode` 组件。</span><span class="sxs-lookup"><span data-stu-id="269c5-381">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="269c5-382">以递归方式呈现时，请避免采用会导致无限递归的编码模式：</span><span class="sxs-lookup"><span data-stu-id="269c5-382">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="269c5-383">请勿以递归方式呈现包含循环的数据结构。</span><span class="sxs-lookup"><span data-stu-id="269c5-383">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="269c5-384">例如，请勿呈现其子级包含其自身的树节点。</span><span class="sxs-lookup"><span data-stu-id="269c5-384">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="269c5-385">请勿创建包含循环的布局链。</span><span class="sxs-lookup"><span data-stu-id="269c5-385">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="269c5-386">例如，请勿创建布局为其本身的布局。</span><span class="sxs-lookup"><span data-stu-id="269c5-386">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="269c5-387">请勿允许最终用户通过恶意数据输入或 JavaScript 互操作调用违反递归固定协定（规则）。</span><span class="sxs-lookup"><span data-stu-id="269c5-387">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="269c5-388">呈现过程中的无限循环：</span><span class="sxs-lookup"><span data-stu-id="269c5-388">Infinite loops during rendering:</span></span>

* <span data-ttu-id="269c5-389">会导致呈现过程永久地继续下去。</span><span class="sxs-lookup"><span data-stu-id="269c5-389">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="269c5-390">相当于创建不终止的循环。</span><span class="sxs-lookup"><span data-stu-id="269c5-390">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="269c5-391">在这些情况下，受影响的 Blazor Server 线路会失败，并且该线程通常会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="269c5-391">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="269c5-392">在操作系统允许范围内无限期地消耗 CPU 时间。</span><span class="sxs-lookup"><span data-stu-id="269c5-392">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="269c5-393">消耗不限量的服务器内存。</span><span class="sxs-lookup"><span data-stu-id="269c5-393">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="269c5-394">消耗不限量的内存相当于不终止的循环在每次迭代时向集合添加条目的情况。</span><span class="sxs-lookup"><span data-stu-id="269c5-394">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="269c5-395">若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。</span><span class="sxs-lookup"><span data-stu-id="269c5-395">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="269c5-396">自定义呈现器树逻辑</span><span class="sxs-lookup"><span data-stu-id="269c5-396">Custom render tree logic</span></span>

<span data-ttu-id="269c5-397">大多数 Blazor 组件都实现为 Razor 组件文件 (`.razor`)，并由框架编译以生成在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 上运行的逻辑，从而呈现其输出。</span><span class="sxs-lookup"><span data-stu-id="269c5-397">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="269c5-398">但是，开发人员可使用程序 C# 代码手动实现 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑。</span><span class="sxs-lookup"><span data-stu-id="269c5-398">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="269c5-399">有关详细信息，请参阅 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="269c5-399">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="269c5-400">手动呈现树生成器逻辑被视为一种高级且不安全的方案，不建议开发人员在常规组件开发工作中采用。</span><span class="sxs-lookup"><span data-stu-id="269c5-400">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="269c5-401">如果编写 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 代码，开发人员必须保证代码的正确性。</span><span class="sxs-lookup"><span data-stu-id="269c5-401">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="269c5-402">例如，开发人员必须确保：</span><span class="sxs-lookup"><span data-stu-id="269c5-402">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="269c5-403">对 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的调用已正确均衡。</span><span class="sxs-lookup"><span data-stu-id="269c5-403">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="269c5-404">仅将特性添加到正确的位置。</span><span class="sxs-lookup"><span data-stu-id="269c5-404">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="269c5-405">若手动呈现树生成器逻辑不正确，则可能出现任意未定义的行为（包括崩溃和服务器挂起）以及安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="269c5-405">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="269c5-406">请知悉：手动呈现树生成器逻辑的复杂程度和 *危险* 程度与手动编写程序集代码或 [Microsoft 中间语言 (MSIL)](/dotnet/standard/managed-code) 指令是一样的。</span><span class="sxs-lookup"><span data-stu-id="269c5-406">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="269c5-407">其他资源</span><span class="sxs-lookup"><span data-stu-id="269c5-407">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="269c5-408">&dagger;适用于服务器端 ASP.NET Core 应用，这些应用是 Blazor 应用的 Web API 后端应用。</span><span class="sxs-lookup"><span data-stu-id="269c5-408">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

::: zone-end
