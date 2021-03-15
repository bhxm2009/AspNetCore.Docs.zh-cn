---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: 15d011e88ab291173668a0b6dc5f46e97fdfeff0
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605706"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="06a4c-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="06a4c-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="06a4c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06a4c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06a4c-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="06a4c-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-106">Each component:</span></span>

* <span data-ttu-id="06a4c-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="06a4c-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="06a4c-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="06a4c-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="06a4c-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="06a4c-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="06a4c-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="06a4c-113">这些可重用的类和并行匿名方法即为中间件，也叫中间件组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="06a4c-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="06a4c-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="06a4c-115">当中间件短路时，它被称为“终端中间件”，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="06a4c-116"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="06a4c-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="06a4c-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="06a4c-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="06a4c-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="06a4c-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="06a4c-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="06a4c-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="06a4c-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="06a4c-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="06a4c-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="06a4c-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="06a4c-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="06a4c-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="06a4c-129">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="06a4c-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="06a4c-130">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="06a4c-131">可通过不调用 next 参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="06a4c-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="06a4c-132">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="06a4c-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="06a4c-133">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”。</span><span class="sxs-lookup"><span data-stu-id="06a4c-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="06a4c-134">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="06a4c-135">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件的作用。</span><span class="sxs-lookup"><span data-stu-id="06a4c-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="06a4c-136">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="06a4c-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="06a4c-137">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="06a4c-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="06a4c-138">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="06a4c-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="06a4c-139">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="06a4c-140">例如，[设置标头和状态代码更改将引发异常](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="06a4c-141">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="06a4c-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="06a4c-142">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="06a4c-142">May cause a protocol violation.</span></span> <span data-ttu-id="06a4c-143">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="06a4c-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="06a4c-144">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="06a4c-144">May corrupt the body format.</span></span> <span data-ttu-id="06a4c-145">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="06a4c-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="06a4c-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="06a4c-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="06a4c-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委托不会收到 `next` 参数。</span><span class="sxs-lookup"><span data-stu-id="06a4c-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="06a4c-148">第一个 `Run` 委托始终为终端，用于终止管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="06a4c-149">`Run` 是一种约定。</span><span class="sxs-lookup"><span data-stu-id="06a4c-149">`Run` is a convention.</span></span> <span data-ttu-id="06a4c-150">某些中间件组件可能会公开在管道末尾运行的 `Run[Middleware]` 方法：</span><span class="sxs-lookup"><span data-stu-id="06a4c-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="06a4c-151">在前面的示例中，`Run` 委托将 `"Hello from 2nd delegate."` 写入响应，然后终止管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="06a4c-152">如果在 `Run` 委托之后添加了另一个 `Use` 或 `Run` 委托，则不会调用该委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="06a4c-153">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="06a4c-153">Middleware order</span></span>

<span data-ttu-id="06a4c-154">下图显示了 ASP.NET Core MVC 和 Razor Pages 应用的完整请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="06a4c-155">你可以在典型应用中了解现有中间件的顺序，以及在哪里添加自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="06a4c-156">你可以完全控制如何重新排列现有中间件，或根据场景需要注入新的自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 中间件管道](index/_static/middleware-pipeline.svg)

<span data-ttu-id="06a4c-158">上图中的“终结点”中间件为相应的应用类型（MVC 或 Razor Pages）执行筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 筛选器管道](index/_static/mvc-endpoint.svg)

<span data-ttu-id="06a4c-160">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="06a4c-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="06a4c-161">此顺序对于安全性、性能和功能至关重要。</span><span class="sxs-lookup"><span data-stu-id="06a4c-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="06a4c-162">下面的 `Startup.Configure` 方法按照典型的建议顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-162">The following `Startup.Configure` method adds security-related middleware components in the typical recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="06a4c-163">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="06a4c-163">In the preceding code:</span></span>

* <span data-ttu-id="06a4c-164">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="06a4c-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="06a4c-165">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="06a4c-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="06a4c-166">例如：</span><span class="sxs-lookup"><span data-stu-id="06a4c-166">For example:</span></span>
  * <span data-ttu-id="06a4c-167">`UseCors`、`UseAuthentication` 和 `UseAuthorization` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="06a4c-168">由于[此错误](https://github.com/dotnet/aspnetcore/issues/23218)，`UseCors` 当前必须在 `UseResponseCaching` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="06a4c-169">在某些场景下，中间件将具有不同的排序。</span><span class="sxs-lookup"><span data-stu-id="06a4c-169">In some scenarios, middleware will have different ordering.</span></span> <span data-ttu-id="06a4c-170">例如，高速缓存和压缩排序是特定于场景的，存在多个有效的排序。</span><span class="sxs-lookup"><span data-stu-id="06a4c-170">For example, caching and compression ordering is scenario specific, and there's multiple valid orderings.</span></span> <span data-ttu-id="06a4c-171">例如：</span><span class="sxs-lookup"><span data-stu-id="06a4c-171">For example:</span></span>

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

<span data-ttu-id="06a4c-172">使用前面的代码，可以通过缓存压缩的响应来保存 CPU，但你可能最终会使用不同的压缩算法（如 gzip 或 brotli）来缓存资源的多个表示形式。</span><span class="sxs-lookup"><span data-stu-id="06a4c-172">With the preceding code, CPU could be saved by caching the compressed response, but you might end up caching multiple representations of a resource using different compression algorithms such as gzip or brotli.</span></span>

<span data-ttu-id="06a4c-173">以下排序结合了静态文件以允许缓存压缩的静态文件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-173">The following ordering combines static files to allow caching compressed static files:</span></span>

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
app.UseStaticFiles();
```

<span data-ttu-id="06a4c-174">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-174">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="06a4c-175">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="06a4c-175">Exception/error handling</span></span>
   * <span data-ttu-id="06a4c-176">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="06a4c-176">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="06a4c-177">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="06a4c-177">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="06a4c-178">数据库错误页中间件报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="06a4c-178">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="06a4c-179">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="06a4c-179">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="06a4c-180">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-180">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="06a4c-181">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="06a4c-181">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="06a4c-182">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="06a4c-182">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="06a4c-183">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="06a4c-183">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="06a4c-184">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="06a4c-184">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="06a4c-185">用于路由请求的路由中间件 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-185">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="06a4c-186">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="06a4c-186">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="06a4c-187">用于授权用户访问安全资源的授权中间件 (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-187">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="06a4c-188">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="06a4c-188">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="06a4c-189">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-189">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="06a4c-190">用于将 Razor Pages 终结点添加到请求管道的终结点路由中间件（带有 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A> 的 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>）。</span><span class="sxs-lookup"><span data-stu-id="06a4c-190">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="06a4c-191">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="06a4c-191">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="06a4c-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="06a4c-193">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-193">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="06a4c-194">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-194">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="06a4c-195">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="06a4c-195">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="06a4c-196">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-196">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="06a4c-197">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="06a4c-197">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="06a4c-198">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-198">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="06a4c-199">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="06a4c-199">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="06a4c-200">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor Page 或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="06a4c-200">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="06a4c-201">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="06a4c-201">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="06a4c-202">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-202">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="06a4c-203">可以压缩 Razor Pages 响应。</span><span class="sxs-lookup"><span data-stu-id="06a4c-203">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseRouting();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="06a4c-204">对于单页应用程序 (SPA)，SPA 中间件 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> 通常是中间件管道中的最后一个。</span><span class="sxs-lookup"><span data-stu-id="06a4c-204">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="06a4c-205">SPA 中间件处于最后的作用是：</span><span class="sxs-lookup"><span data-stu-id="06a4c-205">The SPA middleware comes last:</span></span>

* <span data-ttu-id="06a4c-206">允许所有其他中间件首先响应匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-206">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="06a4c-207">允许具有客户端侧路由的 SPA 针对服务器应用无法识别的所有路由运行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-207">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="06a4c-208">若要详细了解 SPA，请参阅 [React](xref:spa/react) 和 [Angular](xref:spa/angular) 项目模板的指南。</span><span class="sxs-lookup"><span data-stu-id="06a4c-208">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="06a4c-209">转接头中间件顺序</span><span class="sxs-lookup"><span data-stu-id="06a4c-209">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="06a4c-210">对中间件管道进行分支</span><span class="sxs-lookup"><span data-stu-id="06a4c-210">Branch the middleware pipeline</span></span>

<span data-ttu-id="06a4c-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="06a4c-212">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-212">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="06a4c-213">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-213">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="06a4c-214">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="06a4c-214">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="06a4c-215">请求</span><span class="sxs-lookup"><span data-stu-id="06a4c-215">Request</span></span>             | <span data-ttu-id="06a4c-216">响应</span><span class="sxs-lookup"><span data-stu-id="06a4c-216">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="06a4c-217">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="06a4c-217">localhost:1234</span></span>      | <span data-ttu-id="06a4c-218">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="06a4c-218">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="06a4c-219">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="06a4c-219">localhost:1234/map1</span></span> | <span data-ttu-id="06a4c-220">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="06a4c-220">Map Test 1</span></span>                   |
| <span data-ttu-id="06a4c-221">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="06a4c-221">localhost:1234/map2</span></span> | <span data-ttu-id="06a4c-222">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="06a4c-222">Map Test 2</span></span>                   |
| <span data-ttu-id="06a4c-223">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="06a4c-223">localhost:1234/map3</span></span> | <span data-ttu-id="06a4c-224">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="06a4c-224">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="06a4c-225">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="06a4c-225">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="06a4c-226">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="06a4c-226">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="06a4c-227">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="06a4c-227">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="06a4c-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="06a4c-229">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-229">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="06a4c-230">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="06a4c-230">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="06a4c-231">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应：</span><span class="sxs-lookup"><span data-stu-id="06a4c-231">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="06a4c-232">请求</span><span class="sxs-lookup"><span data-stu-id="06a4c-232">Request</span></span>                       | <span data-ttu-id="06a4c-233">响应</span><span class="sxs-lookup"><span data-stu-id="06a4c-233">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="06a4c-234">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="06a4c-234">localhost:1234</span></span>                | <span data-ttu-id="06a4c-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="06a4c-235">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="06a4c-236">localhost:1234/?branch=main</span><span class="sxs-lookup"><span data-stu-id="06a4c-236">localhost:1234/?branch=main</span></span> | <span data-ttu-id="06a4c-237">Branch used = main</span><span class="sxs-lookup"><span data-stu-id="06a4c-237">Branch used = main</span></span>         |

<span data-ttu-id="06a4c-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> 也基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="06a4c-239">与 `MapWhen` 不同的是，如果这个分支不发生短路或包含终端中间件，则会重新加入主管道：</span><span class="sxs-lookup"><span data-stu-id="06a4c-239">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=18-19)]

<span data-ttu-id="06a4c-240">在前面的示例中，响应 "Hello from main pipeline."</span><span class="sxs-lookup"><span data-stu-id="06a4c-240">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="06a4c-241">是为所有请求编写的。</span><span class="sxs-lookup"><span data-stu-id="06a4c-241">is written for all requests.</span></span> <span data-ttu-id="06a4c-242">如果请求中包含查询字符串变量 `branch`，则在重新加入主管道之前会记录其值。</span><span class="sxs-lookup"><span data-stu-id="06a4c-242">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="06a4c-243">内置中间件</span><span class="sxs-lookup"><span data-stu-id="06a4c-243">Built-in middleware</span></span>

<span data-ttu-id="06a4c-244">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-244">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="06a4c-245">“顺序”列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-245">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="06a4c-246">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”。</span><span class="sxs-lookup"><span data-stu-id="06a4c-246">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="06a4c-247">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="06a4c-247">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="06a4c-248">中间件</span><span class="sxs-lookup"><span data-stu-id="06a4c-248">Middleware</span></span> | <span data-ttu-id="06a4c-249">描述</span><span class="sxs-lookup"><span data-stu-id="06a4c-249">Description</span></span> | <span data-ttu-id="06a4c-250">顺序</span><span class="sxs-lookup"><span data-stu-id="06a4c-250">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="06a4c-251">身份验证</span><span class="sxs-lookup"><span data-stu-id="06a4c-251">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="06a4c-252">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-252">Provides authentication support.</span></span> | <span data-ttu-id="06a4c-253">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-253">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="06a4c-254">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-254">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="06a4c-255">授权</span><span class="sxs-lookup"><span data-stu-id="06a4c-255">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="06a4c-256">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-256">Provides authorization support.</span></span> | <span data-ttu-id="06a4c-257">紧接在身份验证中间件之后。</span><span class="sxs-lookup"><span data-stu-id="06a4c-257">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="06a4c-258">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="06a4c-258">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="06a4c-259">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="06a4c-259">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="06a4c-260">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-260">Before middleware that issues cookies.</span></span> <span data-ttu-id="06a4c-261">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-261">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="06a4c-262">CORS</span><span class="sxs-lookup"><span data-stu-id="06a4c-262">CORS</span></span>](xref:security/cors) | <span data-ttu-id="06a4c-263">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="06a4c-263">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="06a4c-264">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-264">Before components that use CORS.</span></span> <span data-ttu-id="06a4c-265">由于[此错误](https://github.com/dotnet/aspnetcore/issues/23218)，`UseCors` 当前必须在 `UseResponseCaching` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-265">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="06a4c-266">诊断</span><span class="sxs-lookup"><span data-stu-id="06a4c-266">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="06a4c-267">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-267">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="06a4c-268">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-268">Before components that generate errors.</span></span> <span data-ttu-id="06a4c-269">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-269">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="06a4c-270">转接头</span><span class="sxs-lookup"><span data-stu-id="06a4c-270">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="06a4c-271">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-271">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="06a4c-272">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-272">Before components that consume the updated fields.</span></span> <span data-ttu-id="06a4c-273">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="06a4c-273">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="06a4c-274">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="06a4c-274">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="06a4c-275">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="06a4c-275">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="06a4c-276">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-276">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="06a4c-277">标头传播</span><span class="sxs-lookup"><span data-stu-id="06a4c-277">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="06a4c-278">将 HTTP 标头从传入的请求传播到传出的 HTTP 客户端请求中。</span><span class="sxs-lookup"><span data-stu-id="06a4c-278">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="06a4c-279">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="06a4c-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="06a4c-280">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="06a4c-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="06a4c-281">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="06a4c-282">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="06a4c-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="06a4c-283">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="06a4c-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="06a4c-284">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="06a4c-285">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="06a4c-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="06a4c-286">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="06a4c-287">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="06a4c-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="06a4c-288">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="06a4c-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="06a4c-289">MVC</span><span class="sxs-lookup"><span data-stu-id="06a4c-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="06a4c-290">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="06a4c-291">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="06a4c-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="06a4c-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="06a4c-293">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="06a4c-294">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="06a4c-295">响应缓存</span><span class="sxs-lookup"><span data-stu-id="06a4c-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="06a4c-296">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-296">Provides support for caching responses.</span></span> | <span data-ttu-id="06a4c-297">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-297">Before components that require caching.</span></span> <span data-ttu-id="06a4c-298">`UseCORS` 必须在 `UseResponseCaching` 之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-298">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="06a4c-299">响应压缩</span><span class="sxs-lookup"><span data-stu-id="06a4c-299">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="06a4c-300">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-300">Provides support for compressing responses.</span></span> | <span data-ttu-id="06a4c-301">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-301">Before components that require compression.</span></span> |
| [<span data-ttu-id="06a4c-302">请求本地化</span><span class="sxs-lookup"><span data-stu-id="06a4c-302">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="06a4c-303">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-303">Provides localization support.</span></span> | <span data-ttu-id="06a4c-304">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-304">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="06a4c-305">终结点路由</span><span class="sxs-lookup"><span data-stu-id="06a4c-305">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="06a4c-306">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="06a4c-306">Defines and constrains request routes.</span></span> | <span data-ttu-id="06a4c-307">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-307">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="06a4c-308">SPA</span><span class="sxs-lookup"><span data-stu-id="06a4c-308">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="06a4c-309">通过返回单页应用程序 (SPA) 的默认页面，在中间件链中处理来自这个点的所有请求</span><span class="sxs-lookup"><span data-stu-id="06a4c-309">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="06a4c-310">在链中处于靠后位置，因此其他服务于静态文件、MVC 操作等内容的中间件占据优先位置。</span><span class="sxs-lookup"><span data-stu-id="06a4c-310">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="06a4c-311">会话</span><span class="sxs-lookup"><span data-stu-id="06a4c-311">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="06a4c-312">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-312">Provides support for managing user sessions.</span></span> | <span data-ttu-id="06a4c-313">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-313">Before components that require Session.</span></span> | 
| [<span data-ttu-id="06a4c-314">静态文件</span><span class="sxs-lookup"><span data-stu-id="06a4c-314">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="06a4c-315">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-315">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="06a4c-316">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-316">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="06a4c-317">URL 重写</span><span class="sxs-lookup"><span data-stu-id="06a4c-317">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="06a4c-318">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-318">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="06a4c-319">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-319">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="06a4c-320">WebSockets</span><span class="sxs-lookup"><span data-stu-id="06a4c-320">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="06a4c-321">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="06a4c-321">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="06a4c-322">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-322">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="06a4c-323">其他资源</span><span class="sxs-lookup"><span data-stu-id="06a4c-323">Additional resources</span></span>

* <span data-ttu-id="06a4c-324">[生存期和注册选项](xref:fundamentals/dependency-injection#lifetime-and-registration-options)包含已设置范围、临时性和单一实例生存期服务的中间件的完整示例  。</span><span class="sxs-lookup"><span data-stu-id="06a4c-324">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06a4c-325">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06a4c-325">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06a4c-326">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-326">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="06a4c-327">每个组件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-327">Each component:</span></span>

* <span data-ttu-id="06a4c-328">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-328">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="06a4c-329">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-329">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="06a4c-330">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-330">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="06a4c-331">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-331">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="06a4c-332">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-332">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="06a4c-333">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="06a4c-333">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="06a4c-334">这些可重用的类和并行匿名方法即为中间件，也叫中间件组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-334">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="06a4c-335">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="06a4c-335">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="06a4c-336">当中间件短路时，它被称为“终端中间件”，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-336">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="06a4c-337"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="06a4c-337"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="06a4c-338">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="06a4c-338">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="06a4c-339">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="06a4c-339">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="06a4c-340">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="06a4c-340">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="06a4c-341">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-341">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="06a4c-345">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-345">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="06a4c-346">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-346">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="06a4c-347">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-347">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="06a4c-348">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-348">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="06a4c-349">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-349">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="06a4c-350">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-350">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="06a4c-351">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="06a4c-351">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="06a4c-352">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="06a4c-352">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="06a4c-353">可通过不调用 next 参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="06a4c-353">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="06a4c-354">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="06a4c-354">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="06a4c-355">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”。</span><span class="sxs-lookup"><span data-stu-id="06a4c-355">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="06a4c-356">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-356">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="06a4c-357">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件的作用。</span><span class="sxs-lookup"><span data-stu-id="06a4c-357">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="06a4c-358">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="06a4c-358">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="06a4c-359">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="06a4c-359">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="06a4c-360">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="06a4c-360">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="06a4c-361">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-361">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="06a4c-362">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-362">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="06a4c-363">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="06a4c-363">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="06a4c-364">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="06a4c-364">May cause a protocol violation.</span></span> <span data-ttu-id="06a4c-365">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="06a4c-365">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="06a4c-366">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="06a4c-366">May corrupt the body format.</span></span> <span data-ttu-id="06a4c-367">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="06a4c-367">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="06a4c-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="06a4c-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="06a4c-369">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="06a4c-369">Middleware order</span></span>

<span data-ttu-id="06a4c-370">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="06a4c-370">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="06a4c-371">此顺序对于安全性、性能和功能至关重要。</span><span class="sxs-lookup"><span data-stu-id="06a4c-371">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="06a4c-372">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-372">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="06a4c-373">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="06a4c-373">In the preceding code:</span></span>

* <span data-ttu-id="06a4c-374">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="06a4c-374">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="06a4c-375">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="06a4c-375">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="06a4c-376">例如，`UseCors` 和 `UseAuthentication` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="06a4c-376">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="06a4c-377">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="06a4c-377">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="06a4c-378">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="06a4c-378">Exception/error handling</span></span>
   * <span data-ttu-id="06a4c-379">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="06a4c-379">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="06a4c-380">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="06a4c-380">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="06a4c-381">数据库错误页中间件 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="06a4c-381">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="06a4c-382">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="06a4c-382">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="06a4c-383">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-383">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="06a4c-384">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="06a4c-384">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="06a4c-385">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="06a4c-385">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="06a4c-386">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="06a4c-386">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="06a4c-387">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="06a4c-387">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="06a4c-388">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="06a4c-388">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="06a4c-389">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="06a4c-389">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="06a4c-390">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-390">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="06a4c-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) 将 MVC 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="06a4c-392">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="06a4c-392">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="06a4c-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="06a4c-394">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="06a4c-394">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="06a4c-395">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-395">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="06a4c-396">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="06a4c-396">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="06a4c-397">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-397">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="06a4c-398">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="06a4c-398">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="06a4c-399">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-399">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="06a4c-400">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="06a4c-400">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="06a4c-401">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor Page 或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="06a4c-401">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="06a4c-402">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="06a4c-402">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="06a4c-403">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-403">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="06a4c-404">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="06a4c-404">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="06a4c-405">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="06a4c-405">Use, Run, and Map</span></span>

<span data-ttu-id="06a4c-406">使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 和 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="06a4c-406">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="06a4c-407">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="06a4c-407">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="06a4c-408">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="06a4c-408">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="06a4c-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="06a4c-410">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-410">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="06a4c-411">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-411">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="06a4c-412">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="06a4c-412">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="06a4c-413">请求</span><span class="sxs-lookup"><span data-stu-id="06a4c-413">Request</span></span>             | <span data-ttu-id="06a4c-414">响应</span><span class="sxs-lookup"><span data-stu-id="06a4c-414">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="06a4c-415">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="06a4c-415">localhost:1234</span></span>      | <span data-ttu-id="06a4c-416">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="06a4c-416">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="06a4c-417">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="06a4c-417">localhost:1234/map1</span></span> | <span data-ttu-id="06a4c-418">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="06a4c-418">Map Test 1</span></span>                   |
| <span data-ttu-id="06a4c-419">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="06a4c-419">localhost:1234/map2</span></span> | <span data-ttu-id="06a4c-420">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="06a4c-420">Map Test 2</span></span>                   |
| <span data-ttu-id="06a4c-421">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="06a4c-421">localhost:1234/map3</span></span> | <span data-ttu-id="06a4c-422">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="06a4c-422">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="06a4c-423">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="06a4c-423">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="06a4c-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="06a4c-425">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="06a4c-425">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="06a4c-426">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="06a4c-426">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="06a4c-427">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="06a4c-427">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="06a4c-428">请求</span><span class="sxs-lookup"><span data-stu-id="06a4c-428">Request</span></span>                       | <span data-ttu-id="06a4c-429">响应</span><span class="sxs-lookup"><span data-stu-id="06a4c-429">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="06a4c-430">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="06a4c-430">localhost:1234</span></span>                | <span data-ttu-id="06a4c-431">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="06a4c-431">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="06a4c-432">localhost:1234/?branch=main</span><span class="sxs-lookup"><span data-stu-id="06a4c-432">localhost:1234/?branch=main</span></span> | <span data-ttu-id="06a4c-433">Branch used = main</span><span class="sxs-lookup"><span data-stu-id="06a4c-433">Branch used = main</span></span>         |

<span data-ttu-id="06a4c-434">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="06a4c-434">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="06a4c-435">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="06a4c-435">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="06a4c-436">内置中间件</span><span class="sxs-lookup"><span data-stu-id="06a4c-436">Built-in middleware</span></span>

<span data-ttu-id="06a4c-437">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-437">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="06a4c-438">“顺序”列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-438">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="06a4c-439">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”。</span><span class="sxs-lookup"><span data-stu-id="06a4c-439">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="06a4c-440">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="06a4c-440">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="06a4c-441">中间件</span><span class="sxs-lookup"><span data-stu-id="06a4c-441">Middleware</span></span> | <span data-ttu-id="06a4c-442">描述</span><span class="sxs-lookup"><span data-stu-id="06a4c-442">Description</span></span> | <span data-ttu-id="06a4c-443">顺序</span><span class="sxs-lookup"><span data-stu-id="06a4c-443">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="06a4c-444">身份验证</span><span class="sxs-lookup"><span data-stu-id="06a4c-444">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="06a4c-445">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-445">Provides authentication support.</span></span> | <span data-ttu-id="06a4c-446">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-446">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="06a4c-447">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-447">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="06a4c-448">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="06a4c-448">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="06a4c-449">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="06a4c-449">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="06a4c-450">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-450">Before middleware that issues cookies.</span></span> <span data-ttu-id="06a4c-451">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="06a4c-451">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="06a4c-452">CORS</span><span class="sxs-lookup"><span data-stu-id="06a4c-452">CORS</span></span>](xref:security/cors) | <span data-ttu-id="06a4c-453">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="06a4c-453">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="06a4c-454">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-454">Before components that use CORS.</span></span> |
| [<span data-ttu-id="06a4c-455">诊断</span><span class="sxs-lookup"><span data-stu-id="06a4c-455">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="06a4c-456">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-456">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="06a4c-457">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-457">Before components that generate errors.</span></span> <span data-ttu-id="06a4c-458">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-458">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="06a4c-459">转接头</span><span class="sxs-lookup"><span data-stu-id="06a4c-459">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="06a4c-460">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-460">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="06a4c-461">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-461">Before components that consume the updated fields.</span></span> <span data-ttu-id="06a4c-462">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="06a4c-462">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="06a4c-463">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="06a4c-463">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="06a4c-464">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="06a4c-464">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="06a4c-465">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-465">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="06a4c-466">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="06a4c-466">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="06a4c-467">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="06a4c-467">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="06a4c-468">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-468">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="06a4c-469">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="06a4c-469">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="06a4c-470">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="06a4c-470">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="06a4c-471">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-471">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="06a4c-472">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="06a4c-472">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="06a4c-473">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="06a4c-473">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="06a4c-474">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="06a4c-474">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="06a4c-475">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="06a4c-475">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="06a4c-476">MVC</span><span class="sxs-lookup"><span data-stu-id="06a4c-476">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="06a4c-477">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="06a4c-477">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="06a4c-478">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-478">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="06a4c-479">OWIN</span><span class="sxs-lookup"><span data-stu-id="06a4c-479">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="06a4c-480">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="06a4c-480">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="06a4c-481">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-481">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="06a4c-482">响应缓存</span><span class="sxs-lookup"><span data-stu-id="06a4c-482">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="06a4c-483">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-483">Provides support for caching responses.</span></span> | <span data-ttu-id="06a4c-484">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-484">Before components that require caching.</span></span> |
| [<span data-ttu-id="06a4c-485">响应压缩</span><span class="sxs-lookup"><span data-stu-id="06a4c-485">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="06a4c-486">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-486">Provides support for compressing responses.</span></span> | <span data-ttu-id="06a4c-487">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-487">Before components that require compression.</span></span> |
| [<span data-ttu-id="06a4c-488">请求本地化</span><span class="sxs-lookup"><span data-stu-id="06a4c-488">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="06a4c-489">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-489">Provides localization support.</span></span> | <span data-ttu-id="06a4c-490">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-490">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="06a4c-491">终结点路由</span><span class="sxs-lookup"><span data-stu-id="06a4c-491">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="06a4c-492">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="06a4c-492">Defines and constrains request routes.</span></span> | <span data-ttu-id="06a4c-493">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-493">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="06a4c-494">会话</span><span class="sxs-lookup"><span data-stu-id="06a4c-494">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="06a4c-495">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-495">Provides support for managing user sessions.</span></span> | <span data-ttu-id="06a4c-496">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-496">Before components that require Session.</span></span> |
| [<span data-ttu-id="06a4c-497">静态文件</span><span class="sxs-lookup"><span data-stu-id="06a4c-497">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="06a4c-498">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-498">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="06a4c-499">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="06a4c-499">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="06a4c-500">URL 重写</span><span class="sxs-lookup"><span data-stu-id="06a4c-500">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="06a4c-501">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="06a4c-501">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="06a4c-502">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-502">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="06a4c-503">WebSockets</span><span class="sxs-lookup"><span data-stu-id="06a4c-503">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="06a4c-504">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="06a4c-504">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="06a4c-505">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="06a4c-505">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="06a4c-506">其他资源</span><span class="sxs-lookup"><span data-stu-id="06a4c-506">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
