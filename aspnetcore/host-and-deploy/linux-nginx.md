---
title: 使用 Nginx 在 Linux 上托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Ubuntu 16.04 上将 Nginx 设置为反向代理，从而将 HTTP 流量转发到在 Kestrel 上运行的 ASP.NET Core Web 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 81bb2d176fe47c673f2f340b97bb2c25c2c640ea
ms.sourcegitcommit: 8f4313c762a0b7c30e5ce328b3afe146838f53d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107591479"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="75a5e-103">使用 Nginx 在 Linux 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75a5e-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="75a5e-104">作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="75a5e-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="75a5e-105">本指南介绍如何在 Ubuntu 16.04 服务器上设置生产就绪 ASP.NET Core 环境。</span><span class="sxs-lookup"><span data-stu-id="75a5e-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="75a5e-106">这些说明可能适用于较新版本的 Ubuntu，但尚未使用较新版本进行测试。</span><span class="sxs-lookup"><span data-stu-id="75a5e-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="75a5e-107">有关 ASP.NET Core 支持的其他 Linux 分配的信息，请参阅 [Linux 上 .NET Core 的先决条件](/dotnet/core/linux-prerequisites)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="75a5e-108">对于 Ubuntu 14.04，建议使用 `supervisord` 作为监视 Kestrel 进程的解决方案。</span><span class="sxs-lookup"><span data-stu-id="75a5e-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="75a5e-109">`systemd` 不适用于 Ubuntu 14.04。</span><span class="sxs-lookup"><span data-stu-id="75a5e-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="75a5e-110">有关 Ubuntu 14.04 的说明，请参阅[本主题的以前版本](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="75a5e-111">本指南：</span><span class="sxs-lookup"><span data-stu-id="75a5e-111">This guide:</span></span>

* <span data-ttu-id="75a5e-112">将现有 ASP.NET Core 应用置于反向代理服务器后方。</span><span class="sxs-lookup"><span data-stu-id="75a5e-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="75a5e-113">设置反向代理服务器，以便将请求转发到 Kestrel Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="75a5e-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="75a5e-114">确保 Web 应用在启动时作为守护程序运行。</span><span class="sxs-lookup"><span data-stu-id="75a5e-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="75a5e-115">配置进程管理工具以帮助重新启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="75a5e-116">先决条件</span><span class="sxs-lookup"><span data-stu-id="75a5e-116">Prerequisites</span></span>

* <span data-ttu-id="75a5e-117">使用具有 sudo 特权的标准用户帐户访问 Ubuntu 16.04 服务器。</span><span class="sxs-lookup"><span data-stu-id="75a5e-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="75a5e-118">服务器上安装了最新的非预览版 [.NET 运行时](/dotnet/core/install/linux)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-118">The latest non-preview [.NET runtime installed](/dotnet/core/install/linux) on the server.</span></span>
* <span data-ttu-id="75a5e-119">一个现有 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-119">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="75a5e-120">升级共享框架后，可随时重启服务器托管的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-120">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="75a5e-121">通过应用发布和复制</span><span class="sxs-lookup"><span data-stu-id="75a5e-121">Publish and copy over the app</span></span>

<span data-ttu-id="75a5e-122">配置应用以进行[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-122">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="75a5e-123">如果应用在本地运行，且未配置为建立安全连接 (HTTPS)，则采用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="75a5e-123">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="75a5e-124">配置应用，以处理安全的本地连接。</span><span class="sxs-lookup"><span data-stu-id="75a5e-124">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="75a5e-125">有关详细信息，请参阅 [HTTPS 配置](#https-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="75a5e-125">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="75a5e-126">从 `Properties/launchSettings.json` 文件的 `applicationUrl` 属性中删除 `https://localhost:5001`（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="75a5e-126">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="75a5e-127">在开发环境中运行 [dotnet publish](/dotnet/core/tools/dotnet-publish)，将应用打包到可在服务器上运行的目录中（例如 `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`，其中 `{TARGET FRAMEWORK MONIKER}` 占位符表示目标框架名字对象/TFM）：</span><span class="sxs-lookup"><span data-stu-id="75a5e-127">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="75a5e-128">如果不希望维护服务器上的 .NET Core 运行时，还可将应用发布为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-128">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="75a5e-129">使用集成到组织工作流的工具（例如 `SCP`、`SFTP`）将 ASP.NET Core 应用复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="75a5e-129">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="75a5e-130">通常可在 `var` 目录（例如 `var/www/helloapp`）下找到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-130">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="75a5e-131">在生产部署方案中，持续集成工作流会执行发布应用并将资产复制到服务器的工作。</span><span class="sxs-lookup"><span data-stu-id="75a5e-131">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="75a5e-132">测试应用：</span><span class="sxs-lookup"><span data-stu-id="75a5e-132">Test the app:</span></span>

1. <span data-ttu-id="75a5e-133">在命令行中运行应用：`dotnet <app_assembly>.dll`。</span><span class="sxs-lookup"><span data-stu-id="75a5e-133">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="75a5e-134">在浏览器中，导航到 `http://<serveraddress>:<port>` 以确认应用在 Linux 本地正常运行。</span><span class="sxs-lookup"><span data-stu-id="75a5e-134">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="75a5e-135">配置反向代理服务器</span><span class="sxs-lookup"><span data-stu-id="75a5e-135">Configure a reverse proxy server</span></span>

<span data-ttu-id="75a5e-136">反向代理是为动态 Web 应用提供服务的常见设置。</span><span class="sxs-lookup"><span data-stu-id="75a5e-136">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="75a5e-137">反向代理终止 HTTP 请求，并将其转发到 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-137">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="75a5e-138">使用反向代理服务器</span><span class="sxs-lookup"><span data-stu-id="75a5e-138">Use a reverse proxy server</span></span>

<span data-ttu-id="75a5e-139">Kestrel 非常适合从 ASP.NET Core 提供动态内容。</span><span class="sxs-lookup"><span data-stu-id="75a5e-139">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="75a5e-140">但是，Web 服务功能不像服务器（如 IIS、Apache 或 Nginx）那样功能丰富。</span><span class="sxs-lookup"><span data-stu-id="75a5e-140">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="75a5e-141">反向代理服务器可以卸载 HTTP 服务器的工作负载，如提供静态内容、缓存请求、压缩请求和 HTTPS 终端。</span><span class="sxs-lookup"><span data-stu-id="75a5e-141">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="75a5e-142">反向代理服务器可能驻留在专用计算机上，也可能与 HTTP 服务器一起部署。</span><span class="sxs-lookup"><span data-stu-id="75a5e-142">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="75a5e-143">鉴于此指南的目的，使用 Nginx 的单个实例。</span><span class="sxs-lookup"><span data-stu-id="75a5e-143">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="75a5e-144">它与 HTTP 服务器一起运行在同一服务器上。</span><span class="sxs-lookup"><span data-stu-id="75a5e-144">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="75a5e-145">根据要求，可以选择不同的设置。</span><span class="sxs-lookup"><span data-stu-id="75a5e-145">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="75a5e-146">由于请求通过反向代理转发，因此使用 [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) 包中的[转接头中间件](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-146">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="75a5e-147">此中间件使用 `X-Forwarded-Proto` 标头来更新 `Request.Scheme`，使重定向 URI 和其他安全策略能够正常工作。</span><span class="sxs-lookup"><span data-stu-id="75a5e-147">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="75a5e-148">调用其他中间件之前，请先在 `Startup.Configure` 的基础上调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="75a5e-148">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="75a5e-149">配置中间件以转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头：</span><span class="sxs-lookup"><span data-stu-id="75a5e-149">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="75a5e-150">如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，则要转接的默认标头为 `None`。</span><span class="sxs-lookup"><span data-stu-id="75a5e-150">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="75a5e-151">默认情况下，在环回地址 (`127.0.0.0/8`, `[::1]`)（包括标准 localhost 地址 (`127.0.0.1`)）上运行的代理受信任。</span><span class="sxs-lookup"><span data-stu-id="75a5e-151">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="75a5e-152">如果组织内的其他受信任代理或网络处理 Internet 与 Web 服务器之间的请求，请使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 将其添加到 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> 的列表。</span><span class="sxs-lookup"><span data-stu-id="75a5e-152">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="75a5e-153">以下示例会将 IP 地址为 10.0.0.100 的受信任代理服务器添加到 `Startup.ConfigureServices` 中的转接头中间件 `KnownProxies`：</span><span class="sxs-lookup"><span data-stu-id="75a5e-153">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="75a5e-154">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="75a5e-154">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="75a5e-155">安装 Nginx</span><span class="sxs-lookup"><span data-stu-id="75a5e-155">Install Nginx</span></span>

<span data-ttu-id="75a5e-156">使用 `apt-get` 安装 Nginx。</span><span class="sxs-lookup"><span data-stu-id="75a5e-156">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="75a5e-157">安装程序将创建一个 `systemd` init 脚本，该脚本运行 Nginx，作为系统启动时的守护程序。</span><span class="sxs-lookup"><span data-stu-id="75a5e-157">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="75a5e-158">按照以下网站上的 Ubuntu 安装说明操作：[Nginx：官方 Debian/Ubuntu 包](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-158">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="75a5e-159">如果需要可选 Nginx 模块，则可能需要从源代码生成 Nginx。</span><span class="sxs-lookup"><span data-stu-id="75a5e-159">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="75a5e-160">因为是首次安装 Nginx，通过运行以下命令显式启动：</span><span class="sxs-lookup"><span data-stu-id="75a5e-160">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="75a5e-161">确认浏览器显示 Nginx 的默认登陆页。</span><span class="sxs-lookup"><span data-stu-id="75a5e-161">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="75a5e-162">可在 `http://<server_IP_address>/index.nginx-debian.html` 访问登陆页面。</span><span class="sxs-lookup"><span data-stu-id="75a5e-162">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="75a5e-163">配置 Nginx</span><span class="sxs-lookup"><span data-stu-id="75a5e-163">Configure Nginx</span></span>

<span data-ttu-id="75a5e-164">若要将 Nginx 配置为反向代理以将 HTTP 请求转发到 ASP.NET Core 应用程序，请修改 `/etc/nginx/sites-available/default`。</span><span class="sxs-lookup"><span data-stu-id="75a5e-164">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="75a5e-165">在文本编辑器中打开它，并将内容替换为以下代码片段：</span><span class="sxs-lookup"><span data-stu-id="75a5e-165">Open it in a text editor, and replace the contents with the following snippet:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://127.0.0.1:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="75a5e-166">如果应用是 SignalR 或 Blazor Server 应用，请分别参阅 <xref:signalr/scale#linux-with-nginx> 和 <xref:blazor/host-and-deploy/server#linux-with-nginx> 以了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="75a5e-166">If the app is a SignalR or Blazor Server app, see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="75a5e-167">当没有匹配的 `server_name` 时，Nginx 使用默认服务器。</span><span class="sxs-lookup"><span data-stu-id="75a5e-167">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="75a5e-168">如果没有定义默认服务器，则配置文件中的第一台服务器是默认服务器。</span><span class="sxs-lookup"><span data-stu-id="75a5e-168">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="75a5e-169">最佳做法是，添加一个特定的默认服务器，它会在配置文件中返回状态代码 444。</span><span class="sxs-lookup"><span data-stu-id="75a5e-169">As a best practice, add a specific default server that returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="75a5e-170">默认的服务器配置示例是：</span><span class="sxs-lookup"><span data-stu-id="75a5e-170">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="75a5e-171">使用上述配置文件和默认服务器，Nginx 接受主机标头 `example.com` 或 `*.example.com` 端口 80 上的公共流量。</span><span class="sxs-lookup"><span data-stu-id="75a5e-171">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="75a5e-172">与这些主机不匹配的请求不会转接到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="75a5e-172">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="75a5e-173">Nginx 将匹配的请求转接到 `http://127.0.0.1:5000` 中的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="75a5e-173">Nginx forwards the matching requests to Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="75a5e-174">有关详细信息，请参阅 [nginx 如何处理请求](https://nginx.org/docs/http/request_processing.html)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-174">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="75a5e-175">若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel/endpoints)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-175">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="75a5e-176">使用上述配置文件和默认服务器，Nginx 接受主机标头 `example.com` 或 `*.example.com` 端口 80 上的公共流量。</span><span class="sxs-lookup"><span data-stu-id="75a5e-176">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="75a5e-177">与这些主机不匹配的请求不会转接到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="75a5e-177">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="75a5e-178">Nginx 将匹配的请求转接到 `http://127.0.0.1:5000` 中的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="75a5e-178">Nginx forwards the matching requests to Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="75a5e-179">有关详细信息，请参阅 [nginx 如何处理请求](https://nginx.org/docs/http/request_processing.html)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-179">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="75a5e-180">若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-180">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="75a5e-181">未能指定正确的 [server_name 指令](https://nginx.org/docs/http/server_names.html)会公开应用的安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="75a5e-181">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="75a5e-182">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.example.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="75a5e-182">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="75a5e-183">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-183">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="75a5e-184">完成配置 Nginx 后，运行 `sudo nginx -t` 来验证配置文件的语法。</span><span class="sxs-lookup"><span data-stu-id="75a5e-184">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="75a5e-185">如果配置文件测试成功，可以通过运行 `sudo nginx -s reload` 强制 Nginx 选取更改。</span><span class="sxs-lookup"><span data-stu-id="75a5e-185">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="75a5e-186">要直接在服务器上运行应用：</span><span class="sxs-lookup"><span data-stu-id="75a5e-186">To directly run the app on the server:</span></span>

1. <span data-ttu-id="75a5e-187">请导航到应用目录。</span><span class="sxs-lookup"><span data-stu-id="75a5e-187">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="75a5e-188">运行应用：`dotnet <app_assembly.dll>`，其中 `app_assembly.dll` 是应用的程序集文件名。</span><span class="sxs-lookup"><span data-stu-id="75a5e-188">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="75a5e-189">如果应用在服务器上运行，但无法通过 Internet 响应，请检查服务器的防火墙，确认端口 80 已打开。</span><span class="sxs-lookup"><span data-stu-id="75a5e-189">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm port 80 is open.</span></span> <span data-ttu-id="75a5e-190">如果使用 Azure Ubuntu VM，请添加启用入站端口 80 流量的网络安全组 (NSG) 规则。</span><span class="sxs-lookup"><span data-stu-id="75a5e-190">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="75a5e-191">不需要启用出站端口 80 规则，因为启用入站规则后会自动许可出站流量。</span><span class="sxs-lookup"><span data-stu-id="75a5e-191">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="75a5e-192">完成应用测试后，请在命令提示符处按 <kbd>Ctrl</kbd> + <kbd>C</kbd> 关闭应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-192">When done testing the app, shut down the app with <kbd>Ctrl</kbd> + <kbd>C</kbd> at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="75a5e-193">监视应用</span><span class="sxs-lookup"><span data-stu-id="75a5e-193">Monitor the app</span></span>

<span data-ttu-id="75a5e-194">服务器设置为将对 `http://<serveraddress>:80` 发起的请求转发到在 `http://127.0.0.1:5000` 中的 Kestrel 上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-194">The server is set up to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="75a5e-195">但是，未将 Nginx 设置为管理 Kestrel 进程。</span><span class="sxs-lookup"><span data-stu-id="75a5e-195">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="75a5e-196">`systemd` 可用于创建服务文件以启动和监视基础 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-196">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="75a5e-197">`systemd` 是一个初始系统，可以提供启动、停止和管理进程的许多强大的功能。</span><span class="sxs-lookup"><span data-stu-id="75a5e-197">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="75a5e-198">创建服务文件</span><span class="sxs-lookup"><span data-stu-id="75a5e-198">Create the service file</span></span>

<span data-ttu-id="75a5e-199">创建服务定义文件：</span><span class="sxs-lookup"><span data-stu-id="75a5e-199">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="75a5e-200">以下示例是应用的服务文件：</span><span class="sxs-lookup"><span data-stu-id="75a5e-200">The following example is a service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="75a5e-201">在前面的示例中，管理服务的用户由 `User` 选项指定。</span><span class="sxs-lookup"><span data-stu-id="75a5e-201">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="75a5e-202">用户 (`www-data`) 必须存在并且拥有正确应用文件的所有权。</span><span class="sxs-lookup"><span data-stu-id="75a5e-202">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="75a5e-203">使用 `TimeoutStopSec` 配置在收到初始中断信号后等待应用程序关闭的持续时间。</span><span class="sxs-lookup"><span data-stu-id="75a5e-203">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="75a5e-204">如果应用程序在此时间段内未关闭，则将发出 SIGKILL 以终止该应用程序。</span><span class="sxs-lookup"><span data-stu-id="75a5e-204">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="75a5e-205">提供作为无单位秒数的值（例如，`150`）、时间跨度值（例如，`2min 30s`）或 `infinity` 以禁用超时。</span><span class="sxs-lookup"><span data-stu-id="75a5e-205">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="75a5e-206">`TimeoutStopSec` 默认为管理器配置文件 (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`) 中 `DefaultTimeoutStopSec` 的值。</span><span class="sxs-lookup"><span data-stu-id="75a5e-206">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="75a5e-207">大多数分发版的默认超时时间为 90 秒。</span><span class="sxs-lookup"><span data-stu-id="75a5e-207">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="75a5e-208">Linux 具有区分大小写的文件系统。</span><span class="sxs-lookup"><span data-stu-id="75a5e-208">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="75a5e-209">将 `ASPNETCORE_ENVIRONMENT` 设置为 `Production` 时，将搜索配置文件 `appsettings.Production.json`，而不搜索 `appsettings.production.json`。</span><span class="sxs-lookup"><span data-stu-id="75a5e-209">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="75a5e-210">必须转义某些值（例如，SQL 连接字符串）以供配置提供程序读取环境变量。</span><span class="sxs-lookup"><span data-stu-id="75a5e-210">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="75a5e-211">使用以下命令生成适当的转义值以供在配置文件中使用：</span><span class="sxs-lookup"><span data-stu-id="75a5e-211">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="75a5e-212">环境变量名不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="75a5e-212">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="75a5e-213">使用双下划线 (`__`) 代替冒号。</span><span class="sxs-lookup"><span data-stu-id="75a5e-213">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="75a5e-214">环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables)将双下划线转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="75a5e-214">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="75a5e-215">以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：</span><span class="sxs-lookup"><span data-stu-id="75a5e-215">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="75a5e-216">环境变量名不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="75a5e-216">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="75a5e-217">使用双下划线 (`__`) 代替冒号。</span><span class="sxs-lookup"><span data-stu-id="75a5e-217">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="75a5e-218">环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)将双下划线转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="75a5e-218">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="75a5e-219">以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：</span><span class="sxs-lookup"><span data-stu-id="75a5e-219">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="75a5e-220">保存该文件并启用该服务。</span><span class="sxs-lookup"><span data-stu-id="75a5e-220">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="75a5e-221">启用该服务，并确认它正在运行。</span><span class="sxs-lookup"><span data-stu-id="75a5e-221">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="75a5e-222">在配置了反向代理并通过 `systemd` 管理 Kestrel 后，Web 应用现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="75a5e-222">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="75a5e-223">也可以从远程计算机进行访问，同时限制可能进行阻止的任何防火墙。</span><span class="sxs-lookup"><span data-stu-id="75a5e-223">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="75a5e-224">检查响应标头，`Server` 标头显示由 Kestrel 所提供的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-224">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="75a5e-225">查看日志</span><span class="sxs-lookup"><span data-stu-id="75a5e-225">View logs</span></span>

<span data-ttu-id="75a5e-226">使用 Kestrel 的 Web 应用是通过 `systemd` 进行管理的，因此所有事件和进程都被记录到集中日志。</span><span class="sxs-lookup"><span data-stu-id="75a5e-226">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="75a5e-227">但是，此日志包含由 `systemd` 管理的所有服务和进程的全部条目。</span><span class="sxs-lookup"><span data-stu-id="75a5e-227">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="75a5e-228">若要查看特定于 `kestrel-helloapp.service` 的项，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="75a5e-228">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="75a5e-229">有关进一步筛选，使用时间选项（如 `--since today`、`--until 1 hour ago`）或这些选项的组合可以减少返回的条目数。</span><span class="sxs-lookup"><span data-stu-id="75a5e-229">For further filtering, time options such as `--since today`, `--until 1 hour ago`, or a combination of these can reduce the number of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="75a5e-230">数据保护</span><span class="sxs-lookup"><span data-stu-id="75a5e-230">Data protection</span></span>

<span data-ttu-id="75a5e-231">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)（包括 cookie 中间件等身份验证中间件）和跨站点请求伪造 (CSRF) 保护使用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-231">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="75a5e-232">即使用户代码不调用数据保护 API，也应该配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-232">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="75a5e-233">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="75a5e-233">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="75a5e-234">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="75a5e-234">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="75a5e-235">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="75a5e-235">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="75a5e-236">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="75a5e-236">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="75a5e-237">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="75a5e-237">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="75a5e-238">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-238">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="75a5e-239">若要配置数据保护以持久保存并加密密钥环，请参阅：</span><span class="sxs-lookup"><span data-stu-id="75a5e-239">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="75a5e-240">较长的请求标头字段</span><span class="sxs-lookup"><span data-stu-id="75a5e-240">Long request header fields</span></span>

<span data-ttu-id="75a5e-241">代理服务器默认设置通常将请求标头字段限制为 4 K 或 8 K，具体取决于平台。</span><span class="sxs-lookup"><span data-stu-id="75a5e-241">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="75a5e-242">某些应用可能需要超过默认值的字段（例如，使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 的应用）。</span><span class="sxs-lookup"><span data-stu-id="75a5e-242">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="75a5e-243">如果需要更长的字段，则代理服务器的默认设置需要进行调整。</span><span class="sxs-lookup"><span data-stu-id="75a5e-243">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="75a5e-244">要应用的值具体取决于方案。</span><span class="sxs-lookup"><span data-stu-id="75a5e-244">The values to apply depend on the scenario.</span></span> <span data-ttu-id="75a5e-245">有关详细信息，请参见服务器文档。</span><span class="sxs-lookup"><span data-stu-id="75a5e-245">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="75a5e-246">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="75a5e-246">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="75a5e-247">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="75a5e-247">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="75a5e-248">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="75a5e-248">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="75a5e-249">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="75a5e-249">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="75a5e-250">除非必要，否则不要提高代理缓冲区的默认值。</span><span class="sxs-lookup"><span data-stu-id="75a5e-250">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="75a5e-251">提高这些值将增加缓冲区溢出的风险和恶意用户的拒绝服务 (DoS) 攻击风险。</span><span class="sxs-lookup"><span data-stu-id="75a5e-251">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="75a5e-252">保护应用</span><span class="sxs-lookup"><span data-stu-id="75a5e-252">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="75a5e-253">启用 AppArmor</span><span class="sxs-lookup"><span data-stu-id="75a5e-253">Enable AppArmor</span></span>

<span data-ttu-id="75a5e-254">Linux 安全模块 (LSM) 是一个框架，它是自 Linux 2.6 后的 Linux kernel 的一部分。</span><span class="sxs-lookup"><span data-stu-id="75a5e-254">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="75a5e-255">LSM 支持安全模块的不同实现。</span><span class="sxs-lookup"><span data-stu-id="75a5e-255">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="75a5e-256">[AppArmor](https://wiki.ubuntu.com/AppArmor) 是实现强制访问控制系统的 LSM，它允许将程序限制在一组有限的资源内。</span><span class="sxs-lookup"><span data-stu-id="75a5e-256">[AppArmor](https://wiki.ubuntu.com/AppArmor) is an LSM that implements a Mandatory Access Control system, which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="75a5e-257">确保已启用并成功配置 AppArmor。</span><span class="sxs-lookup"><span data-stu-id="75a5e-257">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="75a5e-258">配置防火墙</span><span class="sxs-lookup"><span data-stu-id="75a5e-258">Configure the firewall</span></span>

<span data-ttu-id="75a5e-259">关闭所有未使用的外部端口。</span><span class="sxs-lookup"><span data-stu-id="75a5e-259">Close off all external ports that aren't in use.</span></span> <span data-ttu-id="75a5e-260">通过为配置防火墙提供 CLI，不复杂的防火墙 (ufw) 为 `iptables` 提供了前端。</span><span class="sxs-lookup"><span data-stu-id="75a5e-260">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="75a5e-261">如果未正确配置，防火墙将阻止对整个系统的访问。</span><span class="sxs-lookup"><span data-stu-id="75a5e-261">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="75a5e-262">在使用 SSH 进行连接时，未能指定正确的 SSH 端口最终会将你关在系统之外。</span><span class="sxs-lookup"><span data-stu-id="75a5e-262">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="75a5e-263">默认端口为 22。</span><span class="sxs-lookup"><span data-stu-id="75a5e-263">The default port is 22.</span></span> <span data-ttu-id="75a5e-264">有关详细信息，请参阅 [ufw 简介](https://help.ubuntu.com/community/UFW)和[手册](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-264">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="75a5e-265">安装 `ufw`，并将其配置为允许所需任何端口上的流量。</span><span class="sxs-lookup"><span data-stu-id="75a5e-265">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="75a5e-266">保护 Nginx</span><span class="sxs-lookup"><span data-stu-id="75a5e-266">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="75a5e-267">更改 Nginx 响应名称</span><span class="sxs-lookup"><span data-stu-id="75a5e-267">Change the Nginx response name</span></span>

<span data-ttu-id="75a5e-268">编辑 `src/http/ngx_http_header_filter_module.c`：</span><span class="sxs-lookup"><span data-stu-id="75a5e-268">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="75a5e-269">配置选项</span><span class="sxs-lookup"><span data-stu-id="75a5e-269">Configure options</span></span>

<span data-ttu-id="75a5e-270">用其他必需模块配置服务器。</span><span class="sxs-lookup"><span data-stu-id="75a5e-270">Configure the server with additional required modules.</span></span> <span data-ttu-id="75a5e-271">请考虑使用 [ModSecurity](https://www.modsecurity.org/) 等 Web 应用防火墙来加强对应用的保护。</span><span class="sxs-lookup"><span data-stu-id="75a5e-271">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="75a5e-272">HTTPS 配置</span><span class="sxs-lookup"><span data-stu-id="75a5e-272">HTTPS configuration</span></span>

<span data-ttu-id="75a5e-273">配置应用，以进行安全的 (HTTPS) 本地连接</span><span class="sxs-lookup"><span data-stu-id="75a5e-273">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="75a5e-274">[dotnet run](/dotnet/core/tools/dotnet-run) 命令使用应用的 Properties/launchSettings.json 文件，该文件将应用配置为侦听 `applicationUrl` 属性提供的 URL。</span><span class="sxs-lookup"><span data-stu-id="75a5e-274">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property.</span></span> <span data-ttu-id="75a5e-275">例如，`https://localhost:5001;http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="75a5e-275">For example, `https://localhost:5001;http://localhost:5000`.</span></span>

<span data-ttu-id="75a5e-276">使用以下方法之一配置应用，使其在开发过程中将证书用于 `dotnet run` 命令或开发环境（Visual Studio Code 中的 F5 或 Ctrl+F5<kbd></kbd><kbd></kbd><kbd></kbd>）：</span><span class="sxs-lookup"><span data-stu-id="75a5e-276">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="75a5e-277">[从配置中替换默认证书](xref:fundamentals/servers/kestrel/endpoints#configuration)（推荐）</span><span class="sxs-lookup"><span data-stu-id="75a5e-277">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="75a5e-278">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="75a5e-278">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="75a5e-279">[从配置中替换默认证书](xref:fundamentals/servers/kestrel#configuration)（推荐）</span><span class="sxs-lookup"><span data-stu-id="75a5e-279">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="75a5e-280">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="75a5e-280">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="75a5e-281">配置反向代理，以便进行安全 (HTTPS) 客户端连接</span><span class="sxs-lookup"><span data-stu-id="75a5e-281">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

> [!WARNING]
> <span data-ttu-id="75a5e-282">本部分中的安全配置是一种常规配置，可以作为进一步进行自定义的起点。</span><span class="sxs-lookup"><span data-stu-id="75a5e-282">The security configuration in this section is a general configuration to be used as a starting point for further customization.</span></span> <span data-ttu-id="75a5e-283">我们无法为第三方工具、服务器和操作系统提供支持。</span><span class="sxs-lookup"><span data-stu-id="75a5e-283">We're unable to provide support for third-party tooling, servers, and operating systems.</span></span> <span data-ttu-id="75a5e-284">如果使用本部分中的配置，请自行承担风险。</span><span class="sxs-lookup"><span data-stu-id="75a5e-284">*Use the configuration in this section at your own risk.*</span></span> <span data-ttu-id="75a5e-285">有关更多信息，请访问以下资源：</span><span class="sxs-lookup"><span data-stu-id="75a5e-285">For more information, access the following resources:</span></span>
>
> * <span data-ttu-id="75a5e-286">[配置 HTTPS 服务器](http://nginx.org/docs/http/configuring_https_servers.html)（Nginx 文档）</span><span class="sxs-lookup"><span data-stu-id="75a5e-286">[Configuring HTTPS servers](http://nginx.org/docs/http/configuring_https_servers.html) (Nginx documentation)</span></span>
> * [<span data-ttu-id="75a5e-287">mozilla.org SSL Configuration Generator</span><span class="sxs-lookup"><span data-stu-id="75a5e-287">mozilla.org SSL Configuration Generator</span></span>](https://ssl-config.mozilla.org/#server=nginx)

* <span data-ttu-id="75a5e-288">通过指定由受信任的证书颁发机构 (CA) 颁发的有效证书来配置服务器，以侦听端口 443 上的 HTTPS 流量。</span><span class="sxs-lookup"><span data-stu-id="75a5e-288">Configure the server to listen to HTTPS traffic on port 443 by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="75a5e-289">通过采用以下“/etc/nginx/nginx.conf”文件中所示的某些做法来增强安全保护。</span><span class="sxs-lookup"><span data-stu-id="75a5e-289">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span>

* <span data-ttu-id="75a5e-290">下面的示例未将服务器配置为重定向不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="75a5e-290">The following example doesn't configure the server to redirect insecure requests.</span></span> <span data-ttu-id="75a5e-291">建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="75a5e-291">We recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="75a5e-292">有关详细信息，请参阅 <xref:security/enforcing-ssl>。</span><span class="sxs-lookup"><span data-stu-id="75a5e-292">For more information, see <xref:security/enforcing-ssl>.</span></span>

  > [!NOTE]
  > <span data-ttu-id="75a5e-293">对于由服务器配置（而非 HTTPS 重定向中间件）处理安全重定向的开发环境，建议使用临时重定向 (302) 替代永久性重定向 (301)。</span><span class="sxs-lookup"><span data-stu-id="75a5e-293">For development environments where the server configuration handles secure redirection instead of HTTPS Redirection Middleware, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="75a5e-294">链接缓存会导致开发环境中的行为不稳定。</span><span class="sxs-lookup"><span data-stu-id="75a5e-294">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="75a5e-295">添加 `Strict-Transport-Security` (HSTS) 标头可确保由客户端发起的所有后续请求都通过 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="75a5e-295">Adding a `Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span> <span data-ttu-id="75a5e-296">有关设置 `Strict-Transport-Security` 标头的指南，请参阅 <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>。</span><span class="sxs-lookup"><span data-stu-id="75a5e-296">For guidance on setting the `Strict-Transport-Security` header, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="75a5e-297">如果将来将禁用 HTTPS，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="75a5e-297">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="75a5e-298">不要添加 HSTS 标头。</span><span class="sxs-lookup"><span data-stu-id="75a5e-298">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="75a5e-299">选择短的 `max-age` 值。</span><span class="sxs-lookup"><span data-stu-id="75a5e-299">Choose a short `max-age` value.</span></span>

<span data-ttu-id="75a5e-300">添加 /etc/nginx/proxy.conf 配置文件：</span><span class="sxs-lookup"><span data-stu-id="75a5e-300">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="75a5e-301">将 /etc/nginx/nginx.conf 配置文件的内容替换为以下文件。</span><span class="sxs-lookup"><span data-stu-id="75a5e-301">**Replace** the contents of the */etc/nginx/nginx.conf* configuration file with the following file.</span></span> <span data-ttu-id="75a5e-302">示例包含一个配置文件中的 `http` 和 `server` 部分。</span><span class="sxs-lookup"><span data-stu-id="75a5e-302">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf)]

> [!NOTE]
> <span data-ttu-id="75a5e-303">Blazor WebAssembly 应用需要更大的 `burst` 参数值才能容纳应用发出的更大量的请求。</span><span class="sxs-lookup"><span data-stu-id="75a5e-303">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="75a5e-304">有关详细信息，请参阅 <xref:blazor/host-and-deploy/webassembly#nginx>。</span><span class="sxs-lookup"><span data-stu-id="75a5e-304">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

> [!NOTE]
> <span data-ttu-id="75a5e-305">上面的示例禁用了在线证书状态协议 (OCSP) 装订。</span><span class="sxs-lookup"><span data-stu-id="75a5e-305">The preceding example disables Online Certificate Status Protocol (OCSP) Stapling.</span></span> <span data-ttu-id="75a5e-306">如果启用，请确认证书支持该功能。</span><span class="sxs-lookup"><span data-stu-id="75a5e-306">If enabled, confirm that the certificate supports the feature.</span></span> <span data-ttu-id="75a5e-307">有关启用 OCSP 的详细信息和指南，请参阅[模块 ngx_http_ssl_module（Nginx 文档）](http://nginx.org/en/docs/http/ngx_http_ssl_module.html)一文中的以下属性：</span><span class="sxs-lookup"><span data-stu-id="75a5e-307">For more information and guidance on enabling OCSP, see the following properties in the [Module ngx_http_ssl_module (Nginx documentation)](http://nginx.org/en/docs/http/ngx_http_ssl_module.html) article:</span></span>
>
> * `ssl_stapling`
> * `ssl_stapling_file`
> * `ssl_stapling_responder`
> * `ssl_stapling_verify`

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="75a5e-308">保护 Nginx 免受点击劫持的侵害</span><span class="sxs-lookup"><span data-stu-id="75a5e-308">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="75a5e-309">[点击劫持](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)（也称为 *UI 伪装攻击*）是一种恶意攻击，其中网站访问者会上当受骗，从而导致在与当前要访问的页面不同的页面上单击链接或按钮。</span><span class="sxs-lookup"><span data-stu-id="75a5e-309">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="75a5e-310">使用 `X-FRAME-OPTIONS` 可保护网站。</span><span class="sxs-lookup"><span data-stu-id="75a5e-310">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="75a5e-311">缓解点击劫持攻击：</span><span class="sxs-lookup"><span data-stu-id="75a5e-311">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="75a5e-312">编辑 nginx.conf 文件：</span><span class="sxs-lookup"><span data-stu-id="75a5e-312">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="75a5e-313">添加行：`add_header X-Frame-Options "SAMEORIGIN";`</span><span class="sxs-lookup"><span data-stu-id="75a5e-313">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="75a5e-314">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="75a5e-314">Save the file.</span></span>
1. <span data-ttu-id="75a5e-315">重启 Nginx。</span><span class="sxs-lookup"><span data-stu-id="75a5e-315">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="75a5e-316">MIME 类型探查</span><span class="sxs-lookup"><span data-stu-id="75a5e-316">MIME-type sniffing</span></span>

<span data-ttu-id="75a5e-317">此标头可阻止大部分浏览器通过 MIME 方式探查来自已声明内容类型的响应，因为标头会指示浏览器不要替代响应内容类型。</span><span class="sxs-lookup"><span data-stu-id="75a5e-317">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="75a5e-318">使用 `nosniff` 选项后，如果服务器认为内容是“`text/html`”，则浏览器将其显示为“`text/html`”。</span><span class="sxs-lookup"><span data-stu-id="75a5e-318">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="75a5e-319">编辑 nginx.conf 文件：</span><span class="sxs-lookup"><span data-stu-id="75a5e-319">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="75a5e-320">添加行：`add_header X-Content-Type-Options "nosniff";`</span><span class="sxs-lookup"><span data-stu-id="75a5e-320">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="75a5e-321">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="75a5e-321">Save the file.</span></span>
1. <span data-ttu-id="75a5e-322">重启 Nginx。</span><span class="sxs-lookup"><span data-stu-id="75a5e-322">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="75a5e-323">其他 Nginx 建议</span><span class="sxs-lookup"><span data-stu-id="75a5e-323">Additional Nginx suggestions</span></span>

<span data-ttu-id="75a5e-324">在服务器上升级共享框架后，重启服务器托管的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="75a5e-324">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75a5e-325">其他资源</span><span class="sxs-lookup"><span data-stu-id="75a5e-325">Additional resources</span></span>

* [<span data-ttu-id="75a5e-326">Linux 上 .NET Core 的先决条件</span><span class="sxs-lookup"><span data-stu-id="75a5e-326">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="75a5e-327">Nginx：二进制版本：官方 Debian/Ubuntu 包</span><span class="sxs-lookup"><span data-stu-id="75a5e-327">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="75a5e-328">NGINX：使用转接头</span><span class="sxs-lookup"><span data-stu-id="75a5e-328">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
