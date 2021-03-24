---
title: ASP.NET Core 的 Docker 映像
author: rick-anderson
description: 了解如何使用 Docker 注册表中发布的 ASP.NET Core Docker 映像。 拉取并生成你自己的映像。
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 32e721035df8bd9e746ad4db6bb2753c358f3dac
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413491"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="c0f80-104">ASP.NET Core 的 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="c0f80-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="c0f80-105">本教程演示如何在 Docker 容器中运行 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="c0f80-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="c0f80-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="c0f80-107">了解 ASP.NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="c0f80-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="c0f80-108">下载 ASP.NET Core 示例应用</span><span class="sxs-lookup"><span data-stu-id="c0f80-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="c0f80-109">本地运行示例应用</span><span class="sxs-lookup"><span data-stu-id="c0f80-109">Run the sample app locally</span></span>
> * <span data-ttu-id="c0f80-110">在 Linux 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="c0f80-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="c0f80-111">在 Windows 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="c0f80-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="c0f80-112">手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="c0f80-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="c0f80-113">ASP.NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="c0f80-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="c0f80-114">在本教程中，你下载 ASP.NET Core 示例应用并在 Docker 容器中运行它。</span><span class="sxs-lookup"><span data-stu-id="c0f80-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="c0f80-115">此示例适用于 Linux 和 Windows 容器。</span><span class="sxs-lookup"><span data-stu-id="c0f80-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="c0f80-116">示例 Dockerfile 使用 [Docker 多阶段构建功能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)在不同的容器中生成和运行。</span><span class="sxs-lookup"><span data-stu-id="c0f80-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="c0f80-117">生成和运行容器是由 Microsoft 从 Docker 中心提供的映像中创建的：</span><span class="sxs-lookup"><span data-stu-id="c0f80-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="c0f80-118">此示例将此映像用于生成应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="c0f80-119">此映像包含带有命令行工具 (CLI) 的 .NET SDK。</span><span class="sxs-lookup"><span data-stu-id="c0f80-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="c0f80-120">此映像对本地开发、调试和单元测试进行了优化。</span><span class="sxs-lookup"><span data-stu-id="c0f80-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="c0f80-121">为开发和编译而安装的工具使映像变得相对较大。</span><span class="sxs-lookup"><span data-stu-id="c0f80-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="c0f80-122">此示例将此映像用于生成应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="c0f80-123">此映像包含带有命令行工具 (CLI) 的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="c0f80-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="c0f80-124">此映像对本地开发、调试和单元测试进行了优化。</span><span class="sxs-lookup"><span data-stu-id="c0f80-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="c0f80-125">为开发和编译而安装的工具使映像变得相对较大。</span><span class="sxs-lookup"><span data-stu-id="c0f80-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

   <span data-ttu-id="c0f80-126">此示例将此映像用于运行应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="c0f80-127">此映像包含 ASP.NET Core 运行时和库，并针对在生产中运行应用进行了优化。</span><span class="sxs-lookup"><span data-stu-id="c0f80-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="c0f80-128">此映像专为部署和应用启动的速度而设计，相对较小，因此优化了从 Docker 注册表到 Docker 主机的网络性能。</span><span class="sxs-lookup"><span data-stu-id="c0f80-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="c0f80-129">仅将运行应用所需的二进制文件和内容复制到容器。</span><span class="sxs-lookup"><span data-stu-id="c0f80-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="c0f80-130">已准备运行内容，以此实现从 `docker run` 到应用启动的最快时间。</span><span class="sxs-lookup"><span data-stu-id="c0f80-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="c0f80-131">Docker 模型中不需要动态代码编译。</span><span class="sxs-lookup"><span data-stu-id="c0f80-131">Dynamic code compilation isn't needed in the Docker model.</span></span>
   
::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

   <span data-ttu-id="c0f80-132">此示例将此映像用于运行应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-132">The sample uses this image for running the app.</span></span> <span data-ttu-id="c0f80-133">此映像包含 ASP.NET Core 运行时和库，并针对在生产中运行应用进行了优化。</span><span class="sxs-lookup"><span data-stu-id="c0f80-133">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="c0f80-134">此映像专为部署和应用启动的速度而设计，相对较小，因此优化了从 Docker 注册表到 Docker 主机的网络性能。</span><span class="sxs-lookup"><span data-stu-id="c0f80-134">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="c0f80-135">仅将运行应用所需的二进制文件和内容复制到容器。</span><span class="sxs-lookup"><span data-stu-id="c0f80-135">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="c0f80-136">已准备运行内容，以此实现从 `docker run` 到应用启动的最快时间。</span><span class="sxs-lookup"><span data-stu-id="c0f80-136">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="c0f80-137">Docker 模型中不需要动态代码编译。</span><span class="sxs-lookup"><span data-stu-id="c0f80-137">Dynamic code compilation isn't needed in the Docker model.</span></span>
   
::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="c0f80-138">先决条件</span><span class="sxs-lookup"><span data-stu-id="c0f80-138">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="c0f80-139">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="c0f80-139">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="c0f80-140">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="c0f80-140">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="c0f80-141">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="c0f80-141">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="c0f80-142">Docker 客户端 18.03 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c0f80-142">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="c0f80-143">Linux 分布</span><span class="sxs-lookup"><span data-stu-id="c0f80-143">Linux distributions</span></span>
    * [<span data-ttu-id="c0f80-144">CentOS</span><span class="sxs-lookup"><span data-stu-id="c0f80-144">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="c0f80-145">Debian</span><span class="sxs-lookup"><span data-stu-id="c0f80-145">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="c0f80-146">Fedora</span><span class="sxs-lookup"><span data-stu-id="c0f80-146">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="c0f80-147">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="c0f80-147">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="c0f80-148">macOS</span><span class="sxs-lookup"><span data-stu-id="c0f80-148">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="c0f80-149">Windows</span><span class="sxs-lookup"><span data-stu-id="c0f80-149">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="c0f80-150">Git</span><span class="sxs-lookup"><span data-stu-id="c0f80-150">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="c0f80-151">下载示例应用</span><span class="sxs-lookup"><span data-stu-id="c0f80-151">Download the sample app</span></span>

* <span data-ttu-id="c0f80-152">通过克隆 [.NET Docker 存储库](https://github.com/dotnet/dotnet-docker)下载示例：</span><span class="sxs-lookup"><span data-stu-id="c0f80-152">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="c0f80-153">本地运行应用</span><span class="sxs-lookup"><span data-stu-id="c0f80-153">Run the app locally</span></span>

* <span data-ttu-id="c0f80-154">导航到 dotnet-docker/samples/aspnetapp/aspnetapp  下的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="c0f80-154">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="c0f80-155">运行以下命令以本地生成并运行应用：</span><span class="sxs-lookup"><span data-stu-id="c0f80-155">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="c0f80-156">在浏览器中转到 `http://localhost:5000` 以测试应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-156">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="c0f80-157">在命令提示符处按 Ctrl+C 以停止应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-157">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="c0f80-158">在 Linux 容器中运行</span><span class="sxs-lookup"><span data-stu-id="c0f80-158">Run in a Linux container</span></span>

* <span data-ttu-id="c0f80-159">在 Docker 客户端中，[切换到 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="c0f80-159">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="c0f80-160">导航到 dotnet-docker/samples/aspnetapp  下的 Dockerfile 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c0f80-160">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="c0f80-161">运行以下命令以在 Docker 中生成并运行示例：</span><span class="sxs-lookup"><span data-stu-id="c0f80-161">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="c0f80-162">`build` 命令参数：</span><span class="sxs-lookup"><span data-stu-id="c0f80-162">The `build` command arguments:</span></span>
  * <span data-ttu-id="c0f80-163">将映像命名为 aspnetapp。</span><span class="sxs-lookup"><span data-stu-id="c0f80-163">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="c0f80-164">在当前文件夹（末尾的句点）中查找 Dockerfile。</span><span class="sxs-lookup"><span data-stu-id="c0f80-164">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="c0f80-165">运行命令参数：</span><span class="sxs-lookup"><span data-stu-id="c0f80-165">The run command arguments:</span></span>
  * <span data-ttu-id="c0f80-166">分配伪 TTY，即使未附加也使其保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c0f80-166">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="c0f80-167">（与 `--interactive --tty` 的效果相同。）</span><span class="sxs-lookup"><span data-stu-id="c0f80-167">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="c0f80-168">容器在退出时自动删除。</span><span class="sxs-lookup"><span data-stu-id="c0f80-168">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="c0f80-169">将本地计算机上的端口 5000 映射到容器中的端口 80。</span><span class="sxs-lookup"><span data-stu-id="c0f80-169">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="c0f80-170">将容器命名为 aspnetcore_sample。</span><span class="sxs-lookup"><span data-stu-id="c0f80-170">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="c0f80-171">指定 aspnetapp 映像。</span><span class="sxs-lookup"><span data-stu-id="c0f80-171">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="c0f80-172">在浏览器中转到 `http://localhost:5000` 以测试应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-172">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="c0f80-173">在 Windows 容器中运行</span><span class="sxs-lookup"><span data-stu-id="c0f80-173">Run in a Windows container</span></span>

* <span data-ttu-id="c0f80-174">在 Docker 客户端中，[切换到 Windows 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="c0f80-174">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="c0f80-175">导航到 `dotnet-docker/samples/aspnetapp` 下的 docker 文件文件夹。</span><span class="sxs-lookup"><span data-stu-id="c0f80-175">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="c0f80-176">运行以下命令以在 Docker 中生成并运行示例：</span><span class="sxs-lookup"><span data-stu-id="c0f80-176">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="c0f80-177">对于 Windows 容器，你需要容器的 IP 地址（浏览到 `http://localhost:5000` 不起作用）：</span><span class="sxs-lookup"><span data-stu-id="c0f80-177">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="c0f80-178">打开另一个命令提示符。</span><span class="sxs-lookup"><span data-stu-id="c0f80-178">Open up another command prompt.</span></span>
  * <span data-ttu-id="c0f80-179">运行 `docker ps` 以查看正在运行的容器。</span><span class="sxs-lookup"><span data-stu-id="c0f80-179">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="c0f80-180">验证其中是否包含“aspnetcore_sample”容器。</span><span class="sxs-lookup"><span data-stu-id="c0f80-180">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="c0f80-181">运行 `docker exec aspnetcore_sample ipconfig` 以显示容器的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c0f80-181">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="c0f80-182">该命令的输出如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c0f80-182">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="c0f80-183">将容器 IPv4 地址（例如，172.29.245.43）复制并粘贴到浏览器地址栏以测试应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-183">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="c0f80-184">手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="c0f80-184">Build and deploy manually</span></span>

<span data-ttu-id="c0f80-185">在某些情况下，你可能希望通过复制运行时所需的应用资产来将应用部署到容器中。</span><span class="sxs-lookup"><span data-stu-id="c0f80-185">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="c0f80-186">本部分演示如何手动进行部署。</span><span class="sxs-lookup"><span data-stu-id="c0f80-186">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="c0f80-187">导航到 dotnet-docker/samples/aspnetapp/aspnetapp  下的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="c0f80-187">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="c0f80-188">运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令：</span><span class="sxs-lookup"><span data-stu-id="c0f80-188">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="c0f80-189">命令参数：</span><span class="sxs-lookup"><span data-stu-id="c0f80-189">The command arguments:</span></span>
  * <span data-ttu-id="c0f80-190">在发布模式（默认为调试模式）下生成应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-190">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="c0f80-191">在“已发布”文件夹中创建资产。</span><span class="sxs-lookup"><span data-stu-id="c0f80-191">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="c0f80-192">运行应用。</span><span class="sxs-lookup"><span data-stu-id="c0f80-192">Run the app.</span></span>

  * <span data-ttu-id="c0f80-193">Windows:</span><span class="sxs-lookup"><span data-stu-id="c0f80-193">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="c0f80-194">Linux：</span><span class="sxs-lookup"><span data-stu-id="c0f80-194">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="c0f80-195">浏览到 `http://localhost:5000` 以查看主页。</span><span class="sxs-lookup"><span data-stu-id="c0f80-195">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="c0f80-196">要在 Docker 容器中使用手动发布的应用，请创建新的 Dockerfile，并使用 `docker build .` 命令构建映像。</span><span class="sxs-lookup"><span data-stu-id="c0f80-196">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build an image.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="c0f80-197">若要查看新映像，请使用 `docker images` 命令。</span><span class="sxs-lookup"><span data-stu-id="c0f80-197">To see the new image use the `docker images` command.</span></span>

### <a name="the-dockerfile"></a><span data-ttu-id="c0f80-198">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="c0f80-198">The Dockerfile</span></span>

<span data-ttu-id="c0f80-199">下面是先前运行的 `docker build` 命令使用的 Dockerfile  。</span><span class="sxs-lookup"><span data-stu-id="c0f80-199">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="c0f80-200">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="c0f80-200">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="c0f80-201">在前面的 Dockerfile 中，将 `*.csproj` 文件作为不同的层进行复制和还原 。</span><span class="sxs-lookup"><span data-stu-id="c0f80-201">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="c0f80-202">当 `docker build` 命令生成映像时，它将使用内置缓存。</span><span class="sxs-lookup"><span data-stu-id="c0f80-202">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="c0f80-203">如果自上次运行 `docker build` 命令后，`*.csproj` 文件未发生更改，则 `dotnet restore` 命令无需再次运行。</span><span class="sxs-lookup"><span data-stu-id="c0f80-203">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="c0f80-204">但是，将重复使用相应 `dotnet restore` 层的内置缓存。</span><span class="sxs-lookup"><span data-stu-id="c0f80-204">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="c0f80-205">有关详细信息，请参阅[编写 Dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。</span><span class="sxs-lookup"><span data-stu-id="c0f80-205">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="c0f80-206">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="c0f80-206">The Dockerfile</span></span>

<span data-ttu-id="c0f80-207">下面是先前运行的 `docker build` 命令使用的 Dockerfile  。</span><span class="sxs-lookup"><span data-stu-id="c0f80-207">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="c0f80-208">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="c0f80-208">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="c0f80-209">如前面的 Dockerfile 中所述，将 `*.csproj` 文件作为不同的层进行复制和还原  。</span><span class="sxs-lookup"><span data-stu-id="c0f80-209">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="c0f80-210">当 `docker build` 命令生成映像时，它将使用内置缓存。</span><span class="sxs-lookup"><span data-stu-id="c0f80-210">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="c0f80-211">如果自上次运行 `docker build` 命令后，`*.csproj` 文件未发生更改，则 `dotnet restore` 命令无需再次运行。</span><span class="sxs-lookup"><span data-stu-id="c0f80-211">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="c0f80-212">但是，将重复使用相应 `dotnet restore` 层的内置缓存。</span><span class="sxs-lookup"><span data-stu-id="c0f80-212">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="c0f80-213">有关详细信息，请参阅[编写 Dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。</span><span class="sxs-lookup"><span data-stu-id="c0f80-213">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="c0f80-214">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="c0f80-214">The Dockerfile</span></span>

<span data-ttu-id="c0f80-215">下面是先前运行的 `docker build` 命令使用的 Dockerfile  。</span><span class="sxs-lookup"><span data-stu-id="c0f80-215">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="c0f80-216">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="c0f80-216">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c0f80-217">其他资源</span><span class="sxs-lookup"><span data-stu-id="c0f80-217">Additional resources</span></span>

* [<span data-ttu-id="c0f80-218">Docker 生成命令</span><span class="sxs-lookup"><span data-stu-id="c0f80-218">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="c0f80-219">Docker 运行命令</span><span class="sxs-lookup"><span data-stu-id="c0f80-219">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="c0f80-220">[ASP.NET Core Docker 示例](https://github.com/dotnet/dotnet-docker)（本教程中使用的示例。）</span><span class="sxs-lookup"><span data-stu-id="c0f80-220">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="c0f80-221">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="c0f80-221">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="c0f80-222">使用 Visual Studio Docker 工具</span><span class="sxs-lookup"><span data-stu-id="c0f80-222">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="c0f80-223">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="c0f80-223">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="c0f80-224">使用 Docker 和小型容器的 GC</span><span class="sxs-lookup"><span data-stu-id="c0f80-224">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="c0f80-225">后续步骤</span><span class="sxs-lookup"><span data-stu-id="c0f80-225">Next steps</span></span>

<span data-ttu-id="c0f80-226">包含示例应用的 Git 存储库还包括文档。</span><span class="sxs-lookup"><span data-stu-id="c0f80-226">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="c0f80-227">有关存储库中可用资源的概述，请参阅[自述文件](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md)。</span><span class="sxs-lookup"><span data-stu-id="c0f80-227">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="c0f80-228">特别是，了解如何实现 HTTPS：</span><span class="sxs-lookup"><span data-stu-id="c0f80-228">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c0f80-229">使用 Docker over HTTPS 开发 ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="c0f80-229">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)
