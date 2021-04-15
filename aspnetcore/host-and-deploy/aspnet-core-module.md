---
title: ASP.NET Core 模块
author: rick-anderson
description: 了解用于通过 IIS 托管 ASP.NET Core 应用的 ASP.NET Core 模块。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: fd9c5bf94385d76aed22a09bd4cd248f1c1dcb6e
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957673"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="b7604-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-103">ASP.NET Core Module</span></span>

<span data-ttu-id="b7604-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti) 和 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="b7604-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b7604-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，能让 ASP.NET Core 应用程序通过 IIS 运行。</span><span class="sxs-lookup"><span data-stu-id="b7604-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="b7604-106">使用以下任一方式通过 IIS 运行 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="b7604-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="b7604-107">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](xref:host-and-deploy/iis/in-process-hosting)。</span><span class="sxs-lookup"><span data-stu-id="b7604-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="b7604-108">将 Web 请求转发到运行 Kestrel 服务器的后端 ASP.NET Core 应用，称为[进程外托管模型](xref:host-and-deploy/iis/out-of-process-hosting)。</span><span class="sxs-lookup"><span data-stu-id="b7604-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="b7604-109">我们需要对这两类托管模型进行权衡。</span><span class="sxs-lookup"><span data-stu-id="b7604-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="b7604-110">默认情况下使用的是进程内托管模型，因为这样可以得到更好的性能和诊断。</span><span class="sxs-lookup"><span data-stu-id="b7604-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="b7604-111">安装 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="b7604-112">ASP.NET Core 模块随 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/hosting-bundle)中的 .NET Core 运行时一起安装。</span><span class="sxs-lookup"><span data-stu-id="b7604-112">The ASP.NET Core Module is installed with the .NET Core Runtime from the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span> <span data-ttu-id="b7604-113">ASP.NET Core 模块与 .NET 的 LTS 版本向前和向后兼容。</span><span class="sxs-lookup"><span data-stu-id="b7604-113">The ASP.NET Core Module is forward and backward compatible with LTS releases of .NET.</span></span>

[!INCLUDE[](~/includes/announcements.md)]

<span data-ttu-id="b7604-114">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="b7604-114">Download the installer using the following link:</span></span>

[<span data-ttu-id="b7604-115">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="b7604-115">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="b7604-116">有关详细信息（包括安装模块的早期版本），请参阅 <xref:host-and-deploy/iis/hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="b7604-116">For more information, including installing an earlier version of the module, see <xref:host-and-deploy/iis/hosting-bundle>.</span></span>

<span data-ttu-id="b7604-117">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="b7604-117">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b7604-118">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="b7604-118">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="b7604-119">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="b7604-119">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="b7604-120">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="b7604-120">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="b7604-121">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b7604-121">Supported Windows versions:</span></span>

* <span data-ttu-id="b7604-122">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b7604-122">Windows 7 or later</span></span>
* <span data-ttu-id="b7604-123">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b7604-123">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="b7604-124">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-124">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b7604-125">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b7604-125">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="b7604-126">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="b7604-126">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b7604-127">托管模型</span><span class="sxs-lookup"><span data-stu-id="b7604-127">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="b7604-128">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="b7604-128">In-process hosting model</span></span>

<span data-ttu-id="b7604-129">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="b7604-129">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="b7604-130">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="b7604-130">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="b7604-131">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="b7604-131">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b7604-132">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-132">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="b7604-133">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="b7604-133">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="b7604-134">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-134">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="b7604-135">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-135">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="b7604-136">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="b7604-136">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="b7604-137">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="b7604-137">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="b7604-138">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="b7604-138">Use one app pool per app.</span></span>

* <span data-ttu-id="b7604-139">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [`app_offline.htm` 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-139">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b7604-140">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-140">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="b7604-141">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="b7604-141">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="b7604-142">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="b7604-142">Client disconnects are detected.</span></span> <span data-ttu-id="b7604-143">客户端断开连接时，将取消 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记。</span><span class="sxs-lookup"><span data-stu-id="b7604-143">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="b7604-144">在 ASP.NET Core 2.2.1 或更早版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 所启动进程的工作目录而非应用目录（例如，对于 `w3wp.exe`，是 `C:\Windows\System32\inetsrv`）。</span><span class="sxs-lookup"><span data-stu-id="b7604-144">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="b7604-145">对于设置应用的当前目录的示例代码，请参阅 [`CurrentDirectoryHelpers` 类](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="b7604-145">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="b7604-146">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="b7604-146">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="b7604-147">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="b7604-147">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="b7604-148">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="b7604-148">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="b7604-149">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="b7604-149">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="b7604-150">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="b7604-150">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="b7604-151">不支持 [Web 包（单文件）部署](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="b7604-151">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="b7604-152">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="b7604-152">Out-of-process hosting model</span></span>

<span data-ttu-id="b7604-153">若要配置进程外托管应用，请在项目文件 ( `.csproj`) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="b7604-153">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b7604-154">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="b7604-154">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="b7604-155">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="b7604-155">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="b7604-156">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-156">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b7604-157">对于进程外托管，[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 会调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 来进行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-157">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="b7604-158">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-158">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="b7604-159">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-159">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="b7604-160">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="b7604-160">Hosting model changes</span></span>

<span data-ttu-id="b7604-161">如果在 `web.config` 文件中更改了 `hostingModel` 设置（如 [`web.config`web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-161">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="b7604-162">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-162">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="b7604-163">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-163">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="b7604-164">进程名</span><span class="sxs-lookup"><span data-stu-id="b7604-164">Process name</span></span>

<span data-ttu-id="b7604-165">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="b7604-165">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="b7604-166">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="b7604-166">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b7604-167">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="b7604-167">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b7604-168">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="b7604-168">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b7604-169">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-169">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b7604-170">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="b7604-170">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b7604-171">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="b7604-171">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b7604-172">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-172">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b7604-173">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="b7604-173">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="b7604-174">ASP.NET Core 模块与 .NET 的 LTS 版本向前和向后兼容。</span><span class="sxs-lookup"><span data-stu-id="b7604-174">The ASP.NET Core Module is forward and backward compatible with LTS releases of .NET.</span></span>

[!INCLUDE[](~/includes/announcements.md)]

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b7604-175">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="b7604-175">Configuration with web.config</span></span>

<span data-ttu-id="b7604-176">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="b7604-176">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b7604-177">以下 `web.config` 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="b7604-177">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b7604-178">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="b7604-178">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b7604-179">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="b7604-179">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="b7604-180">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-180">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b7604-181">该路径会将 stdout 日志保存到 `LogFiles` 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="b7604-181">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b7604-182">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="b7604-182">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b7604-183">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="b7604-183">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="b7604-184">特性</span><span class="sxs-lookup"><span data-stu-id="b7604-184">Attribute</span></span> | <span data-ttu-id="b7604-185">描述</span><span class="sxs-lookup"><span data-stu-id="b7604-185">Description</span></span> | <span data-ttu-id="b7604-186">默认</span><span class="sxs-lookup"><span data-stu-id="b7604-186">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b7604-187">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-187">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-188">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="b7604-188">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b7604-189">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-189">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-190">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="b7604-190">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b7604-191">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-191">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-192">如果为 true，会将令牌作为每个请求的标头 `'MS-ASPNETCORE-WINAUTHTOKEN'` 转发到在 `%ASPNETCORE_PORT%` 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-192">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="b7604-193">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="b7604-193">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="b7604-194">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-194">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-195">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-195">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="b7604-196">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-196">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-197">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="b7604-197">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b7604-198">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="b7604-198">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="b7604-199">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="b7604-199">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b7604-200">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-200">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b7604-201">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="b7604-201">Default: `1`</span></span><br><span data-ttu-id="b7604-202">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="b7604-202">Min: `1`</span></span><br><span data-ttu-id="b7604-203">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="b7604-203">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="b7604-204">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-204">Required string attribute.</span></span></p><p><span data-ttu-id="b7604-205">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-205">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b7604-206">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-206">Relative paths are supported.</span></span> <span data-ttu-id="b7604-207">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="b7604-207">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b7604-208">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-208">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-209">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="b7604-209">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b7604-210">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-210">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="b7604-211">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="b7604-211">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="b7604-212">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="b7604-212">Default: `10`</span></span><br><span data-ttu-id="b7604-213">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-213">Min: `0`</span></span><br><span data-ttu-id="b7604-214">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="b7604-214">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b7604-215">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-215">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b7604-216">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="b7604-216">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b7604-217">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-217">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="b7604-218">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="b7604-218">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="b7604-219">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-219">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="b7604-220">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="b7604-220">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="b7604-221">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="b7604-221">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="b7604-222">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-222">Default: `00:02:00`</span></span><br><span data-ttu-id="b7604-223">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-223">Min: `00:00:00`</span></span><br><span data-ttu-id="b7604-224">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-224">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b7604-225">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-225">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-226">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="b7604-226">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b7604-227">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="b7604-227">Default: `10`</span></span><br><span data-ttu-id="b7604-228">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-228">Min: `0`</span></span><br><span data-ttu-id="b7604-229">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="b7604-229">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b7604-230">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-230">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-231">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="b7604-231">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b7604-232">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-232">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="b7604-233">进程内托管时：不会重新启动该进程，也不会使用 rapidFailsPerMinute 设置  。</span><span class="sxs-lookup"><span data-stu-id="b7604-233">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="b7604-234">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="b7604-234">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b7604-235">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="b7604-235">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b7604-236">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="b7604-236">Default: `120`</span></span><br><span data-ttu-id="b7604-237">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-237">Min: `0`</span></span><br><span data-ttu-id="b7604-238">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="b7604-238">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b7604-239">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-239">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-240">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-240">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b7604-241">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-241">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-242">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-242">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b7604-243">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="b7604-243">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b7604-244">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-244">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b7604-245">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7604-245">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="b7604-246">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (`.log`) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="b7604-246">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b7604-247">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 `logs` 文件夹中保存为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="b7604-247">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="b7604-248">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="b7604-248">Set environment variables</span></span>

<span data-ttu-id="b7604-249">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-249">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b7604-250">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-250">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="b7604-251">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-251">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="b7604-252">以下示例在 `web.config` 中设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-252">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="b7604-253">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b7604-253">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b7604-254">开发人员可能会暂时在 `web.config` 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="b7604-254">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b7604-255">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-255">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="b7604-256">直接在 `web.config` 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="b7604-256">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="b7604-257">此方法在发布项目时设置 `web.config` 中的环境：</span><span class="sxs-lookup"><span data-stu-id="b7604-257">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="b7604-258">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b7604-258">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="b7604-259">如果在应用的根目录中检测到名为“`app_offline.htm`”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-259">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b7604-260">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-260">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b7604-261">存在 `app_offline.htm` 文件时，ASP.NET Core 模块会通过发送回 `app_offline.htm` 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-261">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="b7604-262">删除 `app_offline.htm` 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="b7604-262">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="b7604-263">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-263">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b7604-264">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-264">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="b7604-265">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="b7604-265">Start-up error page</span></span>

<span data-ttu-id="b7604-266">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="b7604-266">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="b7604-267">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-267">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="b7604-268">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-268">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="b7604-269">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-269">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="b7604-270">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-270">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b7604-271">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="b7604-271">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b7604-272">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="b7604-272">Log creation and redirection</span></span>

<span data-ttu-id="b7604-273">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="b7604-273">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b7604-274">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7604-274">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b7604-275">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="b7604-275">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="b7604-276">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-276">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b7604-277">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="b7604-277">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b7604-278">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="b7604-278">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="b7604-279">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="b7604-279">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b7604-280">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="b7604-280">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b7604-281">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="b7604-281">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b7604-282">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="b7604-282">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b7604-283">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (`.log`) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 `stdout`）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="b7604-283">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="b7604-284">如果 `stdoutLogFile` 路径以 `stdout` 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="b7604-284">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="b7604-285">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="b7604-285">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="b7604-286">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="b7604-286">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="b7604-287">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="b7604-287">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="b7604-288">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="b7604-288">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="b7604-289">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-289">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b7604-290">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-290">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="b7604-291">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="b7604-291">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="b7604-292">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="b7604-292">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="b7604-293">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="b7604-293">Enhanced diagnostic logs</span></span>

<span data-ttu-id="b7604-294">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="b7604-294">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="b7604-295">将 `<handlerSettings>` 元素添加到 `web.config` 中的 `<aspNetCore>` 元素。</span><span class="sxs-lookup"><span data-stu-id="b7604-295">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="b7604-296">将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="b7604-296">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b7604-297">创建日志文件时，路径中的任何文件夹（上述示例中为 `logs`）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="b7604-297">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="b7604-298">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="b7604-298">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="b7604-299">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="b7604-299">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="b7604-300">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="b7604-300">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="b7604-301">错误</span><span class="sxs-lookup"><span data-stu-id="b7604-301">ERROR</span></span>
* <span data-ttu-id="b7604-302">警告</span><span class="sxs-lookup"><span data-stu-id="b7604-302">WARNING</span></span>
* <span data-ttu-id="b7604-303">INFO</span><span class="sxs-lookup"><span data-stu-id="b7604-303">INFO</span></span>
* <span data-ttu-id="b7604-304">TRACE</span><span class="sxs-lookup"><span data-stu-id="b7604-304">TRACE</span></span>

<span data-ttu-id="b7604-305">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="b7604-305">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="b7604-306">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="b7604-306">CONSOLE</span></span>
* <span data-ttu-id="b7604-307">事件日志</span><span class="sxs-lookup"><span data-stu-id="b7604-307">EVENTLOG</span></span>
* <span data-ttu-id="b7604-308">文件</span><span class="sxs-lookup"><span data-stu-id="b7604-308">FILE</span></span>

<span data-ttu-id="b7604-309">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="b7604-309">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="b7604-310">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-310">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="b7604-311">（默认值：`aspnetcore-debug.log`）</span><span class="sxs-lookup"><span data-stu-id="b7604-311">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="b7604-312">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="b7604-312">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="b7604-313">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="b7604-313">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="b7604-314">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="b7604-314">The size of the log isn't limited.</span></span> <span data-ttu-id="b7604-315">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="b7604-315">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="b7604-316">有关 `web.config` 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="b7604-316">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="b7604-317">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="b7604-317">Modify the stack size</span></span>

<span data-ttu-id="b7604-318">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="b7604-318">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="b7604-319">在 `web.config` 中使用 `stackSize` 设置（以字节为单位）配置托管堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="b7604-319">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="b7604-320">默认大小为 1,048,576 字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="b7604-320">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b7604-321">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="b7604-321">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="b7604-322">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="b7604-322">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="b7604-323">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="b7604-323">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b7604-324">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="b7604-324">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b7604-325">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="b7604-325">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b7604-326">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-326">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b7604-327">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-327">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b7604-328">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="b7604-328">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b7604-329">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-329">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b7604-330">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="b7604-330">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b7604-331">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-331">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b7604-332">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-332">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b7604-333">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="b7604-333">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="b7604-334">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 `applicationHost.config` 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-334">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="b7604-335">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="b7604-335">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="b7604-336">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-336">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="b7604-337">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="b7604-337">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b7604-338">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="b7604-338">Run the installer.</span></span>
1. <span data-ttu-id="b7604-339">将已更新的 `applicationHost.config` 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="b7604-339">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="b7604-340">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="b7604-340">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b7604-341">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="b7604-341">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b7604-342">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-342">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b7604-343">在托管系统上，导航到 `%windir%\System32\inetsrv`。</span><span class="sxs-lookup"><span data-stu-id="b7604-343">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="b7604-344">找到 `aspnetcore.dll` 文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-344">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="b7604-345">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="b7604-345">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b7604-346">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="b7604-346">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b7604-347">可在 `C:\Users\%UserName%\AppData\Local\Temp` 中找到模块的托管捆绑安装程序日志。</span><span class="sxs-lookup"><span data-stu-id="b7604-347">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="b7604-348">该文件命名为 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`。</span><span class="sxs-lookup"><span data-stu-id="b7604-348">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b7604-349">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="b7604-349">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b7604-350">模块</span><span class="sxs-lookup"><span data-stu-id="b7604-350">Module</span></span>

<span data-ttu-id="b7604-351">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="b7604-351">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="b7604-352">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="b7604-352">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="b7604-353">架构</span><span class="sxs-lookup"><span data-stu-id="b7604-353">Schema</span></span>

<span data-ttu-id="b7604-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b7604-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="b7604-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b7604-355">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="b7604-356">Configuration</span><span class="sxs-lookup"><span data-stu-id="b7604-356">Configuration</span></span>

<span data-ttu-id="b7604-357">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b7604-357">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="b7604-358">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b7604-358">**IIS Express**</span></span>

* <span data-ttu-id="b7604-359">Visual Studio：`{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="b7604-359">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="b7604-360">iisexpress.exe CLI：`%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="b7604-360">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="b7604-361">通过在 `applicationHost.config` 文件中搜索 `aspnetcore` 可以找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-361">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b7604-362">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="b7604-362">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="b7604-363">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="b7604-363">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="b7604-364">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="b7604-364">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="b7604-365">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b7604-365">Supported Windows versions:</span></span>

* <span data-ttu-id="b7604-366">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b7604-366">Windows 7 or later</span></span>
* <span data-ttu-id="b7604-367">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b7604-367">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b7604-368">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-368">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b7604-369">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b7604-369">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="b7604-370">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="b7604-370">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b7604-371">托管模型</span><span class="sxs-lookup"><span data-stu-id="b7604-371">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="b7604-372">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="b7604-372">In-process hosting model</span></span>

<span data-ttu-id="b7604-373">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="b7604-373">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b7604-374">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="b7604-374">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="b7604-375">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="b7604-375">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="b7604-376">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="b7604-376">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="b7604-377">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="b7604-377">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="b7604-378">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="b7604-378">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b7604-379">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-379">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="b7604-380">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="b7604-380">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="b7604-381">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-381">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="b7604-382">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-382">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="b7604-383">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="b7604-383">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="b7604-384">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="b7604-384">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="b7604-385">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="b7604-385">Use one app pool per app.</span></span>

* <span data-ttu-id="b7604-386">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-386">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b7604-387">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-387">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="b7604-388">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="b7604-388">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="b7604-389">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="b7604-389">Client disconnects are detected.</span></span> <span data-ttu-id="b7604-390">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="b7604-390">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="b7604-391">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv ）。</span><span class="sxs-lookup"><span data-stu-id="b7604-391">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="b7604-392">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="b7604-392">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="b7604-393">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="b7604-393">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="b7604-394">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="b7604-394">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="b7604-395">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="b7604-395">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="b7604-396">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="b7604-396">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="b7604-397">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="b7604-397">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="b7604-398">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="b7604-398">Out-of-process hosting model</span></span>

<span data-ttu-id="b7604-399">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="b7604-399">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="b7604-400">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-400">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="b7604-401">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="b7604-401">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="b7604-402">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="b7604-402">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="b7604-403">值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="b7604-403">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="b7604-404">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-404">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="b7604-405">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-405">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="b7604-406">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-406">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="b7604-407">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-407">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="b7604-408">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="b7604-408">Hosting model changes</span></span>

<span data-ttu-id="b7604-409">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-409">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="b7604-410">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-410">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="b7604-411">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-411">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="b7604-412">进程名</span><span class="sxs-lookup"><span data-stu-id="b7604-412">Process name</span></span>

<span data-ttu-id="b7604-413">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="b7604-413">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="b7604-414">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="b7604-414">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b7604-415">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="b7604-415">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b7604-416">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="b7604-416">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b7604-417">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-417">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b7604-418">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="b7604-418">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b7604-419">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="b7604-419">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b7604-420">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-420">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b7604-421">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="b7604-421">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="b7604-422">ASP.NET Core 模块与 .NET 的 LTS 版本向前和向后兼容。</span><span class="sxs-lookup"><span data-stu-id="b7604-422">The ASP.NET Core Module is forward and backward compatible with LTS releases of .NET.</span></span>

[!INCLUDE[](~/includes/announcements.md)]

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b7604-423">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="b7604-423">Configuration with web.config</span></span>

<span data-ttu-id="b7604-424">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="b7604-424">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b7604-425">以下 *web.config* 文件发布用于 [依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="b7604-425">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b7604-426">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="b7604-426">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="b7604-427">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="b7604-427">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="b7604-428">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-428">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b7604-429">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="b7604-429">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b7604-430">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="b7604-430">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b7604-431">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="b7604-431">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="b7604-432">特性</span><span class="sxs-lookup"><span data-stu-id="b7604-432">Attribute</span></span> | <span data-ttu-id="b7604-433">描述</span><span class="sxs-lookup"><span data-stu-id="b7604-433">Description</span></span> | <span data-ttu-id="b7604-434">默认</span><span class="sxs-lookup"><span data-stu-id="b7604-434">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b7604-435">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-435">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-436">`processPath` 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="b7604-436">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b7604-437">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-437">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-438">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="b7604-438">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b7604-439">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-439">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-440">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-440">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b7604-441">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="b7604-441">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="b7604-442">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-442">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-443">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-443">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="b7604-444">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-445">指定每个应用均可启动的 `processPath` 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="b7604-445">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b7604-446">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="b7604-446">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="b7604-447">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="b7604-447">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b7604-448">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-448">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b7604-449">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="b7604-449">Default: `1`</span></span><br><span data-ttu-id="b7604-450">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="b7604-450">Min: `1`</span></span><br><span data-ttu-id="b7604-451">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="b7604-451">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="b7604-452">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-452">Required string attribute.</span></span></p><p><span data-ttu-id="b7604-453">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-453">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b7604-454">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-454">Relative paths are supported.</span></span> <span data-ttu-id="b7604-455">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="b7604-455">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b7604-456">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-456">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-457">指定允许 `processPath` 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="b7604-457">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="b7604-458">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-458">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="b7604-459">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="b7604-459">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="b7604-460">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="b7604-460">Default: `10`</span></span><br><span data-ttu-id="b7604-461">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-461">Min: `0`</span></span><br><span data-ttu-id="b7604-462">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="b7604-462">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b7604-463">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-463">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b7604-464">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="b7604-464">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b7604-465">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-465">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="b7604-466">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="b7604-466">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="b7604-467">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-467">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="b7604-468">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="b7604-468">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="b7604-469">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="b7604-469">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="b7604-470">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-470">Default: `00:02:00`</span></span><br><span data-ttu-id="b7604-471">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-471">Min: `00:00:00`</span></span><br><span data-ttu-id="b7604-472">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-472">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b7604-473">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-474">检测到 `app_offline.htm` 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="b7604-474">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="b7604-475">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="b7604-475">Default: `10`</span></span><br><span data-ttu-id="b7604-476">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-476">Min: `0`</span></span><br><span data-ttu-id="b7604-477">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="b7604-477">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b7604-478">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-478">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-479">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="b7604-479">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b7604-480">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-480">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="b7604-481">进程内托管时：不会重新启动该进程，也不会使用 `rapidFailsPerMinute` 设置 。</span><span class="sxs-lookup"><span data-stu-id="b7604-481">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="b7604-482">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 `rapidFailsPerMinute` 次。</span><span class="sxs-lookup"><span data-stu-id="b7604-482">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b7604-483">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="b7604-483">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b7604-484">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="b7604-484">Default: `120`</span></span><br><span data-ttu-id="b7604-485">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-485">Min: `0`</span></span><br><span data-ttu-id="b7604-486">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="b7604-486">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b7604-487">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-487">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-488">如果为 true，`processPath` 中指定的进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件  。</span><span class="sxs-lookup"><span data-stu-id="b7604-488">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b7604-489">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-489">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-490">指定在其中记录 `processPath` 中指定进程的 `stdout` 和 `stderr` 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-490">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="b7604-491">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="b7604-491">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b7604-492">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-492">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b7604-493">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7604-493">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="b7604-494">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (`.log`) 添加到 `stdoutLogFile` 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="b7604-494">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="b7604-495">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 `logs` 文件夹中保存为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="b7604-495">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="b7604-496">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="b7604-496">Setting environment variables</span></span>

<span data-ttu-id="b7604-497">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-497">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b7604-498">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-498">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="b7604-499">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-499">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="b7604-500">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-500">The following example sets two environment variables.</span></span> <span data-ttu-id="b7604-501">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b7604-501">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b7604-502">开发人员可能会暂时在 `web.config` 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="b7604-502">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b7604-503">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-503">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="b7604-504">直接在 `web.config` 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="b7604-504">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="b7604-505">此方法在发布项目时设置 `web.config` 中的环境：</span><span class="sxs-lookup"><span data-stu-id="b7604-505">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="b7604-506">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b7604-506">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="b7604-507">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="b7604-507">app_offline.htm</span></span>

<span data-ttu-id="b7604-508">如果在应用的根目录中检测到名为“`app_offline.htm`”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-508">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b7604-509">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-509">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b7604-510">存在 `app_offline.htm` 文件时，ASP.NET Core 模块会通过发送回 `app_offline.htm` 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-510">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="b7604-511">删除 `app_offline.htm` 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="b7604-511">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="b7604-512">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-512">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="b7604-513">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="b7604-513">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="b7604-514">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="b7604-514">Start-up error page</span></span>

<span data-ttu-id="b7604-515">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="b7604-515">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="b7604-516">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-516">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="b7604-517">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-517">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="b7604-518">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-518">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="b7604-519">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-519">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b7604-520">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="b7604-520">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b7604-521">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="b7604-521">Log creation and redirection</span></span>

<span data-ttu-id="b7604-522">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="b7604-522">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b7604-523">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7604-523">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b7604-524">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="b7604-524">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="b7604-525">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-525">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b7604-526">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="b7604-526">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b7604-527">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="b7604-527">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="b7604-528">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="b7604-528">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b7604-529">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="b7604-529">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b7604-530">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="b7604-530">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b7604-531">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="b7604-531">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b7604-532">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (`.log`) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 `stdout`）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="b7604-532">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="b7604-533">如果 `stdoutLogFile` 路径以 `stdout` 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="b7604-533">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="b7604-534">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="b7604-534">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="b7604-535">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="b7604-535">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="b7604-536">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="b7604-536">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="b7604-537">确认应用池用户标识是否已获得写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="b7604-537">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="b7604-538">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-538">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b7604-539">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-539">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="b7604-540">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="b7604-540">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="b7604-541">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="b7604-541">Enhanced diagnostic logs</span></span>

<span data-ttu-id="b7604-542">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="b7604-542">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="b7604-543">将 `<handlerSettings>` 元素添加到 `web.config` 中的 `<aspNetCore>` 元素。</span><span class="sxs-lookup"><span data-stu-id="b7604-543">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="b7604-544">将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="b7604-544">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="b7604-545">提供给 `<handlerSetting>` 值（上述示例中为 `logs`）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="b7604-545">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="b7604-546">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="b7604-546">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="b7604-547">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="b7604-547">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="b7604-548">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="b7604-548">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="b7604-549">错误</span><span class="sxs-lookup"><span data-stu-id="b7604-549">ERROR</span></span>
* <span data-ttu-id="b7604-550">警告</span><span class="sxs-lookup"><span data-stu-id="b7604-550">WARNING</span></span>
* <span data-ttu-id="b7604-551">INFO</span><span class="sxs-lookup"><span data-stu-id="b7604-551">INFO</span></span>
* <span data-ttu-id="b7604-552">TRACE</span><span class="sxs-lookup"><span data-stu-id="b7604-552">TRACE</span></span>

<span data-ttu-id="b7604-553">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="b7604-553">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="b7604-554">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="b7604-554">CONSOLE</span></span>
* <span data-ttu-id="b7604-555">事件日志</span><span class="sxs-lookup"><span data-stu-id="b7604-555">EVENTLOG</span></span>
* <span data-ttu-id="b7604-556">文件</span><span class="sxs-lookup"><span data-stu-id="b7604-556">FILE</span></span>

<span data-ttu-id="b7604-557">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="b7604-557">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="b7604-558">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-558">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="b7604-559">（默认值：`aspnetcore-debug.log`）</span><span class="sxs-lookup"><span data-stu-id="b7604-559">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="b7604-560">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="b7604-560">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="b7604-561">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="b7604-561">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="b7604-562">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="b7604-562">The size of the log isn't limited.</span></span> <span data-ttu-id="b7604-563">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="b7604-563">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="b7604-564">有关 `web.config` 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="b7604-564">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b7604-565">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="b7604-565">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="b7604-566">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="b7604-566">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="b7604-567">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="b7604-567">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b7604-568">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="b7604-568">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b7604-569">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="b7604-569">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b7604-570">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-570">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b7604-571">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-571">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b7604-572">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="b7604-572">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b7604-573">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-573">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b7604-574">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="b7604-574">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b7604-575">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-575">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b7604-576">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-576">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b7604-577">ASP.NET Core 模块安装程序使用 `TrustedInstaller` 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="b7604-577">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="b7604-578">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 `applicationHost.config` 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-578">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="b7604-579">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="b7604-579">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="b7604-580">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-580">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="b7604-581">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="b7604-581">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b7604-582">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="b7604-582">Run the installer.</span></span>
1. <span data-ttu-id="b7604-583">将已更新的 `applicationHost.config` 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="b7604-583">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="b7604-584">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="b7604-584">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b7604-585">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="b7604-585">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b7604-586">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-586">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b7604-587">在托管系统上，导航到 `%windir%\System32\inetsrv`。</span><span class="sxs-lookup"><span data-stu-id="b7604-587">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="b7604-588">找到 `aspnetcore.dll` 文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-588">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="b7604-589">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="b7604-589">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b7604-590">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="b7604-590">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b7604-591">可在 `C:\\Users\\%UserName%\\AppData\\Local\\Temp` 中找到模块的托管捆绑安装程序日志。</span><span class="sxs-lookup"><span data-stu-id="b7604-591">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="b7604-592">该文件的名称为 `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`，其中占位符 `{TIMESTAMP}` 为时间戳。</span><span class="sxs-lookup"><span data-stu-id="b7604-592">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b7604-593">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="b7604-593">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b7604-594">模块</span><span class="sxs-lookup"><span data-stu-id="b7604-594">Module</span></span>

<span data-ttu-id="b7604-595">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="b7604-595">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="b7604-596">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="b7604-596">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="b7604-597">架构</span><span class="sxs-lookup"><span data-stu-id="b7604-597">Schema</span></span>

<span data-ttu-id="b7604-598">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b7604-598">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="b7604-599">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b7604-599">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="b7604-600">Configuration</span><span class="sxs-lookup"><span data-stu-id="b7604-600">Configuration</span></span>

<span data-ttu-id="b7604-601">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b7604-601">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="b7604-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b7604-602">**IIS Express**</span></span>

* <span data-ttu-id="b7604-603">Visual Studio：`{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="b7604-603">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="b7604-604">iisexpress.exe CLI：`%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="b7604-604">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="b7604-605">通过在 `applicationHost.config` 文件中搜索 `aspnetcore` 可以找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-605">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b7604-606">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b7604-606">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="b7604-607">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b7604-607">Supported Windows versions:</span></span>

* <span data-ttu-id="b7604-608">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b7604-608">Windows 7 or later</span></span>
* <span data-ttu-id="b7604-609">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b7604-609">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b7604-610">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b7604-610">The module only works with Kestrel.</span></span> <span data-ttu-id="b7604-611">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="b7604-611">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="b7604-612">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="b7604-612">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="b7604-613">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="b7604-613">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="b7604-614">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="b7604-614">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b7604-615">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="b7604-615">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="b7604-617">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="b7604-617">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b7604-618">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="b7604-618">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b7604-619">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b7604-619">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="b7604-620">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="b7604-620">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="b7604-621">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-621">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="b7604-622">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="b7604-622">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="b7604-623">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="b7604-623">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b7604-624">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="b7604-624">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b7604-625">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="b7604-625">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="b7604-626">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="b7604-626">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="b7604-627">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="b7604-627">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="b7604-628">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="b7604-628">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="b7604-629">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="b7604-629">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="b7604-630">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-630">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="b7604-631">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="b7604-631">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="b7604-632">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="b7604-632">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="b7604-633">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-633">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="b7604-634">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="b7604-634">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="b7604-635">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="b7604-635">Configuration with web.config</span></span>

<span data-ttu-id="b7604-636">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="b7604-636">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="b7604-637">以下 *web.config* 文件发布用于 [依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="b7604-637">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="b7604-638">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="b7604-638">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="b7604-639">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-639">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b7604-640">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="b7604-640">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="b7604-641">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="b7604-641">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="b7604-642">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="b7604-642">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="b7604-643">特性</span><span class="sxs-lookup"><span data-stu-id="b7604-643">Attribute</span></span> | <span data-ttu-id="b7604-644">描述</span><span class="sxs-lookup"><span data-stu-id="b7604-644">Description</span></span> | <span data-ttu-id="b7604-645">默认</span><span class="sxs-lookup"><span data-stu-id="b7604-645">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="b7604-646">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-646">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-647">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="b7604-647">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="b7604-648">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-648">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-649">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="b7604-649">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="b7604-650">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-650">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-651">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-651">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="b7604-652">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="b7604-652">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="b7604-653">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-653">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-654">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="b7604-654">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="b7604-655">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="b7604-655">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="b7604-656">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-656">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="b7604-657">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="b7604-657">Default: `1`</span></span><br><span data-ttu-id="b7604-658">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="b7604-658">Min: `1`</span></span><br><span data-ttu-id="b7604-659">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="b7604-659">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="b7604-660">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-660">Required string attribute.</span></span></p><p><span data-ttu-id="b7604-661">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-661">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="b7604-662">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-662">Relative paths are supported.</span></span> <span data-ttu-id="b7604-663">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="b7604-663">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="b7604-664">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-664">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-665">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="b7604-665">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="b7604-666">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-666">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="b7604-667">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="b7604-667">Default: `10`</span></span><br><span data-ttu-id="b7604-668">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-668">Min: `0`</span></span><br><span data-ttu-id="b7604-669">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="b7604-669">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="b7604-670">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-670">Optional timespan attribute.</span></span></p><p><span data-ttu-id="b7604-671">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="b7604-671">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="b7604-672">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-672">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="b7604-673">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-673">Default: `00:02:00`</span></span><br><span data-ttu-id="b7604-674">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-674">Min: `00:00:00`</span></span><br><span data-ttu-id="b7604-675">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="b7604-675">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="b7604-676">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-676">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-677">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="b7604-677">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="b7604-678">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="b7604-678">Default: `10`</span></span><br><span data-ttu-id="b7604-679">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-679">Min: `0`</span></span><br><span data-ttu-id="b7604-680">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="b7604-680">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="b7604-681">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-681">Optional integer attribute.</span></span></p><p><span data-ttu-id="b7604-682">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="b7604-682">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="b7604-683">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-683">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="b7604-684">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="b7604-684">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="b7604-685">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="b7604-685">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="b7604-686">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="b7604-686">Default: `120`</span></span><br><span data-ttu-id="b7604-687">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="b7604-687">Min: `0`</span></span><br><span data-ttu-id="b7604-688">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="b7604-688">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="b7604-689">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-689">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="b7604-690">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-690">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="b7604-691">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-691">Optional string attribute.</span></span></p><p><span data-ttu-id="b7604-692">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-692">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="b7604-693">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="b7604-693">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="b7604-694">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-694">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="b7604-695">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-695">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="b7604-696">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="b7604-696">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="b7604-697">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="b7604-697">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="b7604-698">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="b7604-698">Setting environment variables</span></span>

<span data-ttu-id="b7604-699">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-699">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="b7604-700">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-700">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="b7604-701">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="b7604-701">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="b7604-702">如果在 web.config 文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config 文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="b7604-702">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="b7604-703">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-703">The following example sets two environment variables.</span></span> <span data-ttu-id="b7604-704">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b7604-704">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="b7604-705">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="b7604-705">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="b7604-706">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="b7604-706">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="b7604-707">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b7604-707">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="b7604-708">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="b7604-708">app_offline.htm</span></span>

<span data-ttu-id="b7604-709">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-709">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="b7604-710">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-710">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="b7604-711">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-711">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="b7604-712">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="b7604-712">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="b7604-713">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="b7604-713">Start-up error page</span></span>

<span data-ttu-id="b7604-714">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="b7604-714">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="b7604-715">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="b7604-715">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="b7604-716">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="b7604-716">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="b7604-717">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="b7604-717">Log creation and redirection</span></span>

<span data-ttu-id="b7604-718">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="b7604-718">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="b7604-719">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7604-719">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="b7604-720">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="b7604-720">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="b7604-721">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="b7604-721">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="b7604-722">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="b7604-722">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="b7604-723">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="b7604-723">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="b7604-724">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="b7604-724">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="b7604-725">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="b7604-725">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="b7604-726">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="b7604-726">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="b7604-727">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="b7604-727">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="b7604-728">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="b7604-728">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="b7604-729">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="b7604-729">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="b7604-730">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="b7604-730">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="b7604-731">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="b7604-731">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="b7604-732">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="b7604-732">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="b7604-733">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="b7604-733">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="b7604-734">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="b7604-734">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="b7604-735">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="b7604-735">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="b7604-736">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="b7604-736">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="b7604-737">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="b7604-737">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="b7604-738">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="b7604-738">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="b7604-739">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="b7604-739">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="b7604-740">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-740">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="b7604-741">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="b7604-741">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="b7604-742">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="b7604-742">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="b7604-743">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-743">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="b7604-744">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="b7604-744">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="b7604-745">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="b7604-745">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="b7604-746">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="b7604-746">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="b7604-747">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="b7604-747">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="b7604-748">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="b7604-748">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="b7604-749">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="b7604-749">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="b7604-750">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="b7604-750">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="b7604-751">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="b7604-751">Run the installer.</span></span>
1. <span data-ttu-id="b7604-752">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="b7604-752">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="b7604-753">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="b7604-753">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="b7604-754">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="b7604-754">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="b7604-755">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b7604-755">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="b7604-756">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="b7604-756">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="b7604-757">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-757">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="b7604-758">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="b7604-758">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="b7604-759">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="b7604-759">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="b7604-760">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="b7604-760">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="b7604-761">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="b7604-761">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="b7604-762">模块</span><span class="sxs-lookup"><span data-stu-id="b7604-762">Module</span></span>

<span data-ttu-id="b7604-763">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="b7604-763">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="b7604-764">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b7604-764">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="b7604-765">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b7604-765">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="b7604-766">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="b7604-766">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="b7604-767">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b7604-767">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="b7604-768">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="b7604-768">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="b7604-769">架构</span><span class="sxs-lookup"><span data-stu-id="b7604-769">Schema</span></span>

<span data-ttu-id="b7604-770">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b7604-770">**IIS**</span></span>

* <span data-ttu-id="b7604-771">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="b7604-771">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="b7604-772">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b7604-772">**IIS Express**</span></span>

* <span data-ttu-id="b7604-773">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="b7604-773">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="b7604-774">Configuration</span><span class="sxs-lookup"><span data-stu-id="b7604-774">Configuration</span></span>

<span data-ttu-id="b7604-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="b7604-775">**IIS**</span></span>

* <span data-ttu-id="b7604-776">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="b7604-776">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="b7604-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="b7604-777">**IIS Express**</span></span>

* <span data-ttu-id="b7604-778">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="b7604-778">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="b7604-779">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="b7604-779">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="b7604-780">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="b7604-780">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b7604-781">其他资源</span><span class="sxs-lookup"><span data-stu-id="b7604-781">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="b7604-782">[ASP.NET Core 模块引用源[默认分支（主）]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2)：使用“分支”下拉列表选择特定版本（例如，`release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="b7604-782">[ASP.NET Core Module reference source [default branch (main)]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
