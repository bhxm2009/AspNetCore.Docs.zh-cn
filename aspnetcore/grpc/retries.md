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
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="e1a78-103">暂时性故障处理与 gRPC 重试</span><span class="sxs-lookup"><span data-stu-id="e1a78-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="e1a78-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e1a78-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e1a78-105">gRPC 重试是一项功能，允许 gRPC 客户端自动重试失败的调用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="e1a78-106">本文介绍如何配置重试策略，以便在 .NET 中创建可复原的容错 gRPC 应用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="e1a78-107">gRPC 重试所要求的使用 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 版本 2.36.0-pre1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e1a78-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0-pre1 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="e1a78-108">暂时性故障处理</span><span class="sxs-lookup"><span data-stu-id="e1a78-108">Transient fault handling</span></span>

<span data-ttu-id="e1a78-109">暂时性故障可能会中断 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="e1a78-110">暂时性故障包括：</span><span class="sxs-lookup"><span data-stu-id="e1a78-110">Transient faults include:</span></span>

* <span data-ttu-id="e1a78-111">暂时失去网络连接。</span><span class="sxs-lookup"><span data-stu-id="e1a78-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="e1a78-112">服务暂时不可用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="e1a78-113">由于服务器负载导致超时。</span><span class="sxs-lookup"><span data-stu-id="e1a78-113">Timeouts due to server load.</span></span>

<span data-ttu-id="e1a78-114">gRPC 调用中断时，客户端会引发 `RpcException` 并提供有关错误的详细信息。</span><span class="sxs-lookup"><span data-stu-id="e1a78-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="e1a78-115">客户端应用必须捕获异常并选择如何处理错误。</span><span class="sxs-lookup"><span data-stu-id="e1a78-115">The client app must catch the exception and choose how to handle the error.</span></span>

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

<span data-ttu-id="e1a78-116">在整个应用中复制重试逻辑是非常冗长的代码，容易出错。</span><span class="sxs-lookup"><span data-stu-id="e1a78-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="e1a78-117">幸运的是，.NET gRPC 客户端拥有自动重试的内置支持。</span><span class="sxs-lookup"><span data-stu-id="e1a78-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="e1a78-118">配置 gRPC 重试策略</span><span class="sxs-lookup"><span data-stu-id="e1a78-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="e1a78-119">重试策略在创建 gRPC 通道时配置一次：</span><span class="sxs-lookup"><span data-stu-id="e1a78-119">A retry policy is configured once when a gRPC channel is created:</span></span>

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

<span data-ttu-id="e1a78-120">上述代码：</span><span class="sxs-lookup"><span data-stu-id="e1a78-120">The preceding code:</span></span>

* <span data-ttu-id="e1a78-121">创建一个 `MethodConfig`。</span><span class="sxs-lookup"><span data-stu-id="e1a78-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="e1a78-122">重试策略可以按方法配置，而方法可以使用 `Names` 属性进行匹配。</span><span class="sxs-lookup"><span data-stu-id="e1a78-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="e1a78-123">此方法配置有 `MethodName.Default`，因此它将应用于此通道调用的所有 gRPC 方法。</span><span class="sxs-lookup"><span data-stu-id="e1a78-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="e1a78-124">配置重试策略。</span><span class="sxs-lookup"><span data-stu-id="e1a78-124">Configures a retry policy.</span></span> <span data-ttu-id="e1a78-125">此策略将指示客户端自动重试状态代码为 `Unavailable` 的失败 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="e1a78-126">通过设置 `GrpcChannelOptions.ServiceConfig`，将创建的通道配置为使用该重试策略。</span><span class="sxs-lookup"><span data-stu-id="e1a78-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="e1a78-127">随着该通道一起创建的 gRPC 客户端将自动重试失败的调用：</span><span class="sxs-lookup"><span data-stu-id="e1a78-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a><span data-ttu-id="e1a78-128">gRPC 重试选项</span><span class="sxs-lookup"><span data-stu-id="e1a78-128">gRPC retry options</span></span>

<span data-ttu-id="e1a78-129">下表描述了用于配置 gRPC 重试策略的选项：</span><span class="sxs-lookup"><span data-stu-id="e1a78-129">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="e1a78-130">选项</span><span class="sxs-lookup"><span data-stu-id="e1a78-130">Option</span></span> | <span data-ttu-id="e1a78-131">描述</span><span class="sxs-lookup"><span data-stu-id="e1a78-131">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="e1a78-132">最大调用尝试次数，包括原始尝试。</span><span class="sxs-lookup"><span data-stu-id="e1a78-132">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="e1a78-133">此值受 `GrpcChannelOptions.MaxRetryAttempts`（默认值为 5）的限制。</span><span class="sxs-lookup"><span data-stu-id="e1a78-133">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="e1a78-134">必须为该选项提供值，且值必须大于 1。</span><span class="sxs-lookup"><span data-stu-id="e1a78-134">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="e1a78-135">重试尝试之间的初始退避延迟。</span><span class="sxs-lookup"><span data-stu-id="e1a78-135">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="e1a78-136">介于 0 与当前退避之间的随机延迟确定何时进行下一次重试尝试。</span><span class="sxs-lookup"><span data-stu-id="e1a78-136">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="e1a78-137">每次尝试后，当前退避将乘以 `BackoffMultiplier`。</span><span class="sxs-lookup"><span data-stu-id="e1a78-137">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="e1a78-138">必须为该选项提供值，且值必须大于 0。</span><span class="sxs-lookup"><span data-stu-id="e1a78-138">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="e1a78-139">最大退避会限制指数退避增长的上限。</span><span class="sxs-lookup"><span data-stu-id="e1a78-139">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="e1a78-140">必须为该选项提供值，且值必须大于 0。</span><span class="sxs-lookup"><span data-stu-id="e1a78-140">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="e1a78-141">每次重试尝试后，退避将乘以该值，并将在乘数大于 1 的情况下以指数方式增加。</span><span class="sxs-lookup"><span data-stu-id="e1a78-141">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="e1a78-142">必须为该选项提供值，且值必须大于 0。</span><span class="sxs-lookup"><span data-stu-id="e1a78-142">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="e1a78-143">状态代码的集合。</span><span class="sxs-lookup"><span data-stu-id="e1a78-143">A collection of status codes.</span></span> <span data-ttu-id="e1a78-144">具有匹配状态的失败 gRPC 调用将自动重试。</span><span class="sxs-lookup"><span data-stu-id="e1a78-144">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="e1a78-145">有关状态代码的更多信息，请参阅[状态代码及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="e1a78-145">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="e1a78-146">至少需要提供一个可重试的状态代码。</span><span class="sxs-lookup"><span data-stu-id="e1a78-146">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="e1a78-147">Hedging</span><span class="sxs-lookup"><span data-stu-id="e1a78-147">Hedging</span></span>

<span data-ttu-id="e1a78-148">Hedging 是一种备选重试策略。</span><span class="sxs-lookup"><span data-stu-id="e1a78-148">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="e1a78-149">Hedging 允许在不等待响应的情况下，主动发送单个 gRPC 调用的多个副本。</span><span class="sxs-lookup"><span data-stu-id="e1a78-149">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="e1a78-150">Hedged gRPC 调用可以在服务器上执行多次，并使用第一个成功的结果。</span><span class="sxs-lookup"><span data-stu-id="e1a78-150">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="e1a78-151">重要的是，务必仅针对可安全执行多次且不会造成负面影响的方法启用 hedging。</span><span class="sxs-lookup"><span data-stu-id="e1a78-151">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="e1a78-152">与重试相比，Hedging 具有以下优缺点：</span><span class="sxs-lookup"><span data-stu-id="e1a78-152">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="e1a78-153">Hedging 的优点是，它可能会更快地返回成功的结果。</span><span class="sxs-lookup"><span data-stu-id="e1a78-153">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="e1a78-154">它允许同时进行多个 gRPC 调用，并在出现第一个成功的结果时完成。</span><span class="sxs-lookup"><span data-stu-id="e1a78-154">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="e1a78-155">Hedging 的一个缺点是它可能会造成浪费。</span><span class="sxs-lookup"><span data-stu-id="e1a78-155">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="e1a78-156">进行了多个调用并且这些调用全部成功。</span><span class="sxs-lookup"><span data-stu-id="e1a78-156">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="e1a78-157">而仅使用第一个结果，并放弃其余结果。</span><span class="sxs-lookup"><span data-stu-id="e1a78-157">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="e1a78-158">配置 gRPC hedging 策略</span><span class="sxs-lookup"><span data-stu-id="e1a78-158">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="e1a78-159">Hedging 策略的配置类似于重试策略。</span><span class="sxs-lookup"><span data-stu-id="e1a78-159">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="e1a78-160">需要注意的是，hedging 策略不能与重试策略结合使用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-160">Note that a hedging policy can't be combined with a retry policy.</span></span>

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

### <a name="grpc-hedging-options"></a><span data-ttu-id="e1a78-161">gRPC hedging 选项</span><span class="sxs-lookup"><span data-stu-id="e1a78-161">gRPC hedging options</span></span>

<span data-ttu-id="e1a78-162">下表描述了用于配置 gRPC hedging 策略的选项：</span><span class="sxs-lookup"><span data-stu-id="e1a78-162">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="e1a78-163">选项</span><span class="sxs-lookup"><span data-stu-id="e1a78-163">Option</span></span> | <span data-ttu-id="e1a78-164">描述</span><span class="sxs-lookup"><span data-stu-id="e1a78-164">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="e1a78-165">Hedging 策略将发送的调用数量上限。</span><span class="sxs-lookup"><span data-stu-id="e1a78-165">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="e1a78-166">`MaxAttempts` 表示所有尝试的总数，包括原始尝试。</span><span class="sxs-lookup"><span data-stu-id="e1a78-166">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="e1a78-167">此值受 `GrpcChannelOptions.MaxRetryAttempts`（默认值为 5）的限制。</span><span class="sxs-lookup"><span data-stu-id="e1a78-167">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="e1a78-168">必须为该选项提供值，且值必须大于 2。</span><span class="sxs-lookup"><span data-stu-id="e1a78-168">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="e1a78-169">第一次调用立即发送，但后续 hedging 调用将按该值延迟发送。</span><span class="sxs-lookup"><span data-stu-id="e1a78-169">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="e1a78-170">如果延迟设置为零或 `null`，那么所有所有 hedged 调用都将立即发送。</span><span class="sxs-lookup"><span data-stu-id="e1a78-170">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="e1a78-171">默认值为 0。</span><span class="sxs-lookup"><span data-stu-id="e1a78-171">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="e1a78-172">指示其他 hedge 调用仍可能会成功的状态代码集合。</span><span class="sxs-lookup"><span data-stu-id="e1a78-172">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="e1a78-173">如果服务器返回非致命状态代码，hedged 调用将继续。</span><span class="sxs-lookup"><span data-stu-id="e1a78-173">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="e1a78-174">否则，将取消未完成的请求，并将错误返回到应用。</span><span class="sxs-lookup"><span data-stu-id="e1a78-174">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="e1a78-175">有关状态代码的更多信息，请参阅[状态代码及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="e1a78-175">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="e1a78-176">其他资源</span><span class="sxs-lookup"><span data-stu-id="e1a78-176">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="e1a78-177">重试常规指导 - 适用于云应用程序的最佳做法</span><span class="sxs-lookup"><span data-stu-id="e1a78-177">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
