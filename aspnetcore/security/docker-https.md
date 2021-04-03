---
title: 通过 HTTPS 在 Docker 上宿主 ASP.NET Core 映像
author: rick-anderson
description: 了解如何通过 HTTPS 在 Docker 上托管 ASP.NET Core 映像
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
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
uid: security/docker-https
ms.openlocfilehash: 3201eac9128e30e63a34b2e1b8736ac69fb59d8f
ms.sourcegitcommit: 7354c2029164702d075fd3786d96a92c6d49bc6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106164300"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="4f7c6-103">通过 HTTPS 在 Docker 上宿主 ASP.NET Core 映像</span><span class="sxs-lookup"><span data-stu-id="4f7c6-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="4f7c6-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4f7c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4f7c6-105">[默认情况下](./enforcing-ssl.md)，ASP.NET Core 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="4f7c6-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 依赖于信任、标识和加密的 [证书](https://en.wikipedia.org/wiki/Public_key_certificate) 。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="4f7c6-107">本文档说明如何使用 [.net 命令行界面 (CLI) ](/dotnet/core/tools/)使用 HTTPS 运行预生成的容器映像。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-107">This document explains how to run pre-built container images with HTTPS using the [.NET command-line interface (CLI)](/dotnet/core/tools/).</span></span> <span data-ttu-id="4f7c6-108">有关如何在 Visual Studio 中运行 Docker 的说明，请参阅 [通过 HTTPS 开发具有 Docker 的 ASP.NET Core 应用程序](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-108">For instructions on how to run Docker in development with Visual Studio, see [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md).</span></span>

<span data-ttu-id="4f7c6-109">此示例需要 [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) 或更高版本的 [Docker 客户端](https://www.docker.com/products/docker)。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4f7c6-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="4f7c6-110">Prerequisites</span></span>

<span data-ttu-id="4f7c6-111">本文档中的某些说明需要 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="4f7c6-112">证书</span><span class="sxs-lookup"><span data-stu-id="4f7c6-112">Certificates</span></span>

<span data-ttu-id="4f7c6-113">针对域的[生产主机](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要[证书颁发机构颁发](https://wikipedia.org/wiki/Certificate_authority)的证书。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="4f7c6-114">[Let's Encrypt](https://letsencrypt.org/) 是提供免费证书的证书颁发机构。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="4f7c6-115">本文档使用 [自签名开发证书](https://en.wikipedia.org/wiki/Self-signed_certificate) 来托管预生成的映像 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="4f7c6-116">说明类似于使用生产证书。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="4f7c6-117">使用 [dotnet dev](/dotnet/core/additional-tools/self-signed-certificates-guide) 证书创建用于开发和测试的自签名证书。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="4f7c6-118">对于生产证书：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-118">For production certs:</span></span>

* <span data-ttu-id="4f7c6-119">此 `dotnet dev-certs` 工具不是必需的。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="4f7c6-120">证书不需要存储在说明中使用的位置。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="4f7c6-121">尽管不建议在网站目录中存储证书，但任何位置都应有效。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="4f7c6-122">以下部分中包含的说明使用 Docker 的命令行选项将证书装载到容器中 `-v` 。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="4f7c6-123">可以使用 Dockerfile 中的命令将证书添加到容器映像 `COPY` 中，但不建议这样做。 </span><span class="sxs-lookup"><span data-stu-id="4f7c6-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="4f7c6-124">由于以下原因，不建议将证书复制到映像：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="4f7c6-125">使用同一个映像测试开发人员证书非常困难。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="4f7c6-126">使用同一个映像通过生产证书进行托管很困难。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="4f7c6-127">证书泄露存在重大风险。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="4f7c6-128">用 HTTPS 运行预生成的容器映像</span><span class="sxs-lookup"><span data-stu-id="4f7c6-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="4f7c6-129">使用以下有关操作系统配置的说明。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="4f7c6-130">使用 Linux 容器的 Windows</span><span class="sxs-lookup"><span data-stu-id="4f7c6-130">Windows using Linux containers</span></span>

<span data-ttu-id="4f7c6-131">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="4f7c6-132">在上述命令中，将替换 `{ password here }` 为密码。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="4f7c6-133">在命令行界面中使用为 HTTPS 配置的 ASP.NET Core 运行容器映像：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="4f7c6-134">使用 [PowerShell](/powershell/scripting/overview)时，请将替换 `%USERPROFILE%` 为 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="4f7c6-135">密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="4f7c6-136">注意：在这种情况下，证书必须是 `.pfx` 文件。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="4f7c6-137">`.crt` `.key` 示例容器不支持使用带有或不带密码的或文件。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="4f7c6-138">例如，在指定文件时 `.crt` ，容器可能会返回错误消息，如 "服务器模式 SSL 必须使用具有关联私钥的证书"。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="4f7c6-139">当使用 [WSL](/windows/wsl/about)时，验证装载路径以确保证书正确加载。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="4f7c6-140">macOS 或 Linux</span><span class="sxs-lookup"><span data-stu-id="4f7c6-140">macOS or Linux</span></span>

<span data-ttu-id="4f7c6-141">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="4f7c6-142">`dotnet dev-certs https --trust` 仅在 macOS 和 Windows 上受支持。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="4f7c6-143">你需要以分发的支持方式信任 Linux 上的证书。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="4f7c6-144">可能需要在浏览器中信任该证书。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="4f7c6-145">在上述命令中，将替换 `{ password here }` 为密码。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="4f7c6-146">使用为 HTTPS 配置的 ASP.NET Core 运行容器映像：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="4f7c6-147">密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="4f7c6-148">使用 Windows 容器的 windows</span><span class="sxs-lookup"><span data-stu-id="4f7c6-148">Windows using Windows containers</span></span>

<span data-ttu-id="4f7c6-149">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="4f7c6-150">在上述命令中，将替换 `{ password here }` 为密码。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="4f7c6-151">使用 [PowerShell](/powershell/scripting/overview)时，请将替换 `%USERPROFILE%` 为 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="4f7c6-152">使用为 HTTPS 配置的 ASP.NET Core 运行容器映像：</span><span class="sxs-lookup"><span data-stu-id="4f7c6-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="4f7c6-153">密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="4f7c6-154">使用 [PowerShell](/powershell/scripting/overview)时，请将替换 `%USERPROFILE%` 为 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="4f7c6-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
