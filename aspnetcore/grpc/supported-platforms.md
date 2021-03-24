---
title: .NET 上的 gRPC 支持的平台
author: jamesnk
description: 了解 .NET 上的 gRPC 支持的平台。
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
uid: grpc/supported-platforms
ms.openlocfilehash: c2bd808d16f11077e39aada829d79e8aedf2755b
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413413"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="86207-103">.NET 上的 gRPC 支持的平台</span><span class="sxs-lookup"><span data-stu-id="86207-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="86207-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="86207-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="86207-105">本文讨论在 .NET 中使用 gRPC 的要求和支持的平台。</span><span class="sxs-lookup"><span data-stu-id="86207-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span> <span data-ttu-id="86207-106">针对以下两个主要的 gRPC 工作负载，要求有所不同：</span><span class="sxs-lookup"><span data-stu-id="86207-106">There are different requirements for the two major gRPC workloads:</span></span>

* [<span data-ttu-id="86207-107">在 ASP.NET Core 中托管 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="86207-107">Hosting gRPC services in ASP.NET Core</span></span>](#aspnet-core-grpc-server-requirements)
* [<span data-ttu-id="86207-108">从 .NET 客户端应用调用 gRPC</span><span class="sxs-lookup"><span data-stu-id="86207-108">Calling gRPC from .NET client apps</span></span>](#net-grpc-client-requirements)

## <a name="wire-formats"></a><span data-ttu-id="86207-109">连网格式</span><span class="sxs-lookup"><span data-stu-id="86207-109">Wire-formats</span></span>

<span data-ttu-id="86207-110">gRPC 充分利用 HTTP/2 中提供的高级功能。</span><span class="sxs-lookup"><span data-stu-id="86207-110">gRPC takes advantage of advanced features available in HTTP/2.</span></span> <span data-ttu-id="86207-111">在任何位置都不支持 HTTP/2，但使用 HTTP/1.1 的第二种线路格式可用于 gRPC：</span><span class="sxs-lookup"><span data-stu-id="86207-111">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="86207-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2 上的 gRPC 是 gRPC 的常见使用方式。</span><span class="sxs-lookup"><span data-stu-id="86207-112">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="86207-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web 修改 gRPC 协议，使其与 HTTP/1.1 兼容。</span><span class="sxs-lookup"><span data-stu-id="86207-113">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="86207-114">gRPC-Web 可在更多地方使用。</span><span class="sxs-lookup"><span data-stu-id="86207-114">gRPC-Web can be used in more places.</span></span> <span data-ttu-id="86207-115">在不完全支持 HTTP/2 的浏览器应用和网络中均可使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="86207-115">gRPC-Web can be used by browser apps and in networks without complete support for HTTP/2.</span></span> <span data-ttu-id="86207-116">不再支持两个高级 gRPC 功能：客户端流式处理和双向流式处理。</span><span class="sxs-lookup"><span data-stu-id="86207-116">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="86207-117">.NET 上的 gRPC 支持两种线路格式。</span><span class="sxs-lookup"><span data-stu-id="86207-117">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="86207-118">默认情况下使用 `application/grpc`。</span><span class="sxs-lookup"><span data-stu-id="86207-118">`application/grpc` is used by default.</span></span> <span data-ttu-id="86207-119">必须在客户端和服务器上配置 gRPC-Web 才能成功调用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="86207-119">gRPC-Web must be configured on the client and the server for successful gRPC-Web calls.</span></span> <span data-ttu-id="86207-120">有关设置 gRPC-Web 的信息，请参阅 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="86207-120">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="aspnet-core-grpc-server-requirements"></a><span data-ttu-id="86207-121">ASP.NET Core gRPC 服务器要求</span><span class="sxs-lookup"><span data-stu-id="86207-121">ASP.NET Core gRPC server requirements</span></span>

<span data-ttu-id="86207-122">使用 ASP.NET Core 托管 gRPC 服务需要 .NET Core 3.x 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="86207-122">Hosting gRPC services with ASP.NET Core requires .NET Core 3.x or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="86207-123">.NET 5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="86207-123">.NET 5 or later</span></span>
> * <span data-ttu-id="86207-124">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="86207-124">.NET Core 3</span></span>

<span data-ttu-id="86207-125">ASP.NET Core gRPC 服务可以托管在 .NET Core 支持的所有操作系统上。</span><span class="sxs-lookup"><span data-stu-id="86207-125">ASP.NET Core gRPC services can be hosted on all operating system that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="86207-126">Windows</span><span class="sxs-lookup"><span data-stu-id="86207-126">Windows</span></span>
> * <span data-ttu-id="86207-127">Linux</span><span class="sxs-lookup"><span data-stu-id="86207-127">Linux</span></span>
> * <span data-ttu-id="86207-128">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="86207-128">macOS&dagger;</span></span>

<span data-ttu-id="86207-129">&dagger;[macOS 不支持通过 HTTPS 托管 ASP.NET Core 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="86207-129">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="supported-aspnet-core-servers"></a><span data-ttu-id="86207-130">支持的 ASP.NET Core 服务器</span><span class="sxs-lookup"><span data-stu-id="86207-130">Supported ASP.NET Core servers</span></span>

<span data-ttu-id="86207-131">支持所有内置的 ASP.NET Core 服务器。</span><span class="sxs-lookup"><span data-stu-id="86207-131">All built-in ASP.NET Core servers are supported.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="86207-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="86207-132">Kestrel</span></span>
> * <span data-ttu-id="86207-133">TestServer</span><span class="sxs-lookup"><span data-stu-id="86207-133">TestServer</span></span>
> * <span data-ttu-id="86207-134">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="86207-134">IIS&dagger;</span></span>
> * <span data-ttu-id="86207-135">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="86207-135">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="86207-136">&dagger;IIS 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="86207-136">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="86207-137">&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="86207-137">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="86207-138">有关配置 ASP.NET Core 服务器以运行 gRPC 的信息，请参阅 <xref:grpc/aspnetcore#server-options>。</span><span class="sxs-lookup"><span data-stu-id="86207-138">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

### <a name="azure-services"></a><span data-ttu-id="86207-139">Azure 服务</span><span class="sxs-lookup"><span data-stu-id="86207-139">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="86207-140">Azure Kubernetes 服务 (AKS)</span><span class="sxs-lookup"><span data-stu-id="86207-140">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="86207-141">[Azure 应用服务](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="86207-141">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="86207-142">&dagger;Azure 应用服务不支持通过 HTTP/2 托管 gRPC。</span><span class="sxs-lookup"><span data-stu-id="86207-142">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="86207-143">gRPC-Web 是一种兼容的替代方法。</span><span class="sxs-lookup"><span data-stu-id="86207-143">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="86207-144">工作正在进行，以在 Azure 应用服务中提高 HTTP/2 对 gRPC 的支持。</span><span class="sxs-lookup"><span data-stu-id="86207-144">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="86207-145">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/9020)。</span><span class="sxs-lookup"><span data-stu-id="86207-145">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="net-grpc-client-requirements"></a><span data-ttu-id="86207-146">.NET gRPC 客户端要求</span><span class="sxs-lookup"><span data-stu-id="86207-146">.NET gRPC client requirements</span></span>

<span data-ttu-id="86207-147">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) 包支持在 .NET Core 3 以及 .NET 5 或更高版本上通过 HTTP/2 进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="86207-147">The [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) package supports gRPC calls over HTTP/2 on .NET Core 3 and .NET 5 or later.</span></span>

<span data-ttu-id="86207-148">.NET Framework 上对 gRPC over HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="86207-148">Limited support is available for gRPC over HTTP/2 on .NET Framework.</span></span> <span data-ttu-id="86207-149">其他 .NET 版本（例如 UWP、Xamarin 和 Unity）不具备所需的 HTTP/2 支持，必须改为使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="86207-149">Other .NET versions such as UWP, Xamarin and Unity don't have required HTTP/2 support, and must use gRPC-Web instead.</span></span>

<span data-ttu-id="86207-150">下表列出了 .NET 实现及其 gRPC 客户端支持：</span><span class="sxs-lookup"><span data-stu-id="86207-150">The following table lists .NET implementations and their gRPC client support:</span></span>

| <span data-ttu-id="86207-151">.NET 实现</span><span class="sxs-lookup"><span data-stu-id="86207-151">.NET implementation</span></span>                          | <span data-ttu-id="86207-152">gRPC over HTTP/2</span><span class="sxs-lookup"><span data-stu-id="86207-152">gRPC over HTTP/2</span></span>   | <span data-ttu-id="86207-153">gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="86207-153">gRPC-Web</span></span>   |
|----------------------------------------------|--------------------|------------|
| <span data-ttu-id="86207-154">.NET 5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="86207-154">.NET 5 or later</span></span>                              | <span data-ttu-id="86207-155">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-155">✔️</span></span>                | <span data-ttu-id="86207-156">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-156">✔️</span></span>         |
| <span data-ttu-id="86207-157">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="86207-157">.NET Core 3</span></span>                                  | <span data-ttu-id="86207-158">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-158">✔️</span></span>                | <span data-ttu-id="86207-159">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-159">✔️</span></span>         |
| <span data-ttu-id="86207-160">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="86207-160">.NET Core 2.1</span></span>                                | ❌                | <span data-ttu-id="86207-161">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-161">✔️</span></span>         |
| <span data-ttu-id="86207-162">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="86207-162">.NET Framework 4.6.1</span></span>                         | ⚠️&dagger;        | <span data-ttu-id="86207-163">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-163">✔️</span></span>         |
| Blazor WebAssembly                           | ❌                | <span data-ttu-id="86207-164">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-164">✔️</span></span>         |
| <span data-ttu-id="86207-165">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="86207-165">Mono 5.4</span></span>                                     | ❌                | <span data-ttu-id="86207-166">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-166">✔️</span></span>         |
| <span data-ttu-id="86207-167">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="86207-167">Xamarin.iOS 10.14</span></span>                            | ❌                | <span data-ttu-id="86207-168">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-168">✔️</span></span>         |
| <span data-ttu-id="86207-169">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="86207-169">Xamarin.Android 8.0</span></span>                          | ❌                | <span data-ttu-id="86207-170">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-170">✔️</span></span>         |
| <span data-ttu-id="86207-171">通用 Windows 平台 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="86207-171">Universal Windows Platform 10.0.16299</span></span>        | ❌                | <span data-ttu-id="86207-172">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-172">✔️</span></span>         |
| <span data-ttu-id="86207-173">Unity 2018.1</span><span class="sxs-lookup"><span data-stu-id="86207-173">Unity 2018.1</span></span>                                 | ❌                | <span data-ttu-id="86207-174">✔️</span><span class="sxs-lookup"><span data-stu-id="86207-174">✔️</span></span>         |

<span data-ttu-id="86207-175">&dagger;.NET Framework 要求配置 <xref:System.Net.Http.WinHttpHandler>，以及使用 Windows 10 内部版本 19622 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="86207-175">&dagger;.NET Framework requires <xref:System.Net.Http.WinHttpHandler> to be configured and Windows 10 Build 19622 or later.</span></span>

<span data-ttu-id="86207-176">在 .NET Framework 上使用 `Grpc.Net.Client` 或将其与 gRPC-Web 一起使用时，需进行其他配置。</span><span class="sxs-lookup"><span data-stu-id="86207-176">Using `Grpc.Net.Client` on .NET Framework or with gRPC-Web requires additional configuration.</span></span> <span data-ttu-id="86207-177">有关详细信息，请参阅 <xref:grpc/netstandard>。</span><span class="sxs-lookup"><span data-stu-id="86207-177">For more information, see <xref:grpc/netstandard>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86207-178">其他资源</span><span class="sxs-lookup"><span data-stu-id="86207-178">Additional resources</span></span>

* <xref:grpc/netstandard>
* [<span data-ttu-id="86207-179">gRPC C# 核心库</span><span class="sxs-lookup"><span data-stu-id="86207-179">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
