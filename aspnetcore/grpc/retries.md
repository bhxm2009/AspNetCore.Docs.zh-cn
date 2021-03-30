---
title: 暂时性故障处理与 gRPC 重试
author: jamesnk
description: 了解如何在 .NET 中利用重试进行可复原的容错 gRPC 调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/18/2021
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
ms.openlocfilehash: 4fda4968102740af8d1a7f37dcc588abd24ab70e
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105050992"
---
# <a name="transient-fault-handling-with-grpc-retries"></a><span data-ttu-id="c222c-103">暂时性故障处理与 gRPC 重试</span><span class="sxs-lookup"><span data-stu-id="c222c-103">Transient fault handling with gRPC retries</span></span>

<span data-ttu-id="c222c-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c222c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c222c-105">gRPC 重试是一项功能，允许 gRPC 客户端自动重试失败的调用。</span><span class="sxs-lookup"><span data-stu-id="c222c-105">gRPC retries is a feature that allows gRPC clients to automatically retry failed calls.</span></span> <span data-ttu-id="c222c-106">本文介绍如何配置重试策略，以便在 .NET 中创建可复原的容错 gRPC 应用。</span><span class="sxs-lookup"><span data-stu-id="c222c-106">This article discusses how to configure a retry policy to make resilient, fault tolerant gRPC apps in .NET.</span></span>

<span data-ttu-id="c222c-107">gRPC 重试需要 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.36.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c222c-107">gRPC retries requires [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.36.0 or later.</span></span>

## <a name="transient-fault-handling"></a><span data-ttu-id="c222c-108">暂时性故障处理</span><span class="sxs-lookup"><span data-stu-id="c222c-108">Transient fault handling</span></span>

<span data-ttu-id="c222c-109">暂时性故障可能会中断 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="c222c-109">gRPC calls can be interrupted by transient faults.</span></span> <span data-ttu-id="c222c-110">暂时性故障包括：</span><span class="sxs-lookup"><span data-stu-id="c222c-110">Transient faults include:</span></span>

* <span data-ttu-id="c222c-111">暂时失去网络连接。</span><span class="sxs-lookup"><span data-stu-id="c222c-111">Momentary loss of network connectivity.</span></span>
* <span data-ttu-id="c222c-112">服务暂时不可用。</span><span class="sxs-lookup"><span data-stu-id="c222c-112">Temporary unavailability of a service.</span></span>
* <span data-ttu-id="c222c-113">由于服务器负载导致超时。</span><span class="sxs-lookup"><span data-stu-id="c222c-113">Timeouts due to server load.</span></span>

<span data-ttu-id="c222c-114">gRPC 调用中断时，客户端会引发 `RpcException` 并提供有关错误的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c222c-114">When a gRPC call is interrupted, the client throws an `RpcException` with details about the error.</span></span> <span data-ttu-id="c222c-115">客户端应用必须捕获异常并选择如何处理错误。</span><span class="sxs-lookup"><span data-stu-id="c222c-115">The client app must catch the exception and choose how to handle the error.</span></span>

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

<span data-ttu-id="c222c-116">在整个应用中复制重试逻辑是非常冗长的代码，容易出错。</span><span class="sxs-lookup"><span data-stu-id="c222c-116">Duplicating retry logic throughout an app is verbose and error prone.</span></span> <span data-ttu-id="c222c-117">幸运的是，.NET gRPC 客户端拥有自动重试的内置支持。</span><span class="sxs-lookup"><span data-stu-id="c222c-117">Fortunately the .NET gRPC client has a built-in support for automatic retries.</span></span>

## <a name="configure-a-grpc-retry-policy"></a><span data-ttu-id="c222c-118">配置 gRPC 重试策略</span><span class="sxs-lookup"><span data-stu-id="c222c-118">Configure a gRPC retry policy</span></span>

<span data-ttu-id="c222c-119">重试策略在创建 gRPC 通道时配置一次：</span><span class="sxs-lookup"><span data-stu-id="c222c-119">A retry policy is configured once when a gRPC channel is created:</span></span>

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

<span data-ttu-id="c222c-120">上述代码：</span><span class="sxs-lookup"><span data-stu-id="c222c-120">The preceding code:</span></span>

* <span data-ttu-id="c222c-121">创建一个 `MethodConfig`。</span><span class="sxs-lookup"><span data-stu-id="c222c-121">Creates a `MethodConfig`.</span></span> <span data-ttu-id="c222c-122">重试策略可以按方法配置，而方法可以使用 `Names` 属性进行匹配。</span><span class="sxs-lookup"><span data-stu-id="c222c-122">Retry policies can be configured per-method and methods are matched using the `Names` property.</span></span> <span data-ttu-id="c222c-123">此方法配置有 `MethodName.Default`，因此它将应用于此通道调用的所有 gRPC 方法。</span><span class="sxs-lookup"><span data-stu-id="c222c-123">This method is configured with `MethodName.Default`, so it's applied to all gRPC methods called by this channel.</span></span>
* <span data-ttu-id="c222c-124">配置重试策略。</span><span class="sxs-lookup"><span data-stu-id="c222c-124">Configures a retry policy.</span></span> <span data-ttu-id="c222c-125">此策略将指示客户端自动重试状态代码为 `Unavailable` 的失败 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="c222c-125">This policy instructs clients to automatically retry gRPC calls that fail with the status code `Unavailable`.</span></span>
* <span data-ttu-id="c222c-126">通过设置 `GrpcChannelOptions.ServiceConfig`，将创建的通道配置为使用该重试策略。</span><span class="sxs-lookup"><span data-stu-id="c222c-126">Configures the created channel to use the retry policy by setting `GrpcChannelOptions.ServiceConfig`.</span></span>

<span data-ttu-id="c222c-127">随着该通道一起创建的 gRPC 客户端将自动重试失败的调用：</span><span class="sxs-lookup"><span data-stu-id="c222c-127">gRPC clients created with the channel will automatically retry failed calls:</span></span>

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="when-retries-are-valid"></a><span data-ttu-id="c222c-128">重试何时有效</span><span class="sxs-lookup"><span data-stu-id="c222c-128">When retries are valid</span></span>

<span data-ttu-id="c222c-129">如果失败状态代码与配置的状态代码匹配并且先前的尝试次数少于最大尝试次数，则会重试调用。</span><span class="sxs-lookup"><span data-stu-id="c222c-129">Calls are retried if the failing status code matches a configured status code and the previous number of attempts is less than the maximum attempts.</span></span> <span data-ttu-id="c222c-130">在某些情况下，不可重试 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="c222c-130">In certain cases it is not valid to retry a gRPC call.</span></span> <span data-ttu-id="c222c-131">已提交调用时，就会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="c222c-131">These cases occur when the call has been committed.</span></span>

<span data-ttu-id="c222c-132">在以下两种情况下，将提交 gRPC 调用：</span><span class="sxs-lookup"><span data-stu-id="c222c-132">A gRPC call becomes committed in two scenarios:</span></span>

* <span data-ttu-id="c222c-133">客户端收到响应头。</span><span class="sxs-lookup"><span data-stu-id="c222c-133">The client receives response headers.</span></span> <span data-ttu-id="c222c-134">调用 `ServerCallContext.WriteResponseHeadersAsync` 或将第一个消息写入服务器响应流时，服务器会发送响应头。</span><span class="sxs-lookup"><span data-stu-id="c222c-134">Response headers are sent by the server when `ServerCallContext.WriteResponseHeadersAsync` is called, or when the first message is written to the server response stream.</span></span>
* <span data-ttu-id="c222c-135">客户端的传出消息（如果是流式处理则为消息）已超出客户端的最大缓冲区大小。</span><span class="sxs-lookup"><span data-stu-id="c222c-135">The client's outgoing message (or messages if streaming) has exceeded the client's maximum buffer size.</span></span> <span data-ttu-id="c222c-136">`MaxRetryBufferSize` 和 `MaxRetryBufferPerCallSize` [在通道上配置](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="c222c-136">`MaxRetryBufferSize` and `MaxRetryBufferPerCallSize` are [configured on the channel](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="c222c-137">无论状态代码是什么或以前的尝试次数是多少，提交的调用都无法重试。</span><span class="sxs-lookup"><span data-stu-id="c222c-137">Committed calls won't retry, regardless of the status code or the previous number of attempts.</span></span>

### <a name="streaming-calls"></a><span data-ttu-id="c222c-138">流式处理调用</span><span class="sxs-lookup"><span data-stu-id="c222c-138">Streaming calls</span></span>

<span data-ttu-id="c222c-139">流式处理调用可以与 gRPC 重试一起使用，但在将它们一起使用时，务必注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="c222c-139">Streaming calls can be used with gRPC retries, but there are important considerations when they are used together:</span></span>

* <span data-ttu-id="c222c-140">服务器流式处理、双向流式处理： 在已收到第一个消息后，从服务器返回多个消息的流式处理 RPC 无法重试。</span><span class="sxs-lookup"><span data-stu-id="c222c-140">**Server streaming**, **bidirectional streaming**: Streaming RPCs that return multiple messages from the server won't retry after the first message has been received.</span></span>
* <span data-ttu-id="c222c-141">客户端流式处理、双向流式处理： 传出消息超出客户端的最大缓冲区大小时，向服务器发送多个消息的流式处理 RPC 无法重试。</span><span class="sxs-lookup"><span data-stu-id="c222c-141">**Client streaming**, **bidirectional streaming**: Streaming RPCs that send multiple messages to the server won't retry if the outgoing messages have exceeded the client's maximum buffer size.</span></span>

<span data-ttu-id="c222c-142">有关详细信息，请参阅[重试何时有效](#when-retries-are-valid)。</span><span class="sxs-lookup"><span data-stu-id="c222c-142">For more information, see [When retries are valid](#when-retries-are-valid).</span></span>

### <a name="grpc-retry-options"></a><span data-ttu-id="c222c-143">gRPC 重试选项</span><span class="sxs-lookup"><span data-stu-id="c222c-143">gRPC retry options</span></span>

<span data-ttu-id="c222c-144">下表描述了用于配置 gRPC 重试策略的选项：</span><span class="sxs-lookup"><span data-stu-id="c222c-144">The following table describes options for configuring gRPC retry policies:</span></span>

| <span data-ttu-id="c222c-145">选项</span><span class="sxs-lookup"><span data-stu-id="c222c-145">Option</span></span> | <span data-ttu-id="c222c-146">描述</span><span class="sxs-lookup"><span data-stu-id="c222c-146">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="c222c-147">最大调用尝试次数，包括原始尝试。</span><span class="sxs-lookup"><span data-stu-id="c222c-147">The maximum number of call attempts, including the original attempt.</span></span> <span data-ttu-id="c222c-148">此值受 `GrpcChannelOptions.MaxRetryAttempts`（默认值为 5）的限制。</span><span class="sxs-lookup"><span data-stu-id="c222c-148">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="c222c-149">必须为该选项提供值，且值必须大于 1。</span><span class="sxs-lookup"><span data-stu-id="c222c-149">A value is required and must be greater than 1.</span></span> |
| `InitialBackoff` | <span data-ttu-id="c222c-150">重试尝试之间的初始退避延迟。</span><span class="sxs-lookup"><span data-stu-id="c222c-150">The initial backoff delay between retry attempts.</span></span> <span data-ttu-id="c222c-151">介于 0 与当前退避之间的随机延迟确定何时进行下一次重试尝试。</span><span class="sxs-lookup"><span data-stu-id="c222c-151">A randomized delay between 0 and the current backoff determines when the next retry attempt is made.</span></span> <span data-ttu-id="c222c-152">每次尝试后，当前退避将乘以 `BackoffMultiplier`。</span><span class="sxs-lookup"><span data-stu-id="c222c-152">After each attempt, the current backoff is multiplied by `BackoffMultiplier`.</span></span> <span data-ttu-id="c222c-153">必须为该选项提供值，且值必须大于 0。</span><span class="sxs-lookup"><span data-stu-id="c222c-153">A value is required and must be greater than zero.</span></span> |
| `MaxBackoff` | <span data-ttu-id="c222c-154">最大退避会限制指数退避增长的上限。</span><span class="sxs-lookup"><span data-stu-id="c222c-154">The maximum backoff places an upper limit on exponential backoff growth.</span></span> <span data-ttu-id="c222c-155">必须为该选项提供值，且值必须大于 0。</span><span class="sxs-lookup"><span data-stu-id="c222c-155">A value is required and must be greater than zero.</span></span> |
| `BackoffMultiplier` | <span data-ttu-id="c222c-156">每次重试尝试后，退避将乘以该值，并将在乘数大于 1 的情况下以指数方式增加。</span><span class="sxs-lookup"><span data-stu-id="c222c-156">The backoff will be multiplied by this value after each retry attempt and will increase exponentially when the multiplier is greater than 1.</span></span> <span data-ttu-id="c222c-157">必须为该选项提供值，且值必须大于 0。</span><span class="sxs-lookup"><span data-stu-id="c222c-157">A value is required and must be greater than zero.</span></span> |
| `RetryableStatusCodes` | <span data-ttu-id="c222c-158">状态代码的集合。</span><span class="sxs-lookup"><span data-stu-id="c222c-158">A collection of status codes.</span></span> <span data-ttu-id="c222c-159">具有匹配状态的失败 gRPC 调用将自动重试。</span><span class="sxs-lookup"><span data-stu-id="c222c-159">A gRPC call that fails with a matching status will be automatically retried.</span></span> <span data-ttu-id="c222c-160">有关状态代码的更多信息，请参阅[状态代码及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="c222c-160">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> <span data-ttu-id="c222c-161">至少需要提供一个可重试的状态代码。</span><span class="sxs-lookup"><span data-stu-id="c222c-161">At least one retryable status code is required.</span></span> |

## <a name="hedging"></a><span data-ttu-id="c222c-162">Hedging</span><span class="sxs-lookup"><span data-stu-id="c222c-162">Hedging</span></span>

<span data-ttu-id="c222c-163">Hedging 是一种备选重试策略。</span><span class="sxs-lookup"><span data-stu-id="c222c-163">Hedging is an alternative retry strategy.</span></span> <span data-ttu-id="c222c-164">Hedging 允许在不等待响应的情况下，主动发送单个 gRPC 调用的多个副本。</span><span class="sxs-lookup"><span data-stu-id="c222c-164">Hedging enables aggressively sending multiple copies of a single gRPC call without waiting for a response.</span></span> <span data-ttu-id="c222c-165">Hedged gRPC 调用可以在服务器上执行多次，并使用第一个成功的结果。</span><span class="sxs-lookup"><span data-stu-id="c222c-165">Hedged gRPC calls may be executed multiple times on the server and the first successful result is used.</span></span> <span data-ttu-id="c222c-166">重要的是，务必仅针对可安全执行多次且不会造成负面影响的方法启用 hedging。</span><span class="sxs-lookup"><span data-stu-id="c222c-166">It's important that hedging is only enabled for methods that are safe to execute multiple times without adverse effect.</span></span>

<span data-ttu-id="c222c-167">与重试相比，Hedging 具有以下优缺点：</span><span class="sxs-lookup"><span data-stu-id="c222c-167">Hedging has pros and cons when compared to retries:</span></span> 

* <span data-ttu-id="c222c-168">Hedging 的优点是，它可能会更快地返回成功的结果。</span><span class="sxs-lookup"><span data-stu-id="c222c-168">An advantage to hedging is it might return a successful result faster.</span></span> <span data-ttu-id="c222c-169">它允许同时进行多个 gRPC 调用，并在出现第一个成功的结果时完成。</span><span class="sxs-lookup"><span data-stu-id="c222c-169">It allows for multiple simultaneously gRPC calls and will complete when the first successful result is available.</span></span> 
* <span data-ttu-id="c222c-170">Hedging 的一个缺点是它可能会造成浪费。</span><span class="sxs-lookup"><span data-stu-id="c222c-170">A disadvantage to hedging is it can be wasteful.</span></span> <span data-ttu-id="c222c-171">进行了多个调用并且这些调用全部成功。</span><span class="sxs-lookup"><span data-stu-id="c222c-171">Multiple calls could be made and all succeed.</span></span> <span data-ttu-id="c222c-172">而仅使用第一个结果，并放弃其余结果。</span><span class="sxs-lookup"><span data-stu-id="c222c-172">Only the first result is used and the rest are discarded.</span></span>

## <a name="configure-a-grpc-hedging-policy"></a><span data-ttu-id="c222c-173">配置 gRPC hedging 策略</span><span class="sxs-lookup"><span data-stu-id="c222c-173">Configure a gRPC hedging policy</span></span>

<span data-ttu-id="c222c-174">Hedging 策略的配置类似于重试策略。</span><span class="sxs-lookup"><span data-stu-id="c222c-174">A hedging policy is configured like a retry policy.</span></span> <span data-ttu-id="c222c-175">需要注意的是，hedging 策略不能与重试策略结合使用。</span><span class="sxs-lookup"><span data-stu-id="c222c-175">Note that a hedging policy can't be combined with a retry policy.</span></span>

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

### <a name="grpc-hedging-options"></a><span data-ttu-id="c222c-176">gRPC hedging 选项</span><span class="sxs-lookup"><span data-stu-id="c222c-176">gRPC hedging options</span></span>

<span data-ttu-id="c222c-177">下表描述了用于配置 gRPC hedging 策略的选项：</span><span class="sxs-lookup"><span data-stu-id="c222c-177">The following table describes options for configuring gRPC hedging policies:</span></span>

| <span data-ttu-id="c222c-178">选项</span><span class="sxs-lookup"><span data-stu-id="c222c-178">Option</span></span> | <span data-ttu-id="c222c-179">描述</span><span class="sxs-lookup"><span data-stu-id="c222c-179">Description</span></span> |
| ------ | ----------- |
| `MaxAttempts` | <span data-ttu-id="c222c-180">Hedging 策略将发送的调用数量上限。</span><span class="sxs-lookup"><span data-stu-id="c222c-180">The hedging policy will send up to this number of calls.</span></span> <span data-ttu-id="c222c-181">`MaxAttempts` 表示所有尝试的总数，包括原始尝试。</span><span class="sxs-lookup"><span data-stu-id="c222c-181">`MaxAttempts` represents the total number of all attempts, including the original attempt.</span></span> <span data-ttu-id="c222c-182">此值受 `GrpcChannelOptions.MaxRetryAttempts`（默认值为 5）的限制。</span><span class="sxs-lookup"><span data-stu-id="c222c-182">This value is limited by `GrpcChannelOptions.MaxRetryAttempts` which defaults to 5.</span></span> <span data-ttu-id="c222c-183">必须为该选项提供值，且值必须大于 2。</span><span class="sxs-lookup"><span data-stu-id="c222c-183">A value is required and must be 2 or greater.</span></span> |
| `HedgingDelay` | <span data-ttu-id="c222c-184">第一次调用立即发送，但后续 hedging 调用将按该值延迟发送。</span><span class="sxs-lookup"><span data-stu-id="c222c-184">The first call will be sent immediately, but the subsequent hedging calls will be delayed by this value.</span></span> <span data-ttu-id="c222c-185">如果延迟设置为零或 `null`，那么所有所有 hedged 调用都将立即发送。</span><span class="sxs-lookup"><span data-stu-id="c222c-185">When the delay is set to zero or `null`, all hedged calls are sent immediately.</span></span> <span data-ttu-id="c222c-186">默认值为 0。</span><span class="sxs-lookup"><span data-stu-id="c222c-186">Default value is zero.</span></span> |
| `NonFatalStatusCodes` | <span data-ttu-id="c222c-187">指示其他 hedge 调用仍可能会成功的状态代码集合。</span><span class="sxs-lookup"><span data-stu-id="c222c-187">A collection of status codes which indicate other hedge calls may still succeed.</span></span> <span data-ttu-id="c222c-188">如果服务器返回非致命状态代码，hedged 调用将继续。</span><span class="sxs-lookup"><span data-stu-id="c222c-188">If a non-fatal status code is returned by the server, hedged calls will continue.</span></span> <span data-ttu-id="c222c-189">否则，将取消未完成的请求，并将错误返回到应用。</span><span class="sxs-lookup"><span data-stu-id="c222c-189">Otherwise, outstanding requests will be canceled and the error returned to the app.</span></span> <span data-ttu-id="c222c-190">有关状态代码的更多信息，请参阅[状态代码及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。</span><span class="sxs-lookup"><span data-stu-id="c222c-190">For more information about status codes, see [Status codes and their use in gRPC](https://grpc.github.io/grpc/core/md_doc_statuscodes.html).</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c222c-191">其他资源</span><span class="sxs-lookup"><span data-stu-id="c222c-191">Additional resources</span></span>

* <xref:grpc/client>
* [<span data-ttu-id="c222c-192">重试常规指导 - 适用于云应用程序的最佳做法</span><span class="sxs-lookup"><span data-stu-id="c222c-192">Retry general guidance - Best practices for cloud applications</span></span>](/azure/architecture/best-practices/transient-faults)
