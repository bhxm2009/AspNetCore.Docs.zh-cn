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
# <a name="grpc-on-net-supported-platforms"></a>.NET 上的 gRPC 支持的平台

作者：[James Newton-King](https://twitter.com/jamesnk)

本文讨论在 .NET 中使用 gRPC 的要求和支持的平台。 针对以下两个主要的 gRPC 工作负载，要求有所不同：

* [在 ASP.NET Core 中托管 gRPC 服务](#aspnet-core-grpc-server-requirements)
* [从 .NET 客户端应用调用 gRPC](#net-grpc-client-requirements)

## <a name="wire-formats"></a>连网格式

gRPC 充分利用 HTTP/2 中提供的高级功能。 在任何位置都不支持 HTTP/2，但使用 HTTP/1.1 的第二种线路格式可用于 gRPC：

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2 上的 gRPC 是 gRPC 的常见使用方式。
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web 修改 gRPC 协议，使其与 HTTP/1.1 兼容。 gRPC-Web 可在更多地方使用。 在不完全支持 HTTP/2 的浏览器应用和网络中均可使用 gRPC-Web。 不再支持两个高级 gRPC 功能：客户端流式处理和双向流式处理。

.NET 上的 gRPC 支持两种线路格式。 默认情况下使用 `application/grpc`。 必须在客户端和服务器上配置 gRPC-Web 才能成功调用 gRPC-Web。 有关设置 gRPC-Web 的信息，请参阅 <xref:grpc/browser>。

## <a name="aspnet-core-grpc-server-requirements"></a>ASP.NET Core gRPC 服务器要求

使用 ASP.NET Core 托管 gRPC 服务需要 .NET Core 3.x 或更高版本。

> [!div class="checklist"]
>
> * .NET 5 或更高版本
> * .NET Core 3

ASP.NET Core gRPC 服务可以托管在 .NET Core 支持的所有操作系统上。

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;

&dagger;[macOS 不支持通过 HTTPS 托管 ASP.NET Core 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

### <a name="supported-aspnet-core-servers"></a>支持的 ASP.NET Core 服务器

支持所有内置的 ASP.NET Core 服务器。

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本。

&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本。

有关配置 ASP.NET Core 服务器以运行 gRPC 的信息，请参阅 <xref:grpc/aspnetcore#server-options>。

### <a name="azure-services"></a>Azure 服务

> [!div class="checklist"]
>
> * [Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure 应用服务](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure 应用服务不支持通过 HTTP/2 托管 gRPC。 gRPC-Web 是一种兼容的替代方法。

工作正在进行，以在 Azure 应用服务中提高 HTTP/2 对 gRPC 的支持。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/9020)。

## <a name="net-grpc-client-requirements"></a>.NET gRPC 客户端要求

[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) 包支持在 .NET Core 3 以及 .NET 5 或更高版本上通过 HTTP/2 进行 gRPC 调用。

.NET Framework 上对 gRPC over HTTP/2 的支持有限。 其他 .NET 版本（例如 UWP、Xamarin 和 Unity）不具备所需的 HTTP/2 支持，必须改为使用 gRPC-Web。

下表列出了 .NET 实现及其 gRPC 客户端支持：

| .NET 实现                          | gRPC over HTTP/2   | gRPC-Web   |
|----------------------------------------------|--------------------|------------|
| .NET 5 或更高版本                              | ✔️                | ✔️         |
| .NET Core 3                                  | ✔️                | ✔️         |
| .NET Core 2.1                                | ❌                | ✔️         |
| .NET Framework 4.6.1                         | ⚠️&dagger;        | ✔️         |
| Blazor WebAssembly                           | ❌                | ✔️         |
| Mono 5.4                                     | ❌                | ✔️         |
| Xamarin.iOS 10.14                            | ❌                | ✔️         |
| Xamarin.Android 8.0                          | ❌                | ✔️         |
| 通用 Windows 平台 10.0.16299        | ❌                | ✔️         |
| Unity 2018.1                                 | ❌                | ✔️         |

&dagger;.NET Framework 要求配置 <xref:System.Net.Http.WinHttpHandler>，以及使用 Windows 10 内部版本 19622 或更高版本。

在 .NET Framework 上使用 `Grpc.Net.Client` 或将其与 gRPC-Web 一起使用时，需进行其他配置。 有关详细信息，请参阅 <xref:grpc/netstandard>。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/netstandard>
* [gRPC C# 核心库](https://grpc.io/docs/languages/csharp/quickstart/)
