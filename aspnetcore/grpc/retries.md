---
title: 暂时性故障处理与 gRPC 重试
author: jamesnk
description: 了解如何在 .NET 中利用重试进行可复原的容错 gRPC 调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
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
uid: grpc/retries
ms.openlocfilehash: 613386d1fedd8b1b04081e3240b8a3aaf7b37012
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589785"
---
# <a name="transient-fault-handling-with-grpc-retries"></a>暂时性故障处理与 gRPC 重试

作者：[James Newton-King](https://twitter.com/jamesnk)

gRPC 重试是一项功能，允许 gRPC 客户端自动重试失败的调用。 本文介绍如何配置重试策略，以便在 .NET 中创建可复原的容错 gRPC 应用。

gRPC 重试所要求的使用 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 版本 2.36.0-pre1 或更高版本。

## <a name="transient-fault-handling"></a>暂时性故障处理

暂时性故障可能会中断 gRPC 调用。 暂时性故障包括：

* 暂时失去网络连接。
* 服务暂时不可用。
* 由于服务器负载导致超时。

gRPC 调用中断时，客户端会引发 `RpcException` 并提供有关错误的详细信息。 客户端应用必须捕获异常并选择如何处理错误。

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

在整个应用中复制重试逻辑是非常冗长的代码，容易出错。 幸运的是，.NET gRPC 客户端拥有自动重试的内置支持。

## <a name="configure-a-grpc-retry-policy"></a>配置 gRPC 重试策略

重试策略在创建 gRPC 通道时配置一次：

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

上述代码：

* 创建一个 `MethodConfig`。 重试策略可以按方法配置，而方法可以使用 `Names` 属性进行匹配。 此方法配置有 `MethodName.Default`，因此它将应用于此通道调用的所有 gRPC 方法。
* 配置重试策略。 此策略将指示客户端自动重试状态代码为 `Unavailable` 的失败 gRPC 调用。
* 通过设置 `GrpcChannelOptions.ServiceConfig`，将创建的通道配置为使用该重试策略。

随着该通道一起创建的 gRPC 客户端将自动重试失败的调用：

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a>gRPC 重试选项

下表描述了用于配置 gRPC 重试策略的选项：

| 选项 | 描述 |
| ------ | ----------- |
| `MaxAttempts` | 最大调用尝试次数，包括原始尝试。 此值受 `GrpcChannelOptions.MaxRetryAttempts`（默认值为 5）的限制。 必须为该选项提供值，且值必须大于 1。 |
| `InitialBackoff` | 重试尝试之间的初始退避延迟。 介于 0 与当前退避之间的随机延迟确定何时进行下一次重试尝试。 每次尝试后，当前退避将乘以 `BackoffMultiplier`。 必须为该选项提供值，且值必须大于 0。 |
| `MaxBackoff` | 最大退避会限制指数退避增长的上限。 必须为该选项提供值，且值必须大于 0。 |
| `BackoffMultiplier` | 每次重试尝试后，退避将乘以该值，并将在乘数大于 1 的情况下以指数方式增加。 必须为该选项提供值，且值必须大于 0。 |
| `RetryableStatusCodes` | 状态代码的集合。 具有匹配状态的失败 gRPC 调用将自动重试。 有关状态代码的更多信息，请参阅[状态代码及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。 至少需要提供一个可重试的状态代码。 |

## <a name="hedging"></a>Hedging

Hedging 是一种备选重试策略。 Hedging 允许在不等待响应的情况下，主动发送单个 gRPC 调用的多个副本。 Hedged gRPC 调用可以在服务器上执行多次，并使用第一个成功的结果。 重要的是，务必仅针对可安全执行多次且不会造成负面影响的方法启用 hedging。

与重试相比，Hedging 具有以下优缺点： 

* Hedging 的优点是，它可能会更快地返回成功的结果。 它允许同时进行多个 gRPC 调用，并在出现第一个成功的结果时完成。 
* Hedging 的一个缺点是它可能会造成浪费。 进行了多个调用并且这些调用全部成功。 而仅使用第一个结果，并放弃其余结果。

## <a name="configure-a-grpc-hedging-policy"></a>配置 gRPC hedging 策略

Hedging 策略的配置类似于重试策略。 需要注意的是，hedging 策略不能与重试策略结合使用。

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a>gRPC hedging 选项

下表描述了用于配置 gRPC hedging 策略的选项：

| 选项 | 描述 |
| ------ | ----------- |
| `MaxAttempts` | Hedging 策略将发送的调用数量上限。 `MaxAttempts` 表示所有尝试的总数，包括原始尝试。 此值受 `GrpcChannelOptions.MaxRetryAttempts`（默认值为 5）的限制。 必须为该选项提供值，且值必须大于 2。 |
| `HedgingDelay` | 第一次调用立即发送，但后续 hedging 调用将按该值延迟发送。 如果延迟设置为零或 `null`，那么所有所有 hedged 调用都将立即发送。 默认值为 0。 |
| `NonFatalStatusCodes` | 指示其他 hedge 调用仍可能会成功的状态代码集合。 如果服务器返回非致命状态代码，hedged 调用将继续。 否则，将取消未完成的请求，并将错误返回到应用。 有关状态代码的更多信息，请参阅[状态代码及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。 |

## <a name="additional-resources"></a>其他资源

* <xref:grpc/client>
* [重试常规指导 - 适用于云应用程序的最佳做法](/azure/architecture/best-practices/transient-faults)
