---
title: 用于 ASP.NET Core Blazor 的工具
author: guardrex
description: 了解可用于构建 Blazor 应用程序的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394455"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="8c668-103">用于 ASP.NET Core Blazor 的工具</span><span class="sxs-lookup"><span data-stu-id="8c668-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="8c668-104">安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="8c668-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="8c668-105">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="8c668-105">Create a new project.</span></span>

1. <span data-ttu-id="8c668-106">选择“Blazor 应用”。</span><span class="sxs-lookup"><span data-stu-id="8c668-106">Select **Blazor App**.</span></span> <span data-ttu-id="8c668-107">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="8c668-107">Select **Next**.</span></span>

1. <span data-ttu-id="8c668-108">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="8c668-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8c668-109">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="8c668-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="8c668-110">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8c668-110">Select **Create**.</span></span>

1. <span data-ttu-id="8c668-111">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="8c668-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="8c668-112">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="8c668-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="8c668-113">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8c668-113">Select **Create**.</span></span>

   <span data-ttu-id="8c668-114">对于托管的 Blazor WebAssembly 体验，选择“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="8c668-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="8c668-115">若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="8c668-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="8c668-116">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="8c668-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="8c668-117">有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。</span><span class="sxs-lookup"><span data-stu-id="8c668-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="8c668-118">执行托管应用 Blazor WebAssembly 时，请从解决方案的 `Server` 项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="8c668-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="8c668-119">安装最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="8c668-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="8c668-120">如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：</span><span class="sxs-lookup"><span data-stu-id="8c668-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="8c668-121">安装最新版本的 [Visual Studio Code](https://code.visualstudio.com)。</span><span class="sxs-lookup"><span data-stu-id="8c668-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="8c668-122">安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="8c668-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="8c668-123">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8c668-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="8c668-124">对于托管的 Blazor WebAssembly 体验，请向命令添加托管选项 (`-ho` 或 `--hosted`) 选项：</span><span class="sxs-lookup"><span data-stu-id="8c668-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="8c668-125">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8c668-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="8c668-126">若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="8c668-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="8c668-127">在 Visual Studio Code 中打开 `WebApplication1` 文件夹，</span><span class="sxs-lookup"><span data-stu-id="8c668-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="8c668-128">IDE 要求添加资产以用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="8c668-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="8c668-129">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="8c668-129">Select **Yes**.</span></span>

   <span data-ttu-id="8c668-130">**托管的 Blazor WebAssembly 启动和任务配置**</span><span class="sxs-lookup"><span data-stu-id="8c668-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="8c668-131">对于托管的 Blazor WebAssembly 解决方案，请将包含 `launch.json` 和 `tasks.json` 文件的 `.vscode` 文件夹添加（或移动）到解决方案的父文件夹中，父文件夹包含常见的项目文件夹名称 `Client`、`Server` 和 `Shared`。</span><span class="sxs-lookup"><span data-stu-id="8c668-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="8c668-132">更新或确认 `launch.json` 和 `tasks.json` 文件中的配置是否从 `Server` 项目执行托管应用 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="8c668-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="8c668-133">**`.vscode/launch.json`** （`launch` 配置）：</span><span class="sxs-lookup"><span data-stu-id="8c668-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="8c668-134">在当前工作目录 (`cwd`) 的前面配置中，`{SERVER APP FOLDER}` 占位符是 `Server` 项目的文件夹，通常为“`Server`”。</span><span class="sxs-lookup"><span data-stu-id="8c668-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="8c668-135">如果使用 Microsoft Edge 并且系统上未安装 Google Chrome，请在配置中添加额外属性 `"browser": "edge"`。</span><span class="sxs-lookup"><span data-stu-id="8c668-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="8c668-136">`Server` 项目文件夹的示例，该示例将生成 Microsoft Edge（而不是默认浏览器 Google Chrome）作为调试运行的浏览器：</span><span class="sxs-lookup"><span data-stu-id="8c668-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="8c668-137">**`.vscode/tasks.json`** （[`dotnet` 命令](/dotnet/core/tools/dotnet)参数）：</span><span class="sxs-lookup"><span data-stu-id="8c668-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="8c668-138">在上述参数中：</span><span class="sxs-lookup"><span data-stu-id="8c668-138">In the preceding argument:</span></span>

   * <span data-ttu-id="8c668-139">`{SERVER APP FOLDER}` 占位符是 `Server` 项目的文件夹，通常为“`Server`”。</span><span class="sxs-lookup"><span data-stu-id="8c668-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="8c668-140">`{PROJECT NAME}` 占位符是应用的名称，在 [Blazor 项目模板](xref:blazor/project-structure)生成的应用中，该名称通常基于解决方案的名称后跟“`.Server`”。</span><span class="sxs-lookup"><span data-stu-id="8c668-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the [Blazor project template](xref:blazor/project-structure).</span></span>

   <span data-ttu-id="8c668-141">来自[将 SignalR 与 Blazor WebAssembly 应用配合使用的教程](xref:tutorials/signalr-blazor)的以下示例使用项目文件夹名称 `Server` 和项目名称 `BlazorWebAssemblySignalRApp.Server`：</span><span class="sxs-lookup"><span data-stu-id="8c668-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="8c668-142">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="8c668-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="8c668-143">信任开发证书</span><span class="sxs-lookup"><span data-stu-id="8c668-143">Trust a development certificate</span></span>

<span data-ttu-id="8c668-144">Linux 上没有用于信任证书的集中途径。</span><span class="sxs-lookup"><span data-stu-id="8c668-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="8c668-145">通常采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="8c668-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="8c668-146">在浏览器的排除列表中排除应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="8c668-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="8c668-147">信任 `localhost` 的所有自签名证书。</span><span class="sxs-lookup"><span data-stu-id="8c668-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="8c668-148">将证书添加到浏览器的受信任证书列表。</span><span class="sxs-lookup"><span data-stu-id="8c668-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="8c668-149">有关详细信息，请参阅浏览器制造商和 Linux 发行版提供的指南。</span><span class="sxs-lookup"><span data-stu-id="8c668-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="8c668-150">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="8c668-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="8c668-151">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="8c668-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="8c668-152">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="8c668-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="8c668-153">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="8c668-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="8c668-154">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="8c668-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="8c668-155">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="8c668-155">Select **Next**.</span></span>

   <span data-ttu-id="8c668-156">若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="8c668-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="8c668-157">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="8c668-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="8c668-158">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="8c668-158">Select **Next**.</span></span>

1. <span data-ttu-id="8c668-159">对于托管的 Blazor WebAssembly 体验，选择“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="8c668-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="8c668-160">在“项目名称”字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="8c668-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="8c668-161">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8c668-161">Select **Create**.</span></span>

1. <span data-ttu-id="8c668-162">选择“运行” > “启动而不调试”以不使用调试程序运行应用 。</span><span class="sxs-lookup"><span data-stu-id="8c668-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="8c668-163">使用“运行” > “开始调试”运行应用，或者使用“运行 (&#9654;)”按钮通过调试程序运行应用。 </span><span class="sxs-lookup"><span data-stu-id="8c668-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="8c668-164">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="8c668-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="8c668-165">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="8c668-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="8c668-166">有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。</span><span class="sxs-lookup"><span data-stu-id="8c668-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="8c668-167">执行托管应用 Blazor WebAssembly 时，请从解决方案的 `Server` 项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="8c668-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="8c668-168">使用适用于跨平台 Blazor 开发的 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c668-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="8c668-169">[Visual Studio Code](https://code.visualstudio.com/) 是一个开源的跨平台集成开发环境 (IDE)，可用于开发 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="8c668-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="8c668-170">使用 .NET CLI 创建新的 Blazor 应用，来使用 Visual Studio Code 进行开发。</span><span class="sxs-lookup"><span data-stu-id="8c668-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="8c668-171">有关详细信息，请选择[本文的 Linux 版本](?pivots=linux)。</span><span class="sxs-lookup"><span data-stu-id="8c668-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="8c668-172">Blazor 模板选项</span><span class="sxs-lookup"><span data-stu-id="8c668-172">Blazor template options</span></span>

<span data-ttu-id="8c668-173">Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型（共两个）创建新应用。</span><span class="sxs-lookup"><span data-stu-id="8c668-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="8c668-174">模板用于创建新的 Blazor 项目和解决方案，而不考虑您选择用于 Blazor 开发的工具（Visual Studio、Visual Studio for Mac、Visual Studio Code 还是 .NET CLI）：</span><span class="sxs-lookup"><span data-stu-id="8c668-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="8c668-175">Blazor WebAssembly 项目模板：`blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="8c668-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="8c668-176">Blazor Server项目模板：`blazorserver`</span><span class="sxs-lookup"><span data-stu-id="8c668-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="8c668-177">有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="8c668-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="8c668-178">有关 Blazor 项目模板的更多信息，请参阅“<xref:blazor/project-structure>”。</span><span class="sxs-lookup"><span data-stu-id="8c668-178">For more information on Blazor project templates, see <xref:blazor/project-structure>.</span></span>

<span data-ttu-id="8c668-179">通过将 help 选项（`-h` 或 `--help`）传递给命令行界面中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 命令，可使用模板选项：</span><span class="sxs-lookup"><span data-stu-id="8c668-179">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a><span data-ttu-id="8c668-180">其他资源</span><span class="sxs-lookup"><span data-stu-id="8c668-180">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
