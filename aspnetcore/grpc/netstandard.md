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
ms.openlocfilehash: a6b066979dcdcdf648b8b0326bef47fe0e466266
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103422482"
---
# <a name="use-grpc-client-with-net-standard-20"></a><span data-ttu-id="a266a-103">将 gRPC 客户端与 .NET Standard 2.0 一起使用</span><span class="sxs-lookup"><span data-stu-id="a266a-103">Use gRPC client with .NET Standard 2.0</span></span>

<span data-ttu-id="a266a-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a266a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a266a-105">本文介绍如何将 .NET gRPC 客户端与支持 [.NET Standard 2.0](/dotnet/standard/net-standard) 的 .NET 实现一起使用。</span><span class="sxs-lookup"><span data-stu-id="a266a-105">This article discusses how to use the .NET gRPC client with .NET implementations that support [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span>

## <a name="net-implementations"></a><span data-ttu-id="a266a-106">.NET 实现</span><span class="sxs-lookup"><span data-stu-id="a266a-106">.NET implementations</span></span>

<span data-ttu-id="a266a-107">以下 .NET 实现（或更高版本）支持 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/)，但不完全支持 HTTP/2：</span><span class="sxs-lookup"><span data-stu-id="a266a-107">The following .NET implementations (or later) support [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) but don't have full support for HTTP/2:</span></span>

* <span data-ttu-id="a266a-108">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="a266a-108">.NET Core 2.1</span></span>
* <span data-ttu-id="a266a-109">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="a266a-109">.NET Framework 4.6.1</span></span>
* <span data-ttu-id="a266a-110">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="a266a-110">Mono 5.4</span></span>
* <span data-ttu-id="a266a-111">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="a266a-111">Xamarin.iOS 10.14</span></span>
* <span data-ttu-id="a266a-112">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="a266a-112">Xamarin.Android 8.0</span></span>
* <span data-ttu-id="a266a-113">通用 Windows 平台 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="a266a-113">Universal Windows Platform 10.0.16299</span></span>
* <span data-ttu-id="a266a-114">Unity 2018.1</span><span class="sxs-lookup"><span data-stu-id="a266a-114">Unity 2018.1</span></span>

<span data-ttu-id="a266a-115">.NET gRPC 客户端可以通过一些其他配置从这些 .NET 实现中调用服务。</span><span class="sxs-lookup"><span data-stu-id="a266a-115">The .NET gRPC client can call services from these .NET implementations with some additional configuration.</span></span>

## <a name="httphandler-configuration"></a><span data-ttu-id="a266a-116">HttpHandler 配置</span><span class="sxs-lookup"><span data-stu-id="a266a-116">HttpHandler configuration</span></span>

<span data-ttu-id="a266a-117">必须使用 `GrpcChannelOptions.HttpHandler` 配置 HTTP 提供程序。</span><span class="sxs-lookup"><span data-stu-id="a266a-117">An HTTP provider must be configured using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="a266a-118">如果未配置处理程序，则会引发错误：</span><span class="sxs-lookup"><span data-stu-id="a266a-118">If a handler isn't configured, an error is thrown:</span></span>

> <span data-ttu-id="a266a-119">`System.PlatformNotSupportedException`：gRPC 需进行额外的配置才能在不支持 gRPC over HTTP/2 的 .NET 实现上成功进行 RPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a266a-119">`System.PlatformNotSupportedException`: gRPC requires extra configuration to successfully make RPC calls on .NET implementations that don't have support for gRPC over HTTP/2.</span></span> <span data-ttu-id="a266a-120">必须使用 `GrpcChannelOptions.HttpHandler` 指定 HTTP 提供程序。</span><span class="sxs-lookup"><span data-stu-id="a266a-120">An HTTP provider must be specified using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="a266a-121">配置的 HTTP 提供程序必须支持 HTTP/2 或配置为使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="a266a-121">The configured HTTP provider must either support HTTP/2 or be configured to use gRPC-Web.</span></span>

<span data-ttu-id="a266a-122">不支持 HTTP/2 的 .NET 实现（例如 UWP、Xamarin 和 Unity）可以改为使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="a266a-122">.NET implementations that don't support HTTP/2, such as UWP, Xamarin, and Unity, can use gRPC-Web as an alternative.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

<span data-ttu-id="a266a-123">有关详细信息，请参阅[使用 .Net gRPC 客户端配置 gRPC-Web](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client)。</span><span class="sxs-lookup"><span data-stu-id="a266a-123">For more information, see [Configure gRPC-Web with the .NET gRPC client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span></span>

## <a name="net-framework"></a><span data-ttu-id="a266a-124">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="a266a-124">.NET Framework</span></span>

<span data-ttu-id="a266a-125">.NET Framework 对 gRPC over HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="a266a-125">.NET Framework has limited support for gRPC over HTTP/2.</span></span> <span data-ttu-id="a266a-126">若要在 .NET Framework 上启用 gRPC over HTTP/2，请将该通道配置为使用 <xref:System.Net.Http.WinHttpHandler>。</span><span class="sxs-lookup"><span data-stu-id="a266a-126">To enable gRPC over HTTP/2 on .NET Framework, configure the channel to use <xref:System.Net.Http.WinHttpHandler>.</span></span>

<span data-ttu-id="a266a-127">使用 `WinHttpHandler` 的要求和限制：</span><span class="sxs-lookup"><span data-stu-id="a266a-127">Requirements and restrictions to using `WinHttpHandler`:</span></span>

* <span data-ttu-id="a266a-128">Windows 10 内部版本 19622 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="a266a-128">Windows 10 Build 19622 or later.</span></span>
* <span data-ttu-id="a266a-129">引用 [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a266a-129">A reference to the [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet package.</span></span>
* <span data-ttu-id="a266a-130">仅支持一元和服务器流式处理 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a266a-130">Only unary and server streaming gRPC calls are supported.</span></span>
* <span data-ttu-id="a266a-131">仅支持通过 TLS 进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a266a-131">Only gRPC calls over TLS are supported.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> <span data-ttu-id="a266a-132">.NET Framework 支持尚处于早期阶段，需要使用预发行版软件。</span><span class="sxs-lookup"><span data-stu-id="a266a-132">.NET Framework support is in its early stages and requires using pre-release software.</span></span>
> * <span data-ttu-id="a266a-133">Windows 10 内部版本 19622 或更高版本可作为 [Windows 预览体验成员](https://insider.windows.com/)内部版本提供。</span><span class="sxs-lookup"><span data-stu-id="a266a-133">Windows 10 Build 19622 or later is available as a [Windows Insiders](https://insider.windows.com/) build.</span></span>
> * <span data-ttu-id="a266a-134">NuGet.org 当前未提供所需的 `System.Net.Http.WinHttpHandler` 版本。应使用[此 NuGet 源](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json)上提供的最新预发行版本。</span><span class="sxs-lookup"><span data-stu-id="a266a-134">The required version of `System.Net.Http.WinHttpHandler` is not currently available on NuGet.org. The latest pre-release version is available on [this NuGet feed](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) should be used.</span></span>

## <a name="grpc-c-core-library"></a><span data-ttu-id="a266a-135">gRPC C# 核心库</span><span class="sxs-lookup"><span data-stu-id="a266a-135">gRPC C# core-library</span></span>

<span data-ttu-id="a266a-136">对于 .NET Framework 和 Xamarin，还可以选择使用 [gRPC C# 核心库](https://grpc.io/docs/languages/csharp/quickstart/)进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a266a-136">An alternative option for .NET Framework and Xamarin is to use [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) to make gRPC calls.</span></span> <span data-ttu-id="a266a-137">它是第三方库，支持在 .NET Framework 和 Xamarin 上通过 HTTP/2 进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a266a-137">It's a third party library that supports making gRPC calls over HTTP/2 on .NET Framework and Xamarin.</span></span> <span data-ttu-id="a266a-138">Microsoft 不支持 gRPC C-core。</span><span class="sxs-lookup"><span data-stu-id="a266a-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a266a-139">其他资源</span><span class="sxs-lookup"><span data-stu-id="a266a-139">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/browser>
* [<span data-ttu-id="a266a-140">gRPC C# 核心库</span><span class="sxs-lookup"><span data-stu-id="a266a-140">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
