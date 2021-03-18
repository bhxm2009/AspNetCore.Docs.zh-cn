---
title: ASP.NET Core Blazor SignalR 指南
author: guardrex
description: 了解如何配置和管理 Blazor SignalR 连接。
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
uid: blazor/fundamentals/signalr
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 63dfd93fbc42a869211bc5cd481a8dbee6eb6c91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118910"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="c9ab7-103">ASP.NET Core Blazor SignalR 指南</span><span class="sxs-lookup"><span data-stu-id="c9ab7-103">ASP.NET Core Blazor SignalR guidance</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="c9ab7-104">本文介绍如何配置和管理 Blazor 应用中的 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-104">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="c9ab7-105">有关 ASP.NET Core SignalR 配置的常规指南，请参阅文档的 <xref:signalr/introduction> 区域中的主题。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="c9ab7-106">若要配置[添加到托管的 Blazor WebAssembly 解决方案](xref:tutorials/signalr-blazor)的 SignalR，请参阅 <xref:signalr/configuration#configure-server-options>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="c9ab7-107">用于身份验证的 SignalR 跨源协商</span><span class="sxs-lookup"><span data-stu-id="c9ab7-107">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="c9ab7-108">若要将 SignalR 的基础客户端配置为发送凭据（如 cookie 或 HTTP 身份验证标头），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="c9ab7-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 在跨源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 请求中设置 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests.</span></span>

  <span data-ttu-id="c9ab7-110">`IncludeRequestCredentialsMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-110">`IncludeRequestCredentialsMessageHandler.cs`:</span></span>

  ```csharp
  using System.Net.Http;
  using System.Threading;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Components.WebAssembly.Http;

  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="c9ab7-111">在构建中心连接的位置，将 <xref:System.Net.Http.HttpMessageHandler> 分配给 <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 选项：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-111">Where a hub connection is built, assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  HubConnectionBuilder hubConnecton;

  ...

  hubConnecton = new HubConnectionBuilder()
      .WithUrl(new Uri(NavigationManager.ToAbsoluteUri("/chathub")), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

  <span data-ttu-id="c9ab7-112">上面的示例将中心连接 URL 配置为绝对 URI 地址 `/chathub`，这是 [SignalR 与 Blazor 教程](xref:tutorials/signalr-blazor)中用于 `Index` 组件 (`Pages/Index.razor`) 的 URL。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-112">The preceding example configures the hub connection URL to the absolute URI address at `/chathub`, which is the URL used in the [SignalR with Blazor tutorial](xref:tutorials/signalr-blazor) in the `Index` component (`Pages/Index.razor`).</span></span> <span data-ttu-id="c9ab7-113">该 URI 也可以通过字符串来设置，例如 `https://signalr.example.com`，或者通过[配置](xref:blazor/fundamentals/configuration)进行设置。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-113">The URI can also be set via a string, for example `https://signalr.example.com`, or via [configuration](xref:blazor/fundamentals/configuration).</span></span>

<span data-ttu-id="c9ab7-114">有关详细信息，请参阅 <xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-114">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a><span data-ttu-id="c9ab7-115">呈现模式</span><span class="sxs-lookup"><span data-stu-id="c9ab7-115">Render mode</span></span>

<span data-ttu-id="c9ab7-116">如果使用 SignalR 的 Blazor WebAssembly 应用配置为在服务器上预呈现，则预呈现会在客户端与服务器建立连接之前发生。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-116">If a Blazor WebAssembly app that uses SignalR is configured to prerender on the server, prerendering occurs before the client connection to the server is established.</span></span> <span data-ttu-id="c9ab7-117">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-117">For more information, see the following articles:</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c9ab7-118">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9ab7-118">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="c9ab7-119">本文介绍如何配置和管理 Blazor 应用中的 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-119">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="c9ab7-120">有关 ASP.NET Core SignalR 配置的常规指南，请参阅文档的 <xref:signalr/introduction> 区域中的主题。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-120">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="c9ab7-121">若要配置[添加到托管的 Blazor WebAssembly 解决方案](xref:tutorials/signalr-blazor)的 SignalR，请参阅 <xref:signalr/configuration#configure-server-options>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-121">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="c9ab7-122">线路处理程序选项</span><span class="sxs-lookup"><span data-stu-id="c9ab7-122">Circuit handler options</span></span>

<span data-ttu-id="c9ab7-123">使用下表所示的 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 配置 Blazor Server 线路。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-123">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="c9ab7-124">选项</span><span class="sxs-lookup"><span data-stu-id="c9ab7-124">Option</span></span> | <span data-ttu-id="c9ab7-125">默认</span><span class="sxs-lookup"><span data-stu-id="c9ab7-125">Default</span></span> | <span data-ttu-id="c9ab7-126">描述</span><span class="sxs-lookup"><span data-stu-id="c9ab7-126">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="c9ab7-127">当线路上发生未经处理的异常时，或者通过 JS 互操作的 .NET 方法调用导致异常时，便向 JavaScript 发送详细的异常消息。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-127">Send detailed exception messages to JavaScript when an unhandled exception occurs on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="c9ab7-128">100</span><span class="sxs-lookup"><span data-stu-id="c9ab7-128">100</span></span> | <span data-ttu-id="c9ab7-129">服务器在内存中一次保留的断开连接的线路数上限。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-129">Maximum number of disconnected circuits that the server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="c9ab7-130">3 分钟</span><span class="sxs-lookup"><span data-stu-id="c9ab7-130">3 minutes</span></span> | <span data-ttu-id="c9ab7-131">断开连接的线路被移除前在内存中保留的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-131">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="c9ab7-132">1 分钟</span><span class="sxs-lookup"><span data-stu-id="c9ab7-132">1 minute</span></span> | <span data-ttu-id="c9ab7-133">服务器在使异步 JavaScript 函数调用超时之前等待的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-133">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="c9ab7-134">10</span><span class="sxs-lookup"><span data-stu-id="c9ab7-134">10</span></span> | <span data-ttu-id="c9ab7-135">在给定时间内，服务器在内存中对每条线路保留用以支持重新连接可靠的未确认的呈现批处理的最大数量。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-135">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="c9ab7-136">达到限制后，服务器会停止生成新的呈现批处理，直到客户端确认了一个或多个批处理。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-136">After reaching the limit, the server stops producing new render batches until one or more batches are acknowledged by the client.</span></span> |

<span data-ttu-id="c9ab7-137">使用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 的选项委托配置 `Startup.ConfigureServices` 中的选项。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-137">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="c9ab7-138">下面的示例将分配上表中显示的默认选项值。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-138">The following example assigns the default option values shown in the preceding table.</span></span> <span data-ttu-id="c9ab7-139">确认 `Startup.cs` 使用 <xref:System> 命名空间 (`using System;`)。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-139">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="c9ab7-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-140">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="c9ab7-141">若要配置 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>，请结合使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> 和 <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-141">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="c9ab7-142">有关选项说明，请参阅 <xref:signalr/configuration#configure-server-options>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-142">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="c9ab7-143">以下示例分配默认选项值。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-143">The following example assigns the default option values.</span></span> <span data-ttu-id="c9ab7-144">确认 `Startup.cs` 使用 <xref:System> 命名空间 (`using System;`)。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-144">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="c9ab7-145">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-145">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c9ab7-146">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="c9ab7-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c9ab7-147">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c9ab7-148">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c9ab7-149">若要自定义 UI，请在 `_Host.cshtml` Razor 页面的 `<body>` 中使用 `components-reconnect-modal` 的 `id` 定义一个元素。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page.</span></span>

<span data-ttu-id="c9ab7-150">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-150">`Pages/_Host.cshtml`:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c9ab7-151">将以下 CSS 样式添加到站点的样式表中。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-151">Add the following CSS styles to the site's stylesheet.</span></span>

<span data-ttu-id="c9ab7-152">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-152">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="c9ab7-153">下表介绍了 Blazor 框架应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-153">The following table describes the CSS classes applied to the `components-reconnect-modal` element by the Blazor framework.</span></span>

| <span data-ttu-id="c9ab7-154">CSS 类</span><span class="sxs-lookup"><span data-stu-id="c9ab7-154">CSS class</span></span>                       | <span data-ttu-id="c9ab7-155">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="c9ab7-155">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="c9ab7-156">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-156">A lost connection.</span></span> <span data-ttu-id="c9ab7-157">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-157">The client is attempting to reconnect.</span></span> <span data-ttu-id="c9ab7-158">显示模式。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-158">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="c9ab7-159">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-159">An active connection is re-established to the server.</span></span> <span data-ttu-id="c9ab7-160">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-160">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="c9ab7-161">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-161">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c9ab7-162">若要尝试重新连接，请在 JavaScript 中调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-162">To attempt reconnection, call `window.Blazor.reconnect()` in JavaScript.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="c9ab7-163">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-163">Reconnection rejected.</span></span> <span data-ttu-id="c9ab7-164">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-164">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c9ab7-165">若要重新加载应用，请在 JavaScript 中调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-165">To reload the app, call `location.reload()` in JavaScript.</span></span> <span data-ttu-id="c9ab7-166">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-166">This connection state may result when:</span></span><ul><li><span data-ttu-id="c9ab7-167">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-167">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c9ab7-168">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-168">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c9ab7-169">用户组件的实例已被处置。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-169">Instances of the user's components are disposed.</span></span></li><li><span data-ttu-id="c9ab7-170">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-170">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="c9ab7-171">呈现模式</span><span class="sxs-lookup"><span data-stu-id="c9ab7-171">Render mode</span></span>

<span data-ttu-id="c9ab7-172">默认情况下，Blazor Server 应用会在客户端与服务器建立连接之前在服务器上预呈现用户界面。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-172">By default, Blazor Server apps prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c9ab7-173">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-173">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="c9ab7-174">初始化 Blazor 回路</span><span class="sxs-lookup"><span data-stu-id="c9ab7-174">Initialize the Blazor circuit</span></span>

<span data-ttu-id="c9ab7-175">在 `Pages/_Host.cshtml` 文件中配置 Blazor Server 应用 [SignalR 回路 ](xref:blazor/hosting-models#circuits) 的手动启动：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-175">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="c9ab7-176">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-176">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c9ab7-177">将调用 `Blazor.start` 的脚本放置在 `blazor.server.js` 脚本标记之后并放在结束的 `</body>` 标记内。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-177">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="c9ab7-178">禁用 `autostart` 时，应用中不依赖该回路的任何方面都能正常工作。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-178">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="c9ab7-179">例如，客户端路由正常运行。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-179">For example, client-side routing is operational.</span></span> <span data-ttu-id="c9ab7-180">但是，在调用 `Blazor.start` 之前，依赖于该回路的任何方面不会正常运行。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-180">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="c9ab7-181">如果没有已建立的回路，应用行为是不可预测的。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-181">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="c9ab7-182">例如，在回路断开连接时，组件方法无法执行。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-182">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="c9ab7-183">文档准备就绪时初始化 Blazor</span><span class="sxs-lookup"><span data-stu-id="c9ab7-183">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="c9ab7-184">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-184">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="c9ab7-185">链接到由手动启动生成的 `Promise`</span><span class="sxs-lookup"><span data-stu-id="c9ab7-185">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="c9ab7-186">若要执行其他任务（如 JS 互操作初始化），请使用 [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) 链接到 [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)（由手动 Blazor 应用启动生成）。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-186">To perform additional tasks, such as JS interop initialization, use [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) to chain to the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) that results from a manual Blazor app start.</span></span>

<span data-ttu-id="c9ab7-187">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-187">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-signalr-client-logging"></a><span data-ttu-id="c9ab7-188">配置 SignalR 客户端日志</span><span class="sxs-lookup"><span data-stu-id="c9ab7-188">Configure SignalR client logging</span></span>

<span data-ttu-id="c9ab7-189">在客户端生成器上，传入 `configureSignalR` 配置对象，该对象使用日志级别调用 `configureLogging`。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-189">On the client builder, pass in the `configureSignalR` configuration object that calls `configureLogging` with the log level.</span></span>

<span data-ttu-id="c9ab7-190">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-190">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="c9ab7-191">在前面的示例中，`information` 相当于日志级别 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-191">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="c9ab7-192">修改重新连接处理程序</span><span class="sxs-lookup"><span data-stu-id="c9ab7-192">Modify the reconnection handler</span></span>

<span data-ttu-id="c9ab7-193">可以针对自定义行为修改重新连接处理程序的线路连接事件，如：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-193">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="c9ab7-194">在连接断开时通知用户。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-194">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="c9ab7-195">在线路连接时（通过客户端）执行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-195">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="c9ab7-196">若要修改连接事件，请为以下连接更改注册回调：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-196">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="c9ab7-197">使用 `onConnectionDown` 删除的连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-197">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="c9ab7-198">已建立/重新建立的连接使用 `onConnectionUp`。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-198">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="c9ab7-199">**必须同时指定 `onConnectionDown` 和 `onConnectionUp`。**</span><span class="sxs-lookup"><span data-stu-id="c9ab7-199">**Both `onConnectionDown` and `onConnectionUp` must be specified.**</span></span>

<span data-ttu-id="c9ab7-200">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-200">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="c9ab7-201">调整重新连接重试计数和间隔</span><span class="sxs-lookup"><span data-stu-id="c9ab7-201">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="c9ab7-202">若要调整重新连接重试次数和间隔，请设置重试次数 (`maxRetries`) 和允许每次重试运行的毫秒数 (`retryIntervalMilliseconds`)。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-202">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

<span data-ttu-id="c9ab7-203">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-203">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="c9ab7-204">隐藏或替换重新连接显示</span><span class="sxs-lookup"><span data-stu-id="c9ab7-204">Hide or replace the reconnection display</span></span>

<span data-ttu-id="c9ab7-205">若要隐藏重新连接显示，请将重新连接处理程序的 `_reconnectionDisplay` 设置为空对象（`{}` 或 `new Object()`）。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-205">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

<span data-ttu-id="c9ab7-206">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-206">`Pages/_Host.cshtml`:</span></span>

```cshtml
<body>
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="c9ab7-207">若要替换重新连接显示，请将前面示例中的 `_reconnectionDisplay` 设置为要显示的元素：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-207">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="c9ab7-208">占位符 `{ELEMENT ID}` 是要显示的 HTML 元素的 ID。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-208">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c9ab7-209">通过在站点的 CSS 中为模式元素设置 `transition-delay` 属性，自定义重新连接显示出现之前的延迟。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-209">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the site's CSS for the modal element.</span></span> <span data-ttu-id="c9ab7-210">以下示例将转换延迟从 500 毫秒（默认值）设置为 1000 毫秒（1 秒）。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-210">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second).</span></span>

<span data-ttu-id="c9ab7-211">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="c9ab7-211">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="c9ab7-212">从客户端断开 Blazor 线路连接</span><span class="sxs-lookup"><span data-stu-id="c9ab7-212">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="c9ab7-213">默认情况下，触发 [`unload` 页面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)时，Blazor 线路会断开连接。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-213">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="c9ab7-214">若要断开客户端上其他方案的线路连接，请在相应的事件处理程序中调用 `Blazor.disconnect`。</span><span class="sxs-lookup"><span data-stu-id="c9ab7-214">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="c9ab7-215">在下面的示例中，当页面隐藏（[`pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)）时，线路会断开连接：</span><span class="sxs-lookup"><span data-stu-id="c9ab7-215">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c9ab7-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9ab7-216">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="c9ab7-217">Blazor Server 重新连接事件和组件生命周期事件</span><span class="sxs-lookup"><span data-stu-id="c9ab7-217">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
