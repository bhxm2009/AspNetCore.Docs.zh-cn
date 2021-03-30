---
title: 将 gRPC 客户端与 .NET Standard 2.0 一起使用
author: jamesnk
description: 了解如何在支持 .NET Standard 2.0 的应用和库中使用 .NET gRPC 客户端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
uid: grpc/netstandard
ms.openlocfilehash: b3df1d3b5565dc5c03988c29d2befbe1ea164f54
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711473"
---
# <a name="use-grpc-client-with-net-standard-20"></a><span data-ttu-id="943af-103">将 gRPC 客户端与 .NET Standard 2.0 一起使用</span><span class="sxs-lookup"><span data-stu-id="943af-103">Use gRPC client with .NET Standard 2.0</span></span>

<span data-ttu-id="943af-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="943af-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="943af-105">本文介绍如何将 .NET gRPC 客户端与支持 [.NET Standard 2.0](/dotnet/standard/net-standard) 的 .NET 实现一起使用。</span><span class="sxs-lookup"><span data-stu-id="943af-105">This article discusses how to use the .NET gRPC client with .NET implementations that support [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span>

## <a name="net-implementations"></a><span data-ttu-id="943af-106">.NET 实现</span><span class="sxs-lookup"><span data-stu-id="943af-106">.NET implementations</span></span>

<span data-ttu-id="943af-107">以下 .NET 实现（或更高版本）支持 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/)，但不完全支持 HTTP/2：</span><span class="sxs-lookup"><span data-stu-id="943af-107">The following .NET implementations (or later) support [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) but don't have full support for HTTP/2:</span></span>

* <span data-ttu-id="943af-108">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="943af-108">.NET Core 2.1</span></span>
* <span data-ttu-id="943af-109">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="943af-109">.NET Framework 4.6.1</span></span>
* <span data-ttu-id="943af-110">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="943af-110">Mono 5.4</span></span>
* <span data-ttu-id="943af-111">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="943af-111">Xamarin.iOS 10.14</span></span>
* <span data-ttu-id="943af-112">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="943af-112">Xamarin.Android 8.0</span></span>
* <span data-ttu-id="943af-113">通用 Windows 平台 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="943af-113">Universal Windows Platform 10.0.16299</span></span>
* <span data-ttu-id="943af-114">Unity 2018.1</span><span class="sxs-lookup"><span data-stu-id="943af-114">Unity 2018.1</span></span>

<span data-ttu-id="943af-115">.NET gRPC 客户端可以通过一些其他配置从这些 .NET 实现中调用服务。</span><span class="sxs-lookup"><span data-stu-id="943af-115">The .NET gRPC client can call services from these .NET implementations with some additional configuration.</span></span>

## <a name="httphandler-configuration"></a><span data-ttu-id="943af-116">HttpHandler 配置</span><span class="sxs-lookup"><span data-stu-id="943af-116">HttpHandler configuration</span></span>

<span data-ttu-id="943af-117">必须使用 `GrpcChannelOptions.HttpHandler` 配置 HTTP 提供程序。</span><span class="sxs-lookup"><span data-stu-id="943af-117">An HTTP provider must be configured using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="943af-118">如果未配置处理程序，则会引发错误：</span><span class="sxs-lookup"><span data-stu-id="943af-118">If a handler isn't configured, an error is thrown:</span></span>

> <span data-ttu-id="943af-119">`System.PlatformNotSupportedException`：gRPC 需进行额外的配置才能在不支持 gRPC over HTTP/2 的 .NET 实现上成功进行 RPC 调用。</span><span class="sxs-lookup"><span data-stu-id="943af-119">`System.PlatformNotSupportedException`: gRPC requires extra configuration to successfully make RPC calls on .NET implementations that don't have support for gRPC over HTTP/2.</span></span> <span data-ttu-id="943af-120">必须使用 `GrpcChannelOptions.HttpHandler` 指定 HTTP 提供程序。</span><span class="sxs-lookup"><span data-stu-id="943af-120">An HTTP provider must be specified using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="943af-121">配置的 HTTP 提供程序必须支持 HTTP/2 或配置为使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="943af-121">The configured HTTP provider must either support HTTP/2 or be configured to use gRPC-Web.</span></span>

<span data-ttu-id="943af-122">不支持 HTTP/2 的 .NET 实现（例如 UWP、Xamarin 和 Unity）可以改为使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="943af-122">.NET implementations that don't support HTTP/2, such as UWP, Xamarin, and Unity, can use gRPC-Web as an alternative.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

<span data-ttu-id="943af-123">也可以使用 [gRPC 客户端工厂](xref:grpc/clientfactory)创建客户端。</span><span class="sxs-lookup"><span data-stu-id="943af-123">Clients can also be created using the [gRPC client factory](xref:grpc/clientfactory).</span></span> <span data-ttu-id="943af-124">使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 扩展方法配置 HTTP 提供程序。</span><span class="sxs-lookup"><span data-stu-id="943af-124">An HTTP provider is configured using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>(options =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(new HttpClientHandler()));
```

<span data-ttu-id="943af-125">有关详细信息，请参阅[使用 .Net gRPC 客户端配置 gRPC-Web](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client)。</span><span class="sxs-lookup"><span data-stu-id="943af-125">For more information, see [Configure gRPC-Web with the .NET gRPC client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span></span>

## <a name="net-framework"></a><span data-ttu-id="943af-126">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="943af-126">.NET Framework</span></span>

<span data-ttu-id="943af-127">.NET Framework 对 gRPC over HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="943af-127">.NET Framework has limited support for gRPC over HTTP/2.</span></span> <span data-ttu-id="943af-128">若要在 .NET Framework 上启用 gRPC over HTTP/2，请将该通道配置为使用 <xref:System.Net.Http.WinHttpHandler>。</span><span class="sxs-lookup"><span data-stu-id="943af-128">To enable gRPC over HTTP/2 on .NET Framework, configure the channel to use <xref:System.Net.Http.WinHttpHandler>.</span></span>

<span data-ttu-id="943af-129">使用 `WinHttpHandler` 的要求和限制：</span><span class="sxs-lookup"><span data-stu-id="943af-129">Requirements and restrictions to using `WinHttpHandler`:</span></span>

* <span data-ttu-id="943af-130">Windows 10 内部版本 19622 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="943af-130">Windows 10 Build 19622 or later.</span></span>
* <span data-ttu-id="943af-131">引用 [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="943af-131">A reference to the [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet package.</span></span>
* <span data-ttu-id="943af-132">仅支持一元和服务器流式处理 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="943af-132">Only unary and server streaming gRPC calls are supported.</span></span>
* <span data-ttu-id="943af-133">仅支持通过 TLS 进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="943af-133">Only gRPC calls over TLS are supported.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> <span data-ttu-id="943af-134">.NET Framework 支持尚处于早期阶段，需要使用预发行版软件。</span><span class="sxs-lookup"><span data-stu-id="943af-134">.NET Framework support is in its early stages and requires using pre-release software.</span></span>
> * <span data-ttu-id="943af-135">Windows 10 内部版本 19622 或更高版本可作为 [Windows 预览体验成员](https://insider.windows.com/)内部版本提供。</span><span class="sxs-lookup"><span data-stu-id="943af-135">Windows 10 Build 19622 or later is available as a [Windows Insiders](https://insider.windows.com/) build.</span></span>
> * <span data-ttu-id="943af-136">NuGet.org 当前未提供所需的 `System.Net.Http.WinHttpHandler` 版本。应使用[此 NuGet 源](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json)上提供的最新预发行版本。</span><span class="sxs-lookup"><span data-stu-id="943af-136">The required version of `System.Net.Http.WinHttpHandler` is not currently available on NuGet.org. The latest pre-release version is available on [this NuGet feed](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) should be used.</span></span>

## <a name="grpc-c-core-library"></a><span data-ttu-id="943af-137">gRPC C# 核心库</span><span class="sxs-lookup"><span data-stu-id="943af-137">gRPC C# core-library</span></span>

<span data-ttu-id="943af-138">对于 .NET Framework 和 Xamarin，还可以选择使用 [gRPC C# 核心库](https://grpc.io/docs/languages/csharp/quickstart/)进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="943af-138">An alternative option for .NET Framework and Xamarin is to use [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) to make gRPC calls.</span></span> <span data-ttu-id="943af-139">它是第三方库，支持在 .NET Framework 和 Xamarin 上通过 HTTP/2 进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="943af-139">It's a third party library that supports making gRPC calls over HTTP/2 on .NET Framework and Xamarin.</span></span> <span data-ttu-id="943af-140">Microsoft 不支持 gRPC C-core。</span><span class="sxs-lookup"><span data-stu-id="943af-140">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="943af-141">其他资源</span><span class="sxs-lookup"><span data-stu-id="943af-141">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/browser>
* [<span data-ttu-id="943af-142">gRPC C# 核心库</span><span class="sxs-lookup"><span data-stu-id="943af-142">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
