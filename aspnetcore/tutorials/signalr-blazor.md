---
title: 结合使用 ASP.NET Core SignalR 和 Blazor
author: guardrex
description: 创建结合使用 ASP.NET Core SignalR 和 Blazor 的聊天应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 2e0ed3ee59f90ada3a46353a63fa384d664b4afa
ms.sourcegitcommit: 8f4313c762a0b7c30e5ce328b3afe146838f53d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107591492"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="5c6cc-103">结合使用 ASP.NET Core SignalR 和 Blazor</span><span class="sxs-lookup"><span data-stu-id="5c6cc-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="5c6cc-104">本教程介绍结合使用 SignalR 和 Blazor 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="5c6cc-105">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5c6cc-106">创建 Blazor 项目</span><span class="sxs-lookup"><span data-stu-id="5c6cc-106">Create a Blazor project</span></span>
> * <span data-ttu-id="5c6cc-107">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="5c6cc-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="5c6cc-108">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="5c6cc-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="5c6cc-109">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="5c6cc-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="5c6cc-110">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="5c6cc-110">Add Razor component code for chat</span></span>

<span data-ttu-id="5c6cc-111">在本教程结束时，你将拥有一个正常运行的聊天应用。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="5c6cc-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5c6cc-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5c6cc-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="5c6cc-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c6cc-115">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.8 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="5c6cc-118">Visual Studio for Mac 8.8 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5c6cc-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c6cc-121">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="5c6cc-124">Visual Studio for Mac 版本 8.6 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5c6cc-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="5c6cc-126">创建托管 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="5c6cc-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="5c6cc-127">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5c6cc-129">需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5c6cc-130">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="5c6cc-131">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-131">Create a new project.</span></span>

1. <span data-ttu-id="5c6cc-132">选择“Blazor 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="5c6cc-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="5c6cc-133">在“项目名称”字段中键入 `BlazorWebAssemblySignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="5c6cc-134">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5c6cc-135">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-135">Select **Create**.</span></span>

1. <span data-ttu-id="5c6cc-136">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="5c6cc-137">在“高级”下选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="5c6cc-138">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5c6cc-140">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="5c6cc-141">`-ho|--hosted` 选项将创建托管 Blazor WebAssembly 解决方案。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="5c6cc-142">有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="5c6cc-143">`-o|--output` 选项为解决方案创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="5c6cc-144">如果已为解决方案创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建解决方案的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="5c6cc-145">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="5c6cc-146">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="5c6cc-147">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-149">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="5c6cc-150">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="5c6cc-151">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="5c6cc-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="5c6cc-152">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="5c6cc-153">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-153">Select **Next**.</span></span>

1. <span data-ttu-id="5c6cc-154">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="5c6cc-155">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="5c6cc-156">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-156">Select **Next**.</span></span>

1. <span data-ttu-id="5c6cc-157">在“项目名称”字段中，将应用命名为 `BlazorWebAssemblySignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="5c6cc-158">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-158">Select **Create**.</span></span>

   <span data-ttu-id="5c6cc-159">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="5c6cc-160">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="5c6cc-161">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c6cc-163">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="5c6cc-164">`-ho|--hosted` 选项将创建托管 Blazor WebAssembly 解决方案。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="5c6cc-165">`-o|--output` 选项为解决方案创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="5c6cc-166">如果已为解决方案创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建解决方案的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="5c6cc-167">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="5c6cc-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="5c6cc-169">在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c6cc-170">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c6cc-171">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="5c6cc-172">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="5c6cc-173">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="5c6cc-174">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-174">Select **Install**.</span></span>

1. <span data-ttu-id="5c6cc-175">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="5c6cc-176">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="5c6cc-178">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="5c6cc-179">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-181">在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c6cc-182">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c6cc-183">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="5c6cc-184">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="5c6cc-185">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="5c6cc-186">选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="5c6cc-187">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-188">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c6cc-189">在解决方案文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="5c6cc-190">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="5c6cc-191">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="5c6cc-191">Add a SignalR hub</span></span>

<span data-ttu-id="5c6cc-192">在 `BlazorWebAssemblySignalRApp.Server` 项目中，创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-192">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="5c6cc-193">为 SignalR 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="5c6cc-193">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="5c6cc-194">在 `BlazorWebAssemblySignalRApp.Server` 项目中打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-194">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="5c6cc-195">将 `ChatHub` 类的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-195">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-196">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-196">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="5c6cc-197">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-197">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="5c6cc-198">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-198">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="5c6cc-199">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-199">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-200">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-200">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="5c6cc-201">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-201">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="5c6cc-202">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-202">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="5c6cc-203">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-203">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="5c6cc-204">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="5c6cc-204">Add Razor component code for chat</span></span>

1. <span data-ttu-id="5c6cc-205">在 `BlazorWebAssemblySignalRApp.Client` 项目中打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-205">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-206">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-206">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-207">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-207">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="5c6cc-208">运行应用</span><span class="sxs-lookup"><span data-stu-id="5c6cc-208">Run the app</span></span>

<span data-ttu-id="5c6cc-209">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-209">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-210">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5c6cc-211">在“解决方案资源管理器”中，选择 `BlazorWebAssemblySignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-211">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="5c6cc-212">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-212">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c6cc-213">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-213">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-214">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-214">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-215">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-215">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-217">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-217">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-218">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c6cc-219">有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-219">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="5c6cc-220">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-220">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c6cc-221">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-221">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-222">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-222">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-223">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-223">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-225">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-225">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-226">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-227">在“解决方案”边栏中，选择 `BlazorWebAssemblySignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-227">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="5c6cc-228">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-228">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c6cc-229">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-229">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-230">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-230">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-231">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-231">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-233">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-233">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-234">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-234">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="5c6cc-235">在解决方案文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-235">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="5c6cc-236">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-236">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-237">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-237">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-238">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-238">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-240">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-240">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="5c6cc-241">创建 Blazor Server 应用</span><span class="sxs-lookup"><span data-stu-id="5c6cc-241">Create a Blazor Server app</span></span>

<span data-ttu-id="5c6cc-242">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-242">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-243">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5c6cc-244">需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-244">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5c6cc-245">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-245">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="5c6cc-246">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-246">Create a new project.</span></span>

1. <span data-ttu-id="5c6cc-247">选择“Blazor 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="5c6cc-247">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="5c6cc-248">在“项目名称”字段中键入 `BlazorServerSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-248">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="5c6cc-249">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-249">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5c6cc-250">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-250">Select **Create**.</span></span>

1. <span data-ttu-id="5c6cc-251">选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-251">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="5c6cc-252">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-252">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5c6cc-254">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-254">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="5c6cc-255">`-o|--output` 选项为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-255">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="5c6cc-256">如果已为项目创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建项目的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-256">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="5c6cc-257">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-257">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="5c6cc-258">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-258">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="5c6cc-259">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-259">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="5c6cc-260">有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-260">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-261">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-262">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-262">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="5c6cc-263">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-263">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="5c6cc-264">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="5c6cc-264">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="5c6cc-265">选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-265">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="5c6cc-266">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-266">Select **Next**.</span></span>

1. <span data-ttu-id="5c6cc-267">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-267">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="5c6cc-268">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-268">Select **Next**.</span></span>

1. <span data-ttu-id="5c6cc-269">在“项目名称”字段中，将应用命名为 `BlazorServerSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-269">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="5c6cc-270">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-270">Select **Create**.</span></span>

   <span data-ttu-id="5c6cc-271">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-271">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="5c6cc-272">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-272">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="5c6cc-273">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-273">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-274">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-274">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c6cc-275">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-275">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="5c6cc-276">`-o|--output` 选项为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-276">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="5c6cc-277">如果已为项目创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建项目的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-277">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="5c6cc-278">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="5c6cc-278">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-279">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="5c6cc-280">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-280">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c6cc-281">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-281">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c6cc-282">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-282">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="5c6cc-283">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-283">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="5c6cc-284">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-284">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="5c6cc-285">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-285">Select **Install**.</span></span>

1. <span data-ttu-id="5c6cc-286">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-286">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="5c6cc-287">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-287">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-288">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-288">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="5c6cc-289">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-289">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="5c6cc-290">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-290">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-291">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-292">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-292">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c6cc-293">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-293">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c6cc-294">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-294">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="5c6cc-295">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-295">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="5c6cc-296">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-296">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="5c6cc-297">选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-297">Select **Add Package**.</span></span>

1. <span data-ttu-id="5c6cc-298">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-298">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-299">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-299">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c6cc-300">在项目文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-300">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="5c6cc-301">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-301">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="5c6cc-302">添加 System.Text.Encodings.Web 包</span><span class="sxs-lookup"><span data-stu-id="5c6cc-302">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="5c6cc-303">本部分仅适用于 ASP.NET Core 版本 3.x 的应用。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-303">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="5c6cc-304">由于在 ASP.NET Core 3.x 应用中使用 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x 时出现包解析问题，项目需要 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-304">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="5c6cc-305">在未来的 .NET 5 修补程序版本中，将解决基础问题。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-305">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="5c6cc-306">有关详细信息，请参阅 [System.Text.Json 定义无依赖项的 netcoreapp3.0（dotnet/运行时 #45560）](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-306">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="5c6cc-307">若要将 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 添加到项目，请按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-307">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-308">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="5c6cc-309">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-309">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c6cc-310">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-310">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c6cc-311">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-311">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="5c6cc-312">在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-312">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="5c6cc-313">选择与正在使用的共享框架相匹配的包版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-313">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="5c6cc-314">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-314">Select **Install**.</span></span>

1. <span data-ttu-id="5c6cc-315">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-315">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="5c6cc-316">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-316">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-317">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="5c6cc-318">在“集成终端”（工具栏上的“视图”>“终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-318">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="5c6cc-319">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-319">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-320">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-320">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-321">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-321">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c6cc-322">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-322">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c6cc-323">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-323">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="5c6cc-324">在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包旁边的复选框，选择与正在使用的共享框架相匹配的包的正确版本，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-324">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="5c6cc-325">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-326">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c6cc-327">在项目文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="5c6cc-328">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="5c6cc-329">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="5c6cc-329">Add a SignalR hub</span></span>

<span data-ttu-id="5c6cc-330">创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-330">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="5c6cc-331">为 SignalR 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="5c6cc-331">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="5c6cc-332">打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-332">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="5c6cc-333">将 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> 和 `ChatHub` 类的命名空间添加到文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-333">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-334">将响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-334">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="5c6cc-335">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-335">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="5c6cc-336">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-336">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="5c6cc-337">在映射 Blazor 中心的终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-337">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-338">将响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-338">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="5c6cc-339">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-339">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="5c6cc-340">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-340">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="5c6cc-341">在映射 Blazor 中心的终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-341">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="5c6cc-342">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="5c6cc-342">Add Razor component code for chat</span></span>

1. <span data-ttu-id="5c6cc-343">打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-343">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-344">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-344">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="5c6cc-345">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-345">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="5c6cc-346">运行应用</span><span class="sxs-lookup"><span data-stu-id="5c6cc-346">Run the app</span></span>

<span data-ttu-id="5c6cc-347">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-347">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c6cc-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c6cc-348">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5c6cc-349">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-349">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c6cc-350">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-350">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-351">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-351">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-352">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-352">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-354">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-354">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c6cc-355">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c6cc-355">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5c6cc-356">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-356">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c6cc-357">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-357">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-358">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-358">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-359">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-359">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-361">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-361">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c6cc-362">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c6cc-362">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c6cc-363">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-363">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c6cc-364">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-364">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-365">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-365">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-366">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-366">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-368">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-368">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c6cc-369">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c6cc-369">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="5c6cc-370">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-370">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="5c6cc-371">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-371">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c6cc-372">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="5c6cc-372">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c6cc-373">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-373">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="5c6cc-375">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-375">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="5c6cc-376">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5c6cc-376">Next steps</span></span>

<span data-ttu-id="5c6cc-377">在本教程中，你了解了如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-377">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5c6cc-378">创建 Blazor 项目</span><span class="sxs-lookup"><span data-stu-id="5c6cc-378">Create a Blazor project</span></span>
> * <span data-ttu-id="5c6cc-379">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="5c6cc-379">Add the SignalR client library</span></span>
> * <span data-ttu-id="5c6cc-380">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="5c6cc-380">Add a SignalR hub</span></span>
> * <span data-ttu-id="5c6cc-381">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="5c6cc-381">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="5c6cc-382">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="5c6cc-382">Add Razor component code for chat</span></span>

<span data-ttu-id="5c6cc-383">若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：</span><span class="sxs-lookup"><span data-stu-id="5c6cc-383">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="5c6cc-384"><xref:blazor/index>
> [具有 Identity 服务器、WebSocket 和服务器发送事件的持有者令牌身份验证](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="5c6cc-384"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c6cc-385">其他资源</span><span class="sxs-lookup"><span data-stu-id="5c6cc-385">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="5c6cc-386">SignalR 用于身份验证的跨源协商</span><span class="sxs-lookup"><span data-stu-id="5c6cc-386">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
