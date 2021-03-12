---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8e5f216a354b1ed7559b433d3a4867627bf60df3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589772"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f12a9-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="f12a9-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f12a9-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f12a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f12a9-105">这是本系列教程的第一个教程，介绍具有控制器和视图的 ASP.NET Core MVC Web 开发。</span><span class="sxs-lookup"><span data-stu-id="f12a9-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="f12a9-106">在本系列结束时，你将拥有一个管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="f12a9-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="f12a9-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f12a9-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-108">Create a web app.</span></span>
> * <span data-ttu-id="f12a9-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="f12a9-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f12a9-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="f12a9-110">Work with a database.</span></span>
> * <span data-ttu-id="f12a9-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="f12a9-111">Add search and validation.</span></span>

<span data-ttu-id="f12a9-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="f12a9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f12a9-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="f12a9-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f12a9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f12a9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f12a9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f12a9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f12a9-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f12a9-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f12a9-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="f12a9-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f12a9-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f12a9-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f12a9-119">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="f12a9-120">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="f12a9-121">在“配置新项目”对话框中，为“项目名称”输入 `MvcMovie`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="f12a9-122">务必要将项目命名为“MvcMovie”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="f12a9-123">复制代码时，大小写需要匹配每个 `namespace` 匹配项。</span><span class="sxs-lookup"><span data-stu-id="f12a9-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="f12a9-124">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-124">Select **Create**.</span></span>
* <span data-ttu-id="f12a9-125">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：</span><span class="sxs-lookup"><span data-stu-id="f12a9-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="f12a9-126">下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="f12a9-127">ASP.NET Core Web 应用程序（模型-视图-控制器）。</span><span class="sxs-lookup"><span data-stu-id="f12a9-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="f12a9-128">**Create**。</span><span class="sxs-lookup"><span data-stu-id="f12a9-128">**Create**.</span></span>

![<span data-ttu-id="f12a9-129">创建新的 ASP.NET Core Web 应用呈现</span><span class="sxs-lookup"><span data-stu-id="f12a9-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="f12a9-130">有关创建项目的替代方法，请参阅[在 Visual Studio 中创建新项目](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="f12a9-131">Visual Studio 为创建的 MVC 项目使用默认项目模板。</span><span class="sxs-lookup"><span data-stu-id="f12a9-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="f12a9-132">创建的项目：</span><span class="sxs-lookup"><span data-stu-id="f12a9-132">The created project:</span></span>

* <span data-ttu-id="f12a9-133">是一个有效的应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-133">Is a working app.</span></span>
* <span data-ttu-id="f12a9-134">是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="f12a9-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f12a9-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f12a9-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f12a9-136">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="f12a9-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="f12a9-137">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs)和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="f12a9-138">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f12a9-139">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="f12a9-140">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f12a9-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f12a9-141">如果出现对话框，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。**是否添加它们?”** ，选择“是”</span><span class="sxs-lookup"><span data-stu-id="f12a9-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="f12a9-142">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="f12a9-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f12a9-143">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="f12a9-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f12a9-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f12a9-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f12a9-145">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="f12a9-145">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f12a9-147">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="f12a9-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="f12a9-148">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="f12a9-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="f12a9-150">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="f12a9-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f12a9-151">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="f12a9-152">如果看到用于选择“目标框架”的选项，请选择最新的 5.x 版本。</span><span class="sxs-lookup"><span data-stu-id="f12a9-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="f12a9-153">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-153">Select **Next**.</span></span>

* <span data-ttu-id="f12a9-154">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="f12a9-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="f12a9-156">运行应用</span><span class="sxs-lookup"><span data-stu-id="f12a9-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f12a9-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f12a9-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f12a9-158">选择 Ctrl+F5 以在不使用调试程序的情况下运行应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f12a9-159">Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="f12a9-159">Visual Studio:</span></span>

  * <span data-ttu-id="f12a9-160">启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="f12a9-161">运行应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-161">Runs the app.</span></span>

  <span data-ttu-id="f12a9-162">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f12a9-163">本地计算机的标准主机名为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f12a9-164">当 Visual Studio 创建 Web 项目时，对 Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="f12a9-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="f12a9-165">在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：</span><span class="sxs-lookup"><span data-stu-id="f12a9-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f12a9-166">更改代码。</span><span class="sxs-lookup"><span data-stu-id="f12a9-166">Make code changes.</span></span>
* <span data-ttu-id="f12a9-167">保存文件。</span><span class="sxs-lookup"><span data-stu-id="f12a9-167">Save the file.</span></span>
* <span data-ttu-id="f12a9-168">快速刷新浏览器并查看代码更改。</span><span class="sxs-lookup"><span data-stu-id="f12a9-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="f12a9-169">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="f12a9-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![调试菜单](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="f12a9-171">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="f12a9-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="f12a9-173">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="f12a9-173">The following image shows the app:</span></span>

![主页或索引页](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f12a9-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f12a9-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f12a9-176">选择 Ctrl+F5，以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="f12a9-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f12a9-177">Visual Studio Code：</span><span class="sxs-lookup"><span data-stu-id="f12a9-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="f12a9-178">启动 [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="f12a9-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="f12a9-179">启动浏览器。</span><span class="sxs-lookup"><span data-stu-id="f12a9-179">Launches a browser.</span></span>
  * <span data-ttu-id="f12a9-180">导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="f12a9-181">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f12a9-182">本地计算机的标准主机名为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f12a9-183">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="f12a9-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="f12a9-184">在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：</span><span class="sxs-lookup"><span data-stu-id="f12a9-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f12a9-185">更改代码。</span><span class="sxs-lookup"><span data-stu-id="f12a9-185">Make code changes.</span></span>
* <span data-ttu-id="f12a9-186">保存文件。</span><span class="sxs-lookup"><span data-stu-id="f12a9-186">Save the file.</span></span>
* <span data-ttu-id="f12a9-187">快速刷新浏览器并查看代码更改。</span><span class="sxs-lookup"><span data-stu-id="f12a9-187">Quickly refresh the browser and see the code changes.</span></span>

  ![主页或索引页](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f12a9-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f12a9-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f12a9-190">选择“运行”>“开始执行(不调试)”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="f12a9-191">Visual Studio for Mac：</span><span class="sxs-lookup"><span data-stu-id="f12a9-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="f12a9-192">启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="f12a9-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="f12a9-193">启动浏览器。</span><span class="sxs-lookup"><span data-stu-id="f12a9-193">Launches a browser.</span></span>
  * <span data-ttu-id="f12a9-194">导航到 `http://localhost:port`，其中 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="f12a9-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="f12a9-195">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f12a9-196">本地计算机的标准主机名为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f12a9-197">当 Visual Studio 创建 Web 项目时，对 Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="f12a9-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="f12a9-198">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="f12a9-199">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="f12a9-199">The following image shows the app:</span></span>

![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f12a9-201">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="f12a9-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f12a9-202">下一篇：添加控制器</span><span class="sxs-lookup"><span data-stu-id="f12a9-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f12a9-203">这是本系列教程的第一个教程，介绍具有控制器和视图的 ASP.NET Core MVC Web 开发。</span><span class="sxs-lookup"><span data-stu-id="f12a9-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="f12a9-204">在本系列结束时，你将拥有一个管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="f12a9-205">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="f12a9-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f12a9-206">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-206">Create a web app.</span></span>
> * <span data-ttu-id="f12a9-207">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="f12a9-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f12a9-208">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="f12a9-208">Work with a database.</span></span>
> * <span data-ttu-id="f12a9-209">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="f12a9-209">Add search and validation.</span></span>

<span data-ttu-id="f12a9-210">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="f12a9-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f12a9-211">先决条件</span><span class="sxs-lookup"><span data-stu-id="f12a9-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f12a9-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f12a9-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f12a9-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f12a9-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f12a9-214">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f12a9-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f12a9-215">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="f12a9-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f12a9-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f12a9-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f12a9-217">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="f12a9-218">选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![创建新的 ASP.NET Core Web 应用项目](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f12a9-220">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="f12a9-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f12a9-221">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="f12a9-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![配置新项目](start-mvc/_static/config.png)

* <span data-ttu-id="f12a9-223">选择“Web 应用(模型-视图-控制器)”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="f12a9-224">在下拉框中，选择“.NET Core”和“ASP.NET Core 3.1”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="f12a9-225">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="f12a9-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="f12a9-226">Visual Studio 为创建的 MVC 项目使用默认项目模板。</span><span class="sxs-lookup"><span data-stu-id="f12a9-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="f12a9-227">创建的项目：</span><span class="sxs-lookup"><span data-stu-id="f12a9-227">The created project:</span></span>

* <span data-ttu-id="f12a9-228">是一个有效的应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-228">Is a working app.</span></span>
* <span data-ttu-id="f12a9-229">是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="f12a9-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f12a9-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f12a9-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f12a9-231">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="f12a9-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="f12a9-232">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs)和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="f12a9-233">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f12a9-234">将目录 (`cd`) 更改为包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="f12a9-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="f12a9-235">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f12a9-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f12a9-236">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。**是否添加它们?”** ，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="f12a9-237">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="f12a9-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f12a9-238">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="f12a9-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f12a9-239">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f12a9-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f12a9-240">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="f12a9-240">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f12a9-242">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="f12a9-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="f12a9-243">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="f12a9-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="f12a9-245">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="f12a9-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="f12a9-246">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="f12a9-247">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="f12a9-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="f12a9-248">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="f12a9-248">Select **Next**.</span></span>

* <span data-ttu-id="f12a9-249">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="f12a9-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="f12a9-251">运行应用</span><span class="sxs-lookup"><span data-stu-id="f12a9-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f12a9-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f12a9-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f12a9-253">选择 Ctrl+F5 可运行应用，不会进行调试。</span><span class="sxs-lookup"><span data-stu-id="f12a9-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f12a9-254">Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="f12a9-254">Visual Studio:</span></span>

  * <span data-ttu-id="f12a9-255">启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)。</span><span class="sxs-lookup"><span data-stu-id="f12a9-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="f12a9-256">运行应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-256">Runs the app.</span></span>

  <span data-ttu-id="f12a9-257">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f12a9-258">本地计算机的标准主机名为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f12a9-259">当 Visual Studio 创建 Web 项目时，对 Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="f12a9-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="f12a9-260">在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：</span><span class="sxs-lookup"><span data-stu-id="f12a9-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f12a9-261">更改代码。</span><span class="sxs-lookup"><span data-stu-id="f12a9-261">Make code changes.</span></span>
* <span data-ttu-id="f12a9-262">保存文件。</span><span class="sxs-lookup"><span data-stu-id="f12a9-262">Save the file.</span></span>
* <span data-ttu-id="f12a9-263">快速刷新浏览器并查看代码更改。</span><span class="sxs-lookup"><span data-stu-id="f12a9-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="f12a9-264">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="f12a9-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![调试菜单](start-mvc/_static/debug_menu.png)

<span data-ttu-id="f12a9-266">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="f12a9-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="f12a9-268">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="f12a9-268">The following image shows the app:</span></span>

![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f12a9-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f12a9-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f12a9-271">选择 Ctrl+F5 可运行应用，不会进行调试。</span><span class="sxs-lookup"><span data-stu-id="f12a9-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f12a9-272">Visual Studio Code：</span><span class="sxs-lookup"><span data-stu-id="f12a9-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="f12a9-273">启动 [Kestrel](xref:fundamentals/servers/kestrel)</span><span class="sxs-lookup"><span data-stu-id="f12a9-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="f12a9-274">启动浏览器。</span><span class="sxs-lookup"><span data-stu-id="f12a9-274">Launches a browser.</span></span>
  * <span data-ttu-id="f12a9-275">导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="f12a9-276">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f12a9-277">本地计算机的标准主机名为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f12a9-278">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="f12a9-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="f12a9-279">在不进行调试的情况下，通过选择 Ctrl+F5 启动应用，可以：</span><span class="sxs-lookup"><span data-stu-id="f12a9-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="f12a9-280">更改代码。</span><span class="sxs-lookup"><span data-stu-id="f12a9-280">Make code changes.</span></span>
* <span data-ttu-id="f12a9-281">保存文件。</span><span class="sxs-lookup"><span data-stu-id="f12a9-281">Save the file.</span></span>
* <span data-ttu-id="f12a9-282">快速刷新浏览器并查看代码更改。</span><span class="sxs-lookup"><span data-stu-id="f12a9-282">Quickly refresh the browser and see the code changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f12a9-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f12a9-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f12a9-285">选择“运行”>“开始执行(不调试)”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="f12a9-286">Visual Studio for Mac：启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="f12a9-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="f12a9-287">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f12a9-288">本地计算机的标准主机名为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="f12a9-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="f12a9-289">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="f12a9-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f12a9-290">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="f12a9-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="f12a9-291">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="f12a9-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="f12a9-292">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="f12a9-292">The following image shows the app:</span></span>

![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f12a9-294">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="f12a9-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f12a9-295">下一页</span><span class="sxs-lookup"><span data-stu-id="f12a9-295">Next</span></span>](adding-controller.md)

::: moniker-end
