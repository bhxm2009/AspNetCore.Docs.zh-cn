---
title: .NET 中的 gRPC 客户端工厂集成
author: jamesnk
description: 了解如何使用客户端工厂创建 gRPC 客户端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/19/2021
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
uid: grpc/clientfactory
ms.openlocfilehash: ea5181bd44a5deafdc6634b31b9efeda2884b58c
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711498"
---
# <a name="grpc-client-factory-integration-in-net"></a><span data-ttu-id="241e0-103">.NET 中的 gRPC 客户端工厂集成</span><span class="sxs-lookup"><span data-stu-id="241e0-103">gRPC client factory integration in .NET</span></span>

<span data-ttu-id="241e0-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="241e0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="241e0-105">gRPC 与 `HttpClientFactory` 的集成提供了一种创建 gRPC 客户端的集中方式。</span><span class="sxs-lookup"><span data-stu-id="241e0-105">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="241e0-106">它可用作[配置独立 gRPC 客户端实例](xref:grpc/client)的替代方法。</span><span class="sxs-lookup"><span data-stu-id="241e0-106">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="241e0-107">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 包中提供了工厂集成。</span><span class="sxs-lookup"><span data-stu-id="241e0-107">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="241e0-108">工厂具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="241e0-108">The factory offers the following benefits:</span></span>

* <span data-ttu-id="241e0-109">提供了用于配置逻辑 gRPC 客户端实例的中心位置</span><span class="sxs-lookup"><span data-stu-id="241e0-109">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="241e0-110">可管理基础 `HttpClientMessageHandler` 的生存期</span><span class="sxs-lookup"><span data-stu-id="241e0-110">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="241e0-111">在 ASP.NET Core gRPC 服务中自动传播截止时间和取消</span><span class="sxs-lookup"><span data-stu-id="241e0-111">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="241e0-112">注册 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="241e0-112">Register gRPC clients</span></span>

<span data-ttu-id="241e0-113">若要注册 gRPC 客户端，可在 `Startup.ConfigureServices` 中使用通用的 `AddGrpcClient` 扩展方法，并指定 gRPC 类型化客户端类和服务地址：</span><span class="sxs-lookup"><span data-stu-id="241e0-113">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="241e0-114">gRPC 客户端类型通过依赖项注入 (DI) 注册为暂时性。</span><span class="sxs-lookup"><span data-stu-id="241e0-114">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="241e0-115">现在可以在由 DI 创建的类型中直接注入和使用客户端。</span><span class="sxs-lookup"><span data-stu-id="241e0-115">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="241e0-116">ASP.NET Core MVC 控制器、SignalR 中心和 gRPC 服务是可以自动注入 gRPC 客户端的位置：</span><span class="sxs-lookup"><span data-stu-id="241e0-116">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httphandler"></a><span data-ttu-id="241e0-117">配置 HttpHandler</span><span class="sxs-lookup"><span data-stu-id="241e0-117">Configure HttpHandler</span></span>

<span data-ttu-id="241e0-118">`HttpClientFactory` 创建 gRPC 客户端使用的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="241e0-118">`HttpClientFactory` creates the `HttpMessageHandler` used by the gRPC client.</span></span> <span data-ttu-id="241e0-119">标准 `HttpClientFactory` 方法可用于添加传出请求中间件或配置 `HttpClient` 的基础 `HttpClientHandler`：</span><span class="sxs-lookup"><span data-stu-id="241e0-119">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="241e0-120">有关详细信息，请参阅[使用 IHttpClientFactory 发出 HTTP 请求](xref:fundamentals/http-requests)。</span><span class="sxs-lookup"><span data-stu-id="241e0-120">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="241e0-121">配置通道和侦听器</span><span class="sxs-lookup"><span data-stu-id="241e0-121">Configure Channel and Interceptors</span></span>

<span data-ttu-id="241e0-122">特定于 gRPC 的方法可用于：</span><span class="sxs-lookup"><span data-stu-id="241e0-122">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="241e0-123">配置 gRPC 客户端的基础通道。</span><span class="sxs-lookup"><span data-stu-id="241e0-123">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="241e0-124">添加客户端在进行 gRPC 调用时将使用的 `Interceptor` 实例。</span><span class="sxs-lookup"><span data-stu-id="241e0-124">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="241e0-125">截止时间和取消传播</span><span class="sxs-lookup"><span data-stu-id="241e0-125">Deadline and cancellation propagation</span></span>

<span data-ttu-id="241e0-126">可以使用 `EnableCallContextPropagation()` 对 gRPC 服务中工厂所创建的 gRPC 客户端进行配置，以自动将截止时间和取消令牌传播到子调用。</span><span class="sxs-lookup"><span data-stu-id="241e0-126">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="241e0-127">[Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 包中提供了 `EnableCallContextPropagation()` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="241e0-127">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="241e0-128">调用上下文传播的工作方式是：从当前 gRPC 请求上下文中读取截止时间和取消令牌，并自动将其传播到 gRPC 客户端所发出的传出调用。</span><span class="sxs-lookup"><span data-stu-id="241e0-128">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="241e0-129">调用上下文传播是确保复杂的嵌套 gRPC 场景始终传播截止时间和取消的一种极佳方式。</span><span class="sxs-lookup"><span data-stu-id="241e0-129">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="241e0-130">默认情况下，如果客户端在 gRPC 调用的上下文之外使用，`EnableCallContextPropagation` 将引发错误。</span><span class="sxs-lookup"><span data-stu-id="241e0-130">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="241e0-131">此错误旨在提醒你没有要传播的调用上下文。</span><span class="sxs-lookup"><span data-stu-id="241e0-131">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="241e0-132">如果要在调用上下文之外使用客户端，请使用 `SuppressContextNotFoundErrors` 在配置客户端时禁止显示该错误：</span><span class="sxs-lookup"><span data-stu-id="241e0-132">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="241e0-133">有关截止时间和 RPC 取消的详细信息，请参阅 <xref:grpc/deadlines-cancellation>。</span><span class="sxs-lookup"><span data-stu-id="241e0-133">For more information about deadlines and RPC cancellation, see <xref:grpc/deadlines-cancellation>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="241e0-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="241e0-134">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/deadlines-cancellation>
* <xref:fundamentals/http-requests>
