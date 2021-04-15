---
title: 为 ASP.NET Core Kestrel Web 服务器配置终结点
author: rick-anderson
description: 了解如何为 Kestrel（跨平台 ASP.NET Core Web 服务器）配置终结点。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: d1b682b12e2cdcaf2a77b17f726ac569c46cf095
ms.sourcegitcommit: f67ba959d3cbfe33b32fa6a5eae1a5ae9de18167
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106179725"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="31884-103">为 ASP.NET Core Kestrel Web 服务器配置终结点</span><span class="sxs-lookup"><span data-stu-id="31884-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="31884-104">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="31884-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="31884-105">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="31884-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="31884-106">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="31884-106">Specify URLs using the:</span></span>

* <span data-ttu-id="31884-107">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="31884-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="31884-108">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="31884-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="31884-109">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="31884-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="31884-110">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="31884-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="31884-111">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="31884-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="31884-112">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="31884-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="31884-113">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31884-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="31884-114">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="31884-114">A development certificate is created:</span></span>

* <span data-ttu-id="31884-115">安装 [.NET SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="31884-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="31884-116">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="31884-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="31884-117">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="31884-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="31884-118">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="31884-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="31884-119">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="31884-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="31884-120">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="31884-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="31884-121">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="31884-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="31884-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="31884-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="31884-123">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="31884-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="31884-124">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="31884-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="31884-125">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="31884-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="31884-126">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="31884-126">Configure(IConfiguration)</span></span>

<span data-ttu-id="31884-127">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31884-127">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="31884-128">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="31884-128">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="31884-129">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="31884-129">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="31884-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="31884-130">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="31884-131">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="31884-131">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="31884-132">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="31884-132">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="31884-133">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="31884-133">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="listenoptionsusehttps"></a><span data-ttu-id="31884-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="31884-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="31884-135">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="31884-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="31884-136">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="31884-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="31884-137">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="31884-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="31884-138">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="31884-138">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="31884-139">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="31884-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="31884-140">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="31884-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="31884-141">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="31884-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="31884-142">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="31884-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="31884-143">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="31884-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="31884-144">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="31884-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="31884-145">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="31884-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="31884-146">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="31884-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="31884-147">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="31884-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="31884-148">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="31884-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="31884-149">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="31884-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="31884-150">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="31884-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="31884-151">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="31884-151">Supported configurations described next:</span></span>

* <span data-ttu-id="31884-152">无配置</span><span class="sxs-lookup"><span data-stu-id="31884-152">No configuration</span></span>
* <span data-ttu-id="31884-153">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="31884-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="31884-154">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="31884-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="31884-155">无配置</span><span class="sxs-lookup"><span data-stu-id="31884-155">No configuration</span></span>

<span data-ttu-id="31884-156">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="31884-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="31884-157">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="31884-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="31884-158">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="31884-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="31884-159">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="31884-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="31884-160">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="31884-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="31884-161">将 `AllowInvalid` 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="31884-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="31884-162">任何未指定证书的 HTTPS 终结点（下例中的 `HttpsDefaultCert`）会回退至在 `Certificates:Default` 下定义的证书或开发证书。</span><span class="sxs-lookup"><span data-stu-id="31884-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates:Default` or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="31884-163">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="31884-163">Schema notes:</span></span>

* <span data-ttu-id="31884-164">终结点的名称[不区分大小写](xref:fundamentals/configuration/index#configuration-keys-and-values)。</span><span class="sxs-lookup"><span data-stu-id="31884-164">Endpoints names are [case-insensitive](xref:fundamentals/configuration/index#configuration-keys-and-values).</span></span> <span data-ttu-id="31884-165">例如，由于再也无法解析标识符“Families”，因此 `HTTPS` and `Https` 是等效的。</span><span class="sxs-lookup"><span data-stu-id="31884-165">For example, `HTTPS` and `Https` are equivalent.</span></span>
* <span data-ttu-id="31884-166">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="31884-166">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="31884-167">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="31884-167">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="31884-168">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="31884-168">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="31884-169">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="31884-169">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="31884-170">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="31884-170">The `Certificate` section is optional.</span></span> <span data-ttu-id="31884-171">如果未指定 `Certificate` 部分，则使用 `Certificates:Default` 中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="31884-171">If the `Certificate` section isn't specified, the defaults defined in `Certificates:Default` are used.</span></span> <span data-ttu-id="31884-172">如果没有可用的默认值，则使用开发证书。</span><span class="sxs-lookup"><span data-stu-id="31884-172">If no defaults are available, the development certificate is used.</span></span> <span data-ttu-id="31884-173">如果没有默认值，且开发证书不存在，则服务器将引发异常，并且无法启动。</span><span class="sxs-lookup"><span data-stu-id="31884-173">If there are no defaults and the development certificate isn't present, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="31884-174">`Certificate` 部分支持多个[证书源](#certificate-sources)。</span><span class="sxs-lookup"><span data-stu-id="31884-174">The `Certificate` section supports multiple [certificate sources](#certificate-sources).</span></span>
* <span data-ttu-id="31884-175">只要不会导致端口冲突，就能在[配置](xref:fundamentals/configuration/index)中定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="31884-175">Any number of endpoints may be defined in [Configuration](xref:fundamentals/configuration/index) as long as they don't cause port conflicts.</span></span>

#### <a name="certificate-sources"></a><span data-ttu-id="31884-176">证书源</span><span class="sxs-lookup"><span data-stu-id="31884-176">Certificate sources</span></span>

<span data-ttu-id="31884-177">可以将证书节点配置为从多个源加载证书：</span><span class="sxs-lookup"><span data-stu-id="31884-177">Certificate nodes can be configured to load certificates from a number of sources:</span></span>

* <span data-ttu-id="31884-178">`Path` 和 `Password` 用于加载 .pfx 文件。</span><span class="sxs-lookup"><span data-stu-id="31884-178">`Path` and `Password` to load *.pfx* files.</span></span>
* <span data-ttu-id="31884-179">`Path`、`KeyPath` 和 `Password` 用于加载 .pem/ *.crt* 和 .key 文件。</span><span class="sxs-lookup"><span data-stu-id="31884-179">`Path`, `KeyPath` and `Password` to load *.pem*/*.crt* and *.key* files.</span></span>
* <span data-ttu-id="31884-180">`Subject` 和 `Store` 用于从证书存储中加载。</span><span class="sxs-lookup"><span data-stu-id="31884-180">`Subject` and `Store` to load from the certificate store.</span></span>

<span data-ttu-id="31884-181">例如，可将 `Certificates:Default` 证书指定为：</span><span class="sxs-lookup"><span data-stu-id="31884-181">For example, the `Certificates:Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a><span data-ttu-id="31884-182">ConfigurationLoader</span><span class="sxs-lookup"><span data-stu-id="31884-182">ConfigurationLoader</span></span>

<span data-ttu-id="31884-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader>，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="31884-183">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="31884-184">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="31884-184">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="31884-185">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="31884-185">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="31884-186">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="31884-186">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="31884-187">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="31884-187">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="31884-188">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="31884-188">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="31884-189">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="31884-189">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="31884-190">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="31884-190">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="31884-191">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="31884-191">Change the defaults in code</span></span>

<span data-ttu-id="31884-192">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="31884-192">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="31884-193">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="31884-193">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="31884-194">使用服务器名称指示配置终结点</span><span class="sxs-lookup"><span data-stu-id="31884-194">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="31884-195">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="31884-195">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="31884-196">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="31884-196">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="31884-197">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="31884-197">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="31884-198">可用两种方式配置 SNI：</span><span class="sxs-lookup"><span data-stu-id="31884-198">SNI can be configured in two ways:</span></span>

* <span data-ttu-id="31884-199">在代码中创建终结点，并通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ServerCertificateSelector%2A> 回调使用主机名选择证书。</span><span class="sxs-lookup"><span data-stu-id="31884-199">Create an endpoint in code and select a certificate using the host name with the <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ServerCertificateSelector%2A> callback.</span></span>
* <span data-ttu-id="31884-200">在[配置](xref:fundamentals/configuration/index)中配置主机名和 HTTPS 选项之间的映射。</span><span class="sxs-lookup"><span data-stu-id="31884-200">Configure a mapping between host names and HTTPS options in [Configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="31884-201">例如，`appsettings.json` 文件中的 JSON。</span><span class="sxs-lookup"><span data-stu-id="31884-201">For example, JSON in  the `appsettings.json` file.</span></span>

### <a name="sni-with-servercertificateselector"></a><span data-ttu-id="31884-202">具有 `ServerCertificateSelector` 的 SNI</span><span class="sxs-lookup"><span data-stu-id="31884-202">SNI with `ServerCertificateSelector`</span></span>

<span data-ttu-id="31884-203">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="31884-203">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="31884-204">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="31884-204">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="31884-205">可以在项目的 Program.cs 文件的 `ConfigureWebHostDefaults` 方法调用中使用以下回调代码：</span><span class="sxs-lookup"><span data-stu-id="31884-205">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="sni-in-configuration"></a><span data-ttu-id="31884-206">配置中的 SNI</span><span class="sxs-lookup"><span data-stu-id="31884-206">SNI in configuration</span></span>

<span data-ttu-id="31884-207">Kestrel 支持配置中定义的 SNI。</span><span class="sxs-lookup"><span data-stu-id="31884-207">Kestrel supports SNI defined in configuration.</span></span> <span data-ttu-id="31884-208">可以使用包含主机名和 HTTPS 选项之间的映射的 `Sni` 对象来配置终结点。</span><span class="sxs-lookup"><span data-stu-id="31884-208">An endpoint can be configured with an `Sni` object that contains a mapping between host names and HTTPS options.</span></span> <span data-ttu-id="31884-209">连接主机名与选项匹配，并且这些选项用于该连接。</span><span class="sxs-lookup"><span data-stu-id="31884-209">The connection host name is matched to the options and they are used for that connection.</span></span>

<span data-ttu-id="31884-210">以下配置将添加一个名为 `MySniEndpoint` 的终结点，该终结点使用 SNI 基于主机名选择 HTTPS 选项：</span><span class="sxs-lookup"><span data-stu-id="31884-210">The following configuration adds an endpoint named `MySniEndpoint` that uses SNI to select HTTPS options based on the host name:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "MySniEndpoint": {
        "Url": "https://*",
        "SslProtocols": ["Tls11", "Tls12"],
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": ["Tls11", "Tls12", "Tls13"],
            "Certificate": {
              "Subject": "<subject; required>",
              "Store": "<certificate store; required>",
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "<path to .pfx file>",
              "Password": "<certificate password>"
            }
          },
          "*": {
            // At least one subproperty needs to exist per SNI section or it
            // cannot be discovered via IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="31884-211">可由 SNI 覆盖的 HTTPS 选项：</span><span class="sxs-lookup"><span data-stu-id="31884-211">HTTPS options that can be overridden by SNI:</span></span>

* <span data-ttu-id="31884-212">`Certificate` 配置[证书源](#certificate-sources)。</span><span class="sxs-lookup"><span data-stu-id="31884-212">`Certificate` configures the [certificate source](#certificate-sources).</span></span>
* <span data-ttu-id="31884-213">`Protocols` 配置允许的 [HTTP 协议](xref:Microsoft.AspNetCore.Server.Kestrel.Core.HttpProtocols)。</span><span class="sxs-lookup"><span data-stu-id="31884-213">`Protocols` configures the allowed [HTTP protocols](xref:Microsoft.AspNetCore.Server.Kestrel.Core.HttpProtocols).</span></span>
* <span data-ttu-id="31884-214">`SslProtocols` 配置允许的 [SSL 协议](xref:System.Security.Authentication.SslProtocols)。</span><span class="sxs-lookup"><span data-stu-id="31884-214">`SslProtocols` configures the allowed [SSL protocols](xref:System.Security.Authentication.SslProtocols).</span></span>
* <span data-ttu-id="31884-215">`ClientCertificateMode` 配置[客户端证书要求](xref:Microsoft.AspNetCore.Server.Kestrel.Https.ClientCertificateMode)。</span><span class="sxs-lookup"><span data-stu-id="31884-215">`ClientCertificateMode` configures the [client certificate requirements](xref:Microsoft.AspNetCore.Server.Kestrel.Https.ClientCertificateMode).</span></span>

<span data-ttu-id="31884-216">主机名支持通配符匹配：</span><span class="sxs-lookup"><span data-stu-id="31884-216">The host name supports wildcard matching:</span></span>

* <span data-ttu-id="31884-217">完全匹配。</span><span class="sxs-lookup"><span data-stu-id="31884-217">Exact match.</span></span> <span data-ttu-id="31884-218">例如，`a.example.org` 匹配 `a.example.org`。</span><span class="sxs-lookup"><span data-stu-id="31884-218">For example, `a.example.org` matches `a.example.org`.</span></span>
* <span data-ttu-id="31884-219">通配符前缀。</span><span class="sxs-lookup"><span data-stu-id="31884-219">Wildcard prefix.</span></span> <span data-ttu-id="31884-220">如果有多个通配符匹配项，则选择最长的模式。</span><span class="sxs-lookup"><span data-stu-id="31884-220">If there are multiple wildcard matches then the longest pattern is chosen.</span></span> <span data-ttu-id="31884-221">例如，`*.example.org` 匹配 `b.example.org` 和 `c.example.org`。</span><span class="sxs-lookup"><span data-stu-id="31884-221">For example, `*.example.org` matches `b.example.org` and `c.example.org`.</span></span>
* <span data-ttu-id="31884-222">完整通配符。</span><span class="sxs-lookup"><span data-stu-id="31884-222">Full wildcard.</span></span> <span data-ttu-id="31884-223">`*` 匹配其他所有内容，包括不使用 SNI 且不发送主机名的客户端。</span><span class="sxs-lookup"><span data-stu-id="31884-223">`*` matches everything else, including clients that aren't using SNI and don't send a host name.</span></span>

<span data-ttu-id="31884-224">匹配的 SNI 配置将应用于连接的终结点，并重写终结点上的值。</span><span class="sxs-lookup"><span data-stu-id="31884-224">The matched SNI configuration is applied to the endpoint for the connection, overriding values on the endpoint.</span></span> <span data-ttu-id="31884-225">如果连接与已配置的 SNI 主机名不匹配，则连接将被拒绝。</span><span class="sxs-lookup"><span data-stu-id="31884-225">If a connection doesn't match a configured SNI host name then the connection is refused.</span></span>

### <a name="sni-requirements"></a><span data-ttu-id="31884-226">SNI 要求</span><span class="sxs-lookup"><span data-stu-id="31884-226">SNI requirements</span></span>

* <span data-ttu-id="31884-227">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="31884-227">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="31884-228">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="31884-228">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="31884-229">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="31884-229">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="31884-230">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="31884-230">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="31884-231">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="31884-231">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="ssltls-protocols"></a><span data-ttu-id="31884-232">SSL/TLS 协议</span><span class="sxs-lookup"><span data-stu-id="31884-232">SSL/TLS Protocols</span></span>

<span data-ttu-id="31884-233">SSL 协议是用于在两个对等机（传统上是客户端和服务器）之间加密和解密流量的协议。</span><span class="sxs-lookup"><span data-stu-id="31884-233">SSL Protocols are protocols used for encrypting and decrypting traffic between two peers, traditionally a client and a server.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls13;
    });
});
```

<span data-ttu-id="31884-234">默认值 `SslProtocols.None` 会导致 Kestrel 使用操作系统默认值来选择最佳协议。</span><span class="sxs-lookup"><span data-stu-id="31884-234">The default value, `SslProtocols.None`, causes Kestrel to use the operating system defaults to choose the best protocol.</span></span> <span data-ttu-id="31884-235">除非你有特定原因要选择协议，否则请使用默认值。</span><span class="sxs-lookup"><span data-stu-id="31884-235">Unless you have a specific reason to select a protocol, use the default.</span></span>

## <a name="connection-logging"></a><span data-ttu-id="31884-236">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="31884-236">Connection logging</span></span>

<span data-ttu-id="31884-237">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="31884-237">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="31884-238">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="31884-238">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="31884-239">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="31884-239">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="31884-240">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="31884-240">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="31884-241">这是内置[连接中间件](#connection-middleware)。</span><span class="sxs-lookup"><span data-stu-id="31884-241">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="31884-242">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="31884-242">Bind to a TCP socket</span></span>

<span data-ttu-id="31884-243"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="31884-243">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="31884-244">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="31884-244">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="31884-245">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="31884-245">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="31884-246">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="31884-246">Bind to a Unix socket</span></span>

<span data-ttu-id="31884-247">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="31884-247">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="31884-248">在 Nginx 配置文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="31884-248">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="31884-249">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="31884-249">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="31884-250">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="31884-250">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="31884-251">端口 0</span><span class="sxs-lookup"><span data-stu-id="31884-251">Port 0</span></span>

<span data-ttu-id="31884-252">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="31884-252">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="31884-253">以下示例演示如何确定 Kestrel 在运行时绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="31884-253">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="31884-254">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="31884-254">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="31884-255">限制</span><span class="sxs-lookup"><span data-stu-id="31884-255">Limitations</span></span>

<span data-ttu-id="31884-256">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="31884-256">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="31884-257">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="31884-257">`--urls` command-line argument</span></span>
* <span data-ttu-id="31884-258">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="31884-258">`urls` host configuration key</span></span>
* <span data-ttu-id="31884-259">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="31884-259">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="31884-260">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="31884-260">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="31884-261">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="31884-261">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="31884-262">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本文前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="31884-262">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="31884-263">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="31884-263">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="31884-264">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="31884-264">IIS endpoint configuration</span></span>

<span data-ttu-id="31884-265">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="31884-265">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="31884-266">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31884-266">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="31884-267">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="31884-267">ListenOptions.Protocols</span></span>

<span data-ttu-id="31884-268">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="31884-268">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="31884-269">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="31884-269">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="31884-270">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="31884-270">`HttpProtocols` enum value</span></span> | <span data-ttu-id="31884-271">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="31884-271">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="31884-272">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31884-272">HTTP/1.1 only.</span></span> <span data-ttu-id="31884-273">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="31884-273">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="31884-274">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="31884-274">HTTP/2 only.</span></span> <span data-ttu-id="31884-275">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="31884-275">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="31884-276">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="31884-276">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="31884-277">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31884-277">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="31884-278">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="31884-278">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="31884-279">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="31884-279">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="31884-280">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31884-280">TLS version 1.2 or later</span></span>
* <span data-ttu-id="31884-281">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="31884-281">Renegotiation disabled</span></span>
* <span data-ttu-id="31884-282">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="31884-282">Compression disabled</span></span>
* <span data-ttu-id="31884-283">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="31884-283">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="31884-284">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="31884-284">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="31884-285">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="31884-285">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="31884-286">不禁止密码套件。</span><span class="sxs-lookup"><span data-stu-id="31884-286">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="31884-287">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="31884-287">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="31884-288">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="31884-288">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="31884-289">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="31884-289">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="31884-290">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="31884-290">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a><span data-ttu-id="31884-291">连接中间件</span><span class="sxs-lookup"><span data-stu-id="31884-291">Connection Middleware</span></span>

<span data-ttu-id="31884-292">必要时，自定义连接中间件可以按连接为特定密码筛选 TLS 握手。</span><span class="sxs-lookup"><span data-stu-id="31884-292">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="31884-293">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="31884-293">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="31884-294">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="31884-294">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="31884-295">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="31884-295">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="31884-296">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="31884-296">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="31884-297">从配置中设置 HTTP 协议</span><span class="sxs-lookup"><span data-stu-id="31884-297">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="31884-298">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="31884-298">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="31884-299">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议：</span><span class="sxs-lookup"><span data-stu-id="31884-299">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="31884-300">以下 appsettings.json 示例将为所有指定终结点建立 HTTP/1.1 连接协议：</span><span class="sxs-lookup"><span data-stu-id="31884-300">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="31884-301">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="31884-301">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="31884-302">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="31884-302">URL prefixes</span></span>

<span data-ttu-id="31884-303">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="31884-303">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="31884-304">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="31884-304">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="31884-305">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="31884-305">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="31884-306">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="31884-306">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="31884-307">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="31884-307">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="31884-308">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="31884-308">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="31884-309">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="31884-309">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="31884-310">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="31884-310">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="31884-311">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="31884-311">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="31884-312">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="31884-312">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="31884-313">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="31884-313">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="31884-314">反向代理服务器示例包括 IIS、Nginx 或 Apache。</span><span class="sxs-lookup"><span data-stu-id="31884-314">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="31884-315">采用反向代理配置进行托管需要[主机筛选](xref:fundamentals/servers/kestrel/host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="31884-315">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="31884-316">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="31884-316">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="31884-317">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="31884-317">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="31884-318">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="31884-318">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="31884-319">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="31884-319">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
