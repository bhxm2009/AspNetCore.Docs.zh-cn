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
ms.openlocfilehash: f7923029ffc31a1a8949b8ffd79048829e51f48a
ms.sourcegitcommit: 8f4313c762a0b7c30e5ce328b3afe146838f53d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107591674"
---
# <a name="use-grpc-client-with-net-standard-20"></a>将 gRPC 客户端与 .NET Standard 2.0 一起使用

作者：[James Newton-King](https://twitter.com/jamesnk)

本文介绍如何将 .NET gRPC 客户端与支持 [.NET Standard 2.0](/dotnet/standard/net-standard) 的 .NET 实现一起使用。

## <a name="net-implementations"></a>.NET 实现

以下 .NET 实现（或更高版本）支持 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/)，但不完全支持 HTTP/2：

* .NET Core 2.1
* .NET Framework 4.6.1
* Mono 5.4
* Xamarin.iOS 10.14
* Xamarin.Android 8.0
* 通用 Windows 平台 10.0.16299
* Unity 2018.1

.NET gRPC 客户端可以通过一些其他配置从这些 .NET 实现中调用服务。

## <a name="httphandler-configuration"></a>HttpHandler 配置

必须使用 `GrpcChannelOptions.HttpHandler` 配置 HTTP 提供程序。 如果未配置处理程序，则会引发错误：

> `System.PlatformNotSupportedException`：gRPC 需进行额外的配置才能在不支持 gRPC over HTTP/2 的 .NET 实现上成功进行 RPC 调用。 必须使用 `GrpcChannelOptions.HttpHandler` 指定 HTTP 提供程序。 配置的 HTTP 提供程序必须支持 HTTP/2 或配置为使用 gRPC-Web。

不支持 HTTP/2 的 .NET 实现（例如 UWP、Xamarin 和 Unity）可以改为使用 gRPC-Web。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

也可以使用 [gRPC 客户端工厂](xref:grpc/clientfactory)创建客户端。 使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 扩展方法配置 HTTP 提供程序。

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>(options =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(new HttpClientHandler()));
```

有关详细信息，请参阅[使用 .Net gRPC 客户端配置 gRPC-Web](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client)。

## <a name="net-framework"></a>.NET Framework

.NET Framework 对 gRPC over HTTP/2 的支持有限。 若要在 .NET Framework 上启用 gRPC over HTTP/2，请将该通道配置为使用 <xref:System.Net.Http.WinHttpHandler>。

使用 `WinHttpHandler` 的要求和限制：

* Windows 10 内部版本 19622 或更高版本。
* 引用 [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) 版本 6.0.0-preview.3.21201.4 或更高版本。
* 仅支持一元和服务器流式处理 gRPC 调用。
* 仅支持通过 TLS 进行 gRPC 调用。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> .NET Framework 支持尚处于早期阶段，需要使用预发行版软件。
> * Windows 10 内部版本 19622 或更高版本可作为 [Windows 预览体验成员](https://insider.windows.com/)内部版本提供。
> * [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) 版本 6.0.0-preview.3.21201.4 或更高版本。

## <a name="grpc-c-core-library"></a>gRPC C# 核心库

对于 .NET Framework 和 Xamarin，还可以选择使用 [gRPC C# 核心库](https://grpc.io/docs/languages/csharp/quickstart/)进行 gRPC 调用。 它是第三方库，支持在 .NET Framework 和 Xamarin 上通过 HTTP/2 进行 gRPC 调用。 Microsoft 不支持 gRPC C-core。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/client>
* <xref:grpc/browser>
* [gRPC C# 核心库](https://grpc.io/docs/languages/csharp/quickstart/)
