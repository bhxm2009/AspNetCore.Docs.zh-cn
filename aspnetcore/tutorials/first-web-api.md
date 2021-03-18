---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 789cd1a867bc8c17401bbac5c02951b4bd2999b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587653"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="b9671-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="b9671-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="b9671-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="b9671-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="b9671-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="b9671-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b9671-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b9671-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9671-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-107">Create a web API project.</span></span>
> * <span data-ttu-id="b9671-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="b9671-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b9671-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="b9671-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b9671-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="b9671-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b9671-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-111">Call the web API with Postman.</span></span>

<span data-ttu-id="b9671-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b9671-113">概述</span><span class="sxs-lookup"><span data-stu-id="b9671-113">Overview</span></span>

<span data-ttu-id="b9671-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="b9671-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b9671-115">API</span><span class="sxs-lookup"><span data-stu-id="b9671-115">API</span></span> | <span data-ttu-id="b9671-116">描述</span><span class="sxs-lookup"><span data-stu-id="b9671-116">Description</span></span> | <span data-ttu-id="b9671-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="b9671-117">Request body</span></span> | <span data-ttu-id="b9671-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="b9671-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="b9671-119">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-119">Get all to-do items</span></span> | <span data-ttu-id="b9671-120">None</span><span class="sxs-lookup"><span data-stu-id="b9671-120">None</span></span> | <span data-ttu-id="b9671-121">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="b9671-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="b9671-122">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="b9671-122">Get an item by ID</span></span> | <span data-ttu-id="b9671-123">None</span><span class="sxs-lookup"><span data-stu-id="b9671-123">None</span></span> | <span data-ttu-id="b9671-124">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="b9671-125">添加新项</span><span class="sxs-lookup"><span data-stu-id="b9671-125">Add a new item</span></span> | <span data-ttu-id="b9671-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-126">To-do item</span></span> | <span data-ttu-id="b9671-127">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="b9671-128">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b9671-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-129">To-do item</span></span> | <span data-ttu-id="b9671-130">None</span><span class="sxs-lookup"><span data-stu-id="b9671-130">None</span></span> |
|<span data-ttu-id="b9671-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9671-132">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9671-133">None</span><span class="sxs-lookup"><span data-stu-id="b9671-133">None</span></span> | <span data-ttu-id="b9671-134">None</span><span class="sxs-lookup"><span data-stu-id="b9671-134">None</span></span>|

<span data-ttu-id="b9671-135">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="b9671-135">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b9671-141">先决条件</span><span class="sxs-lookup"><span data-stu-id="b9671-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b9671-145">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="b9671-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-147">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b9671-148">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b9671-149">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b9671-150">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择 .NET Core 和 ASP.NET Core 5.0  。</span><span class="sxs-lookup"><span data-stu-id="b9671-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="b9671-151">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-151">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9671-154">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="b9671-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b9671-155">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b9671-156">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="b9671-157">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b9671-158">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-158">The preceding commands:</span></span>

  * <span data-ttu-id="b9671-159">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="b9671-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b9671-160">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b9671-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9671-162">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b9671-164">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b9671-165">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9671-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="b9671-167">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 5.x 目标框架。</span><span class="sxs-lookup"><span data-stu-id="b9671-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="b9671-168">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9671-168">Select **Next**.</span></span>

* <span data-ttu-id="b9671-169">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="b9671-171">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="b9671-172">测试项目</span><span class="sxs-lookup"><span data-stu-id="b9671-172">Test the project</span></span>

<span data-ttu-id="b9671-173">项目模板创建了一个支持 [Swagger](xref:tutorials/web-api-help-pages-using-swagger) 的 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="b9671-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9671-175">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="b9671-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="b9671-176">Visual Studio 将启动：</span><span class="sxs-lookup"><span data-stu-id="b9671-176">Visual Studio launches:</span></span>

* <span data-ttu-id="b9671-177">IIS Express Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="b9671-177">The IIS Express web server.</span></span>
* <span data-ttu-id="b9671-178">默认浏览器，并导航到 `https://localhost:<port>/swagger/index.html`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="b9671-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="b9671-180">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9671-181">在浏览器中，转到以下 URL：[https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="b9671-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b9671-183">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b9671-184">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="b9671-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b9671-185">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="b9671-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b9671-186">将 `/swagger` 追加到 URL（将 URL 更改为 `https://localhost:<port>/swagger`）。</span><span class="sxs-lookup"><span data-stu-id="b9671-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="b9671-187">随即显示 Swagger 页面 `/swagger/index.html`。</span><span class="sxs-lookup"><span data-stu-id="b9671-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="b9671-188">选择 GET  > “试用” > “执行”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="b9671-189">页面将显示：</span><span class="sxs-lookup"><span data-stu-id="b9671-189">The page displays:</span></span>

* <span data-ttu-id="b9671-190">用于测试 WeatherForecast API 的 [Curl](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="b9671-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="b9671-191">用于测试 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="b9671-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="b9671-192">响应代码、正文和标头。</span><span class="sxs-lookup"><span data-stu-id="b9671-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="b9671-193">包含媒体类型、示例值和架构的下拉列表框。</span><span class="sxs-lookup"><span data-stu-id="b9671-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="b9671-194">Swagger 用于为 Web API 生成有用的文档和帮助页面。</span><span class="sxs-lookup"><span data-stu-id="b9671-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="b9671-195">本教程重点介绍如何创建 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="b9671-196">有关 Swagger 的详细信息，请参阅 <xref:tutorials/web-api-help-pages-using-swagger>。</span><span class="sxs-lookup"><span data-stu-id="b9671-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="b9671-197">将请求 URL 复制粘贴到浏览器中：`https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="b9671-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="b9671-198">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b9671-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="b9671-199">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="b9671-199">Update the launchUrl</span></span>

<span data-ttu-id="b9671-200">在 Properties\launchSettings.json 中，将 `launchUrl` 从 `"swagger"` 更新为 `"api/TodoItems"`：</span><span class="sxs-lookup"><span data-stu-id="b9671-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="b9671-201">由于已删除 Swagger，上述标记会将启动的 URL 更改为添加到以下部分的控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="b9671-202">添加模型类</span><span class="sxs-lookup"><span data-stu-id="b9671-202">Add a model class</span></span>

<span data-ttu-id="b9671-203">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="b9671-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b9671-204">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="b9671-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-206">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b9671-207">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="b9671-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9671-208">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="b9671-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="b9671-209">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9671-210">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b9671-211">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b9671-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9671-213">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b9671-214">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="b9671-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9671-216">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-216">Right-click the project.</span></span> <span data-ttu-id="b9671-217">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="b9671-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9671-218">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="b9671-218">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b9671-220">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b9671-221">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b9671-222">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b9671-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="b9671-223">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="b9671-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b9671-224">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b9671-225">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="b9671-225">Add a database context</span></span>

<span data-ttu-id="b9671-226">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="b9671-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b9671-227">此类由 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="b9671-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="b9671-229">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="b9671-229">Add NuGet packages</span></span>

* <span data-ttu-id="b9671-230">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b9671-231">选择“浏览”选项卡，然后在搜索框中输入“`Microsoft.EntityFrameworkCore.InMemory`” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-231">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="b9671-232">在左窗格中，选择“`Microsoft.EntityFrameworkCore.InMemory`”。</span><span class="sxs-lookup"><span data-stu-id="b9671-232">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="b9671-233">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 程序包管理器](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b9671-235">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="b9671-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="b9671-236">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9671-237">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-238">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9671-239">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b9671-240">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b9671-241">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="b9671-241">Register the database context</span></span>

<span data-ttu-id="b9671-242">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="b9671-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b9671-243">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="b9671-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="b9671-244">使用以下代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="b9671-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b9671-245">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-245">The preceding code:</span></span>

* <span data-ttu-id="b9671-246">删除 Swagger 调用。</span><span class="sxs-lookup"><span data-stu-id="b9671-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="b9671-247">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="b9671-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b9671-248">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b9671-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b9671-249">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="b9671-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b9671-250">构建控制器</span><span class="sxs-lookup"><span data-stu-id="b9671-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-252">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b9671-253">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b9671-254">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="b9671-255">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="b9671-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b9671-256">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b9671-257">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b9671-258">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-259">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b9671-260">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="b9671-261">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-261">The preceding commands:</span></span>

* <span data-ttu-id="b9671-262">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b9671-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b9671-263">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="b9671-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b9671-264">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="b9671-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b9671-265">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-265">The generated code:</span></span>

* <span data-ttu-id="b9671-266">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="b9671-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="b9671-267">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b9671-268">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="b9671-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b9671-269">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="b9671-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b9671-270">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="b9671-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="b9671-271">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="b9671-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="b9671-272">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="b9671-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="b9671-273">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="b9671-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="b9671-274">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="b9671-275">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="b9671-276">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="b9671-277">更新 `PostTodoItem` 中的 return 语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="b9671-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b9671-278">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="b9671-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="b9671-279">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="b9671-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b9671-280">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b9671-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9671-281"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b9671-282">如果成功，将返回 [HTTP 201 状态代码](https://developer.mozilla.org/docs/Web/HTTP/Status/201)。</span><span class="sxs-lookup"><span data-stu-id="b9671-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="b9671-283">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b9671-284">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="b9671-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b9671-285">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="b9671-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b9671-286">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b9671-287">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="b9671-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b9671-288">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b9671-289">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="b9671-289">Install Postman</span></span>

<span data-ttu-id="b9671-290">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b9671-291">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="b9671-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b9671-292">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-292">Start the web app.</span></span>
* <span data-ttu-id="b9671-293">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="b9671-293">Start Postman.</span></span>
* <span data-ttu-id="b9671-294">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="b9671-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="b9671-295">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="b9671-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b9671-296">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="b9671-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b9671-297">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9671-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b9671-298">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-298">Create a new request.</span></span>
* <span data-ttu-id="b9671-299">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="b9671-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b9671-300">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b9671-301">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b9671-302">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="b9671-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="b9671-303">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="b9671-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b9671-304">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="b9671-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b9671-305">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b9671-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b9671-306">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-306">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b9671-308">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="b9671-308">Test the location header URI</span></span>

<span data-ttu-id="b9671-309">你可以在浏览器中测试位置标头 URI。</span><span class="sxs-lookup"><span data-stu-id="b9671-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="b9671-310">将位置标头 URI 复制粘贴到浏览器中。</span><span class="sxs-lookup"><span data-stu-id="b9671-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="b9671-311">若要在 Postman 中测试，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b9671-311">To test in Postman:</span></span>

* <span data-ttu-id="b9671-312">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="b9671-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b9671-313">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="b9671-313">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="b9671-315">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="b9671-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="b9671-316">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="b9671-317">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="b9671-318">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b9671-319">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-319">Examine the GET methods</span></span>

<span data-ttu-id="b9671-320">实现了两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="b9671-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b9671-321">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b9671-322">例如：</span><span class="sxs-lookup"><span data-stu-id="b9671-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="b9671-323">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="b9671-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b9671-324">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="b9671-324">Test Get with Postman</span></span>

* <span data-ttu-id="b9671-325">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-325">Create a new request.</span></span>
* <span data-ttu-id="b9671-326">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="b9671-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b9671-327">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b9671-328">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b9671-329">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="b9671-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b9671-330">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-330">Select **Send**.</span></span>

<span data-ttu-id="b9671-331">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="b9671-331">This app uses an in-memory database.</span></span> <span data-ttu-id="b9671-332">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="b9671-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b9671-333">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b9671-334">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="b9671-334">Routing and URL paths</span></span>

<span data-ttu-id="b9671-335">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b9671-336">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="b9671-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b9671-337">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="b9671-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b9671-338">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="b9671-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b9671-339">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="b9671-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b9671-340">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b9671-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b9671-341">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="b9671-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b9671-342">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="b9671-342">This sample doesn't use a template.</span></span> <span data-ttu-id="b9671-343">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b9671-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9671-344">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="b9671-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b9671-345">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b9671-346">返回值</span><span class="sxs-lookup"><span data-stu-id="b9671-346">Return values</span></span>

<span data-ttu-id="b9671-347">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="b9671-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b9671-348">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="b9671-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b9671-349">此返回类型的响应代码为 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)（假设没有未处理的异常）。</span><span class="sxs-lookup"><span data-stu-id="b9671-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b9671-350">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="b9671-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b9671-351">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b9671-352">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="b9671-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b9671-353">如果没有任何项与请求的 ID 匹配，该方法将返回 [404 状态](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="b9671-354">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="b9671-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b9671-355">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b9671-356">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-356">The PutTodoItem method</span></span>

<span data-ttu-id="b9671-357">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b9671-358">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="b9671-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b9671-359">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b9671-360">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="b9671-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b9671-361">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="b9671-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b9671-362">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b9671-363">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="b9671-364">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="b9671-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b9671-365">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="b9671-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b9671-366">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="b9671-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b9671-367">更新 ID 为 1 的待办事项并将其名称设置为 `"feed fish"`：</span><span class="sxs-lookup"><span data-stu-id="b9671-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b9671-368">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="b9671-368">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b9671-370">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="b9671-371">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b9671-372">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b9671-373">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="b9671-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b9671-374">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="b9671-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b9671-375">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b9671-376">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="b9671-377">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="b9671-377">Prevent over-posting</span></span>

<span data-ttu-id="b9671-378">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="b9671-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b9671-379">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="b9671-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b9671-380">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="b9671-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b9671-381">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="b9671-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b9671-382">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="b9671-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="b9671-383">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="b9671-383">A DTO may be used to:</span></span>

* <span data-ttu-id="b9671-384">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="b9671-384">Prevent over-posting.</span></span>
* <span data-ttu-id="b9671-385">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="b9671-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b9671-386">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="b9671-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b9671-387">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="b9671-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b9671-388">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="b9671-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b9671-389">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="b9671-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="b9671-390">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="b9671-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="b9671-391">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="b9671-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="b9671-392">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="b9671-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="b9671-393">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="b9671-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="b9671-394">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="b9671-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b9671-395">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="b9671-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="b9671-396">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="b9671-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b9671-397">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b9671-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9671-398">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-398">Create a web API project.</span></span>
> * <span data-ttu-id="b9671-399">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="b9671-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b9671-400">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="b9671-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b9671-401">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="b9671-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b9671-402">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-402">Call the web API with Postman.</span></span>

<span data-ttu-id="b9671-403">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b9671-404">概述</span><span class="sxs-lookup"><span data-stu-id="b9671-404">Overview</span></span>

<span data-ttu-id="b9671-405">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="b9671-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b9671-406">API</span><span class="sxs-lookup"><span data-stu-id="b9671-406">API</span></span> | <span data-ttu-id="b9671-407">描述</span><span class="sxs-lookup"><span data-stu-id="b9671-407">Description</span></span> | <span data-ttu-id="b9671-408">请求正文</span><span class="sxs-lookup"><span data-stu-id="b9671-408">Request body</span></span> | <span data-ttu-id="b9671-409">响应正文</span><span class="sxs-lookup"><span data-stu-id="b9671-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="b9671-410">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-410">Get all to-do items</span></span> | <span data-ttu-id="b9671-411">None</span><span class="sxs-lookup"><span data-stu-id="b9671-411">None</span></span> | <span data-ttu-id="b9671-412">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="b9671-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="b9671-413">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="b9671-413">Get an item by ID</span></span> | <span data-ttu-id="b9671-414">None</span><span class="sxs-lookup"><span data-stu-id="b9671-414">None</span></span> | <span data-ttu-id="b9671-415">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="b9671-416">添加新项</span><span class="sxs-lookup"><span data-stu-id="b9671-416">Add a new item</span></span> | <span data-ttu-id="b9671-417">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-417">To-do item</span></span> | <span data-ttu-id="b9671-418">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="b9671-419">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b9671-420">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-420">To-do item</span></span> | <span data-ttu-id="b9671-421">None</span><span class="sxs-lookup"><span data-stu-id="b9671-421">None</span></span> |
|<span data-ttu-id="b9671-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9671-423">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9671-424">None</span><span class="sxs-lookup"><span data-stu-id="b9671-424">None</span></span> | <span data-ttu-id="b9671-425">None</span><span class="sxs-lookup"><span data-stu-id="b9671-425">None</span></span>|

<span data-ttu-id="b9671-426">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="b9671-426">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b9671-432">先决条件</span><span class="sxs-lookup"><span data-stu-id="b9671-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-435">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b9671-436">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="b9671-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-438">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b9671-439">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b9671-440">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b9671-441">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="b9671-442">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-442">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9671-445">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="b9671-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b9671-446">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b9671-447">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="b9671-448">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b9671-449">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-449">The preceding commands:</span></span>

  * <span data-ttu-id="b9671-450">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="b9671-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b9671-451">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b9671-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-452">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9671-453">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-453">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b9671-455">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b9671-456">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9671-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="b9671-458">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 3.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="b9671-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="b9671-459">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9671-459">Select **Next**.</span></span>

* <span data-ttu-id="b9671-460">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="b9671-462">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="b9671-463">测试 API</span><span class="sxs-lookup"><span data-stu-id="b9671-463">Test the API</span></span>

<span data-ttu-id="b9671-464">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="b9671-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="b9671-465">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9671-467">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9671-468">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="b9671-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b9671-469">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b9671-470">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b9671-472">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9671-473">在浏览器中，转到以下 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="b9671-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-474">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b9671-475">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b9671-476">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="b9671-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b9671-477">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="b9671-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b9671-478">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="b9671-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="b9671-479">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b9671-479">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="b9671-480">添加模型类</span><span class="sxs-lookup"><span data-stu-id="b9671-480">Add a model class</span></span>

<span data-ttu-id="b9671-481">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="b9671-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b9671-482">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="b9671-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-484">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b9671-485">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="b9671-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9671-486">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="b9671-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="b9671-487">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9671-488">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b9671-489">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9671-491">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b9671-492">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="b9671-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-493">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9671-494">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-494">Right-click the project.</span></span> <span data-ttu-id="b9671-495">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="b9671-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9671-496">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="b9671-496">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b9671-498">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b9671-499">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b9671-500">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="b9671-501">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="b9671-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b9671-502">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b9671-503">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="b9671-503">Add a database context</span></span>

<span data-ttu-id="b9671-504">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="b9671-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b9671-505">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="b9671-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="b9671-507">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="b9671-507">Add NuGet packages</span></span>

* <span data-ttu-id="b9671-508">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b9671-509">选择“浏览”选项卡，然后在搜索框中输入“Microsoft.EntityFrameworkCore.InMemory” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="b9671-510">在左窗格中选择“Microsoft.EntityFrameworkCore.InMemory”。</span><span class="sxs-lookup"><span data-stu-id="b9671-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="b9671-511">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b9671-513">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="b9671-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="b9671-514">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9671-515">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-516">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9671-517">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b9671-518">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b9671-519">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="b9671-519">Register the database context</span></span>

<span data-ttu-id="b9671-520">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="b9671-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b9671-521">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="b9671-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="b9671-522">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="b9671-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b9671-523">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-523">The preceding code:</span></span>

* <span data-ttu-id="b9671-524">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="b9671-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b9671-525">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b9671-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b9671-526">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="b9671-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b9671-527">构建控制器</span><span class="sxs-lookup"><span data-stu-id="b9671-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-529">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b9671-530">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b9671-531">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="b9671-532">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="b9671-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b9671-533">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b9671-534">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b9671-535">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-536">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b9671-537">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="b9671-538">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-538">The preceding commands:</span></span>

* <span data-ttu-id="b9671-539">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b9671-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b9671-540">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="b9671-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b9671-541">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="b9671-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b9671-542">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-542">The generated code:</span></span>

* <span data-ttu-id="b9671-543">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="b9671-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="b9671-544">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b9671-545">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="b9671-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b9671-546">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="b9671-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b9671-547">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="b9671-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="b9671-548">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="b9671-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="b9671-549">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="b9671-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="b9671-550">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="b9671-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="b9671-551">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="b9671-552">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="b9671-553">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="b9671-554">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="b9671-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b9671-555">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="b9671-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="b9671-556">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="b9671-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b9671-557">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b9671-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9671-558"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b9671-559">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="b9671-560">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b9671-561">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="b9671-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b9671-562">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="b9671-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b9671-563">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b9671-564">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="b9671-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b9671-565">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b9671-566">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="b9671-566">Install Postman</span></span>

<span data-ttu-id="b9671-567">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b9671-568">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="b9671-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b9671-569">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-569">Start the web app.</span></span>
* <span data-ttu-id="b9671-570">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="b9671-570">Start Postman.</span></span>
* <span data-ttu-id="b9671-571">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="b9671-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="b9671-572">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="b9671-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b9671-573">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="b9671-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b9671-574">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="b9671-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b9671-575">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-575">Create a new request.</span></span>
* <span data-ttu-id="b9671-576">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="b9671-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b9671-577">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b9671-578">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b9671-579">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="b9671-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="b9671-580">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="b9671-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b9671-581">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="b9671-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b9671-582">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b9671-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b9671-583">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-583">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="b9671-585">使用 Postman 测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="b9671-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="b9671-586">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="b9671-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b9671-587">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="b9671-587">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="b9671-589">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="b9671-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="b9671-590">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="b9671-591">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="b9671-592">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b9671-593">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-593">Examine the GET methods</span></span>

<span data-ttu-id="b9671-594">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="b9671-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b9671-595">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b9671-596">例如：</span><span class="sxs-lookup"><span data-stu-id="b9671-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="b9671-597">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="b9671-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b9671-598">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="b9671-598">Test Get with Postman</span></span>

* <span data-ttu-id="b9671-599">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-599">Create a new request.</span></span>
* <span data-ttu-id="b9671-600">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="b9671-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b9671-601">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b9671-602">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="b9671-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b9671-603">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="b9671-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b9671-604">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-604">Select **Send**.</span></span>

<span data-ttu-id="b9671-605">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="b9671-605">This app uses an in-memory database.</span></span> <span data-ttu-id="b9671-606">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="b9671-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b9671-607">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b9671-608">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="b9671-608">Routing and URL paths</span></span>

<span data-ttu-id="b9671-609">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b9671-610">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="b9671-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b9671-611">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="b9671-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b9671-612">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="b9671-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b9671-613">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="b9671-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b9671-614">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b9671-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b9671-615">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="b9671-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b9671-616">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="b9671-616">This sample doesn't use a template.</span></span> <span data-ttu-id="b9671-617">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b9671-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9671-618">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="b9671-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b9671-619">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b9671-620">返回值</span><span class="sxs-lookup"><span data-stu-id="b9671-620">Return values</span></span> 

<span data-ttu-id="b9671-621">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="b9671-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b9671-622">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="b9671-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b9671-623">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="b9671-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b9671-624">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="b9671-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b9671-625">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b9671-626">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="b9671-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b9671-627">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="b9671-628">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="b9671-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b9671-629">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b9671-630">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-630">The PutTodoItem method</span></span>

<span data-ttu-id="b9671-631">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b9671-632">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="b9671-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b9671-633">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b9671-634">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="b9671-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b9671-635">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="b9671-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b9671-636">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b9671-637">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="b9671-638">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="b9671-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b9671-639">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="b9671-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b9671-640">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="b9671-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b9671-641">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="b9671-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b9671-642">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="b9671-642">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b9671-644">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="b9671-645">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b9671-646">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="b9671-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b9671-647">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="b9671-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b9671-648">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="b9671-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b9671-649">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b9671-650">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="b9671-651">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="b9671-651">Prevent over-posting</span></span>

<span data-ttu-id="b9671-652">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="b9671-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b9671-653">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="b9671-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b9671-654">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="b9671-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b9671-655">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="b9671-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b9671-656">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="b9671-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="b9671-657">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="b9671-657">A DTO may be used to:</span></span>

* <span data-ttu-id="b9671-658">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="b9671-658">Prevent over-posting.</span></span>
* <span data-ttu-id="b9671-659">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="b9671-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b9671-660">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="b9671-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b9671-661">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="b9671-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b9671-662">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="b9671-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b9671-663">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="b9671-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="b9671-664">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="b9671-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="b9671-665">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="b9671-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="b9671-666">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="b9671-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="b9671-667">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="b9671-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="b9671-668">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="b9671-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b9671-669">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="b9671-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="b9671-670">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="b9671-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b9671-671">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b9671-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9671-672">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-672">Create a web API project.</span></span>
> * <span data-ttu-id="b9671-673">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="b9671-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b9671-674">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="b9671-674">Add a controller.</span></span>
> * <span data-ttu-id="b9671-675">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="b9671-676">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="b9671-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="b9671-677">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="b9671-677">Specify return values.</span></span>
> * <span data-ttu-id="b9671-678">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="b9671-679">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="b9671-680">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="b9671-681">概述 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-681">Overview 2.1</span></span>

<span data-ttu-id="b9671-682">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="b9671-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b9671-683">API</span><span class="sxs-lookup"><span data-stu-id="b9671-683">API</span></span> | <span data-ttu-id="b9671-684">描述</span><span class="sxs-lookup"><span data-stu-id="b9671-684">Description</span></span> | <span data-ttu-id="b9671-685">请求正文</span><span class="sxs-lookup"><span data-stu-id="b9671-685">Request body</span></span> | <span data-ttu-id="b9671-686">响应正文</span><span class="sxs-lookup"><span data-stu-id="b9671-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="b9671-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b9671-687">GET /api/TodoItems</span></span> | <span data-ttu-id="b9671-688">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-688">Get all to-do items</span></span> | <span data-ttu-id="b9671-689">None</span><span class="sxs-lookup"><span data-stu-id="b9671-689">None</span></span> | <span data-ttu-id="b9671-690">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="b9671-690">Array of to-do items</span></span>|
|<span data-ttu-id="b9671-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b9671-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="b9671-692">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="b9671-692">Get an item by ID</span></span> | <span data-ttu-id="b9671-693">None</span><span class="sxs-lookup"><span data-stu-id="b9671-693">None</span></span> | <span data-ttu-id="b9671-694">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-694">To-do item</span></span>|
|<span data-ttu-id="b9671-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b9671-695">POST /api/TodoItems</span></span> | <span data-ttu-id="b9671-696">添加新项</span><span class="sxs-lookup"><span data-stu-id="b9671-696">Add a new item</span></span> | <span data-ttu-id="b9671-697">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-697">To-do item</span></span> | <span data-ttu-id="b9671-698">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-698">To-do item</span></span> |
|<span data-ttu-id="b9671-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b9671-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="b9671-700">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b9671-701">待办事项</span><span class="sxs-lookup"><span data-stu-id="b9671-701">To-do item</span></span> | <span data-ttu-id="b9671-702">None</span><span class="sxs-lookup"><span data-stu-id="b9671-702">None</span></span> |
|<span data-ttu-id="b9671-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9671-704">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="b9671-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b9671-705">None</span><span class="sxs-lookup"><span data-stu-id="b9671-705">None</span></span> | <span data-ttu-id="b9671-706">None</span><span class="sxs-lookup"><span data-stu-id="b9671-706">None</span></span>|

<span data-ttu-id="b9671-707">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="b9671-707">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="b9671-713">先决条件 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-716">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="b9671-717">创建 Web 项目 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-719">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b9671-720">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b9671-721">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b9671-722">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="b9671-723">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="b9671-724">请不要选择“启用 Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-724">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9671-727">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="b9671-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b9671-728">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b9671-729">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9671-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="b9671-730">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="b9671-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="b9671-731">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-732">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9671-733">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-733">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b9671-735">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b9671-736">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9671-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="b9671-737">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 2.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="b9671-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="b9671-738">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9671-738">Select **Next**.</span></span>

* <span data-ttu-id="b9671-739">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="b9671-741">测试 API 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-741">Test the API 2.1</span></span>

<span data-ttu-id="b9671-742">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="b9671-742">The project template creates a `values` API.</span></span> <span data-ttu-id="b9671-743">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9671-745">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9671-746">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="b9671-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b9671-747">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b9671-748">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="b9671-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b9671-750">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b9671-751">在浏览器中，转到以下 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="b9671-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-752">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b9671-753">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b9671-754">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="b9671-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b9671-755">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="b9671-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b9671-756">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="b9671-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="b9671-757">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="b9671-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="b9671-758">添加模型类 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-758">Add a model class 2.1</span></span>

<span data-ttu-id="b9671-759">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="b9671-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b9671-760">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="b9671-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-762">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b9671-763">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="b9671-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9671-764">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="b9671-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="b9671-765">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9671-766">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b9671-767">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b9671-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b9671-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b9671-769">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b9671-770">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="b9671-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9671-771">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b9671-772">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-772">Right-click the project.</span></span> <span data-ttu-id="b9671-773">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="b9671-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b9671-774">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="b9671-774">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b9671-776">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b9671-777">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="b9671-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b9671-778">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="b9671-779">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="b9671-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b9671-780">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="b9671-781">添加数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-781">Add a database context 2.1</span></span>

<span data-ttu-id="b9671-782">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="b9671-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b9671-783">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="b9671-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-785">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="b9671-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b9671-786">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-787">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9671-788">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b9671-789">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="b9671-790">注册数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-790">Register the database context 2.1</span></span>

<span data-ttu-id="b9671-791">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="b9671-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b9671-792">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="b9671-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="b9671-793">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="b9671-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="b9671-794">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-794">The preceding code:</span></span>

* <span data-ttu-id="b9671-795">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="b9671-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b9671-796">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="b9671-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b9671-797">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="b9671-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="b9671-798">添加控制器 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-800">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b9671-801">选择“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="b9671-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="b9671-802">在“添加新项”对话框中，选择“API 控制器类”模板 。</span><span class="sxs-lookup"><span data-stu-id="b9671-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="b9671-803">将类命名为 TodoController，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="b9671-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-805">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9671-806">在“控制器”文件夹中，创建名为 `TodoController` 的类。</span><span class="sxs-lookup"><span data-stu-id="b9671-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="b9671-807">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="b9671-808">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="b9671-808">The preceding code:</span></span>

* <span data-ttu-id="b9671-809">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="b9671-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="b9671-810">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="b9671-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="b9671-811">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b9671-812">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="b9671-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b9671-813">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="b9671-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b9671-814">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="b9671-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="b9671-815">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="b9671-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="b9671-816">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="b9671-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="b9671-817">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="b9671-818">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="b9671-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="b9671-819">添加 Get 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-819">Add Get methods 2.1</span></span>

<span data-ttu-id="b9671-820">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="b9671-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="b9671-821">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="b9671-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="b9671-822">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="b9671-822">Stop the app if it's still running.</span></span> <span data-ttu-id="b9671-823">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="b9671-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="b9671-824">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="b9671-825">例如：</span><span class="sxs-lookup"><span data-stu-id="b9671-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="b9671-826">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="b9671-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="b9671-827">路由和 URL 路径 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="b9671-828">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b9671-829">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="b9671-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b9671-830">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="b9671-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="b9671-831">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="b9671-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b9671-832">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”。</span><span class="sxs-lookup"><span data-stu-id="b9671-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="b9671-833">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b9671-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b9671-834">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="b9671-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b9671-835">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="b9671-835">This sample doesn't use a template.</span></span> <span data-ttu-id="b9671-836">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="b9671-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b9671-837">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="b9671-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b9671-838">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="b9671-839">返回值 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-839">Return values 2.1</span></span>

<span data-ttu-id="b9671-840">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="b9671-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b9671-841">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="b9671-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b9671-842">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="b9671-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b9671-843">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="b9671-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b9671-844">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b9671-845">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="b9671-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b9671-846">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="b9671-847">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="b9671-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b9671-848">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="b9671-849">测试 GetTodoItems 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="b9671-850">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b9671-851">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="b9671-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="b9671-852">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-852">Start the web app.</span></span>
* <span data-ttu-id="b9671-853">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="b9671-853">Start Postman.</span></span>
* <span data-ttu-id="b9671-854">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="b9671-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9671-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9671-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9671-856">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="b9671-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b9671-857">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b9671-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b9671-858">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”   。</span><span class="sxs-lookup"><span data-stu-id="b9671-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="b9671-859">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="b9671-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="b9671-860">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="b9671-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="b9671-861">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-861">Create a new request.</span></span>
  * <span data-ttu-id="b9671-862">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="b9671-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="b9671-863">将请求 URI 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="b9671-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="b9671-864">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="b9671-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="b9671-865">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="b9671-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b9671-866">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-866">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="b9671-868">添加 Create 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-868">Add a Create method 2.1</span></span>

<span data-ttu-id="b9671-869">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="b9671-870">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="b9671-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="b9671-871">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="b9671-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b9671-872">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="b9671-873">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="b9671-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="b9671-874">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b9671-875">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="b9671-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="b9671-876">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="b9671-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="b9671-877">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b9671-878">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="b9671-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b9671-879">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="b9671-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="b9671-880">测试 PostTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="b9671-881">生成项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-881">Build the project.</span></span>
* <span data-ttu-id="b9671-882">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="b9671-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b9671-883">将 URI 设置为 `https://localhost:<port>/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="b9671-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="b9671-884">例如 `https://localhost:5001/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="b9671-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="b9671-885">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="b9671-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="b9671-886">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="b9671-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b9671-887">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="b9671-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b9671-888">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="b9671-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b9671-889">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-889">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="b9671-891">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="b9671-892">测试位置标头 URI 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="b9671-893">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="b9671-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b9671-894">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="b9671-894">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="b9671-896">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="b9671-896">Set the method to GET.</span></span>
* <span data-ttu-id="b9671-897">将 URI 设置为 `https://localhost:<port>/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="b9671-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="b9671-898">例如 `https://localhost:5001/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="b9671-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="b9671-899">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="b9671-900">添加 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="b9671-901">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="b9671-902">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="b9671-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b9671-903">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b9671-904">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="b9671-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b9671-905">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="b9671-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b9671-906">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="b9671-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="b9671-907">测试 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="b9671-908">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="b9671-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b9671-909">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="b9671-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b9671-910">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="b9671-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b9671-911">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="b9671-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b9671-912">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="b9671-912">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="b9671-914">添加 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="b9671-915">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="b9671-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="b9671-916">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="b9671-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="b9671-917">测试 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="b9671-918">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="b9671-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b9671-919">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="b9671-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b9671-920">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="b9671-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="b9671-921">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="b9671-921">Select **Send**.</span></span>

<span data-ttu-id="b9671-922">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="b9671-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="b9671-923">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="b9671-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="b9671-924">使用 JavaScript 调用 Web API 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="b9671-925">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="b9671-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="b9671-926">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="b9671-926">jQuery initiates the request.</span></span> <span data-ttu-id="b9671-927">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="b9671-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="b9671-928">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)并[实现默认文件映射](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="b9671-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="b9671-929">在项目目录中创建 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b9671-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="b9671-930">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="b9671-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="b9671-931">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="b9671-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="b9671-932">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="b9671-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="b9671-933">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="b9671-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="b9671-934">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="b9671-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="b9671-935">打开 Properties\launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="b9671-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="b9671-936">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用。</span><span class="sxs-lookup"><span data-stu-id="b9671-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="b9671-937">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="b9671-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="b9671-938">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="b9671-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="b9671-939">获取待办事项列表 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="b9671-940">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="b9671-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="b9671-941">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="b9671-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="b9671-942">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="b9671-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="b9671-943">添加待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="b9671-944">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="b9671-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="b9671-945">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="b9671-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="b9671-946">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="b9671-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="b9671-947">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="b9671-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="b9671-948">更新待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="b9671-949">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="b9671-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="b9671-950">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="b9671-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="b9671-951">删除待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="b9671-952">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="b9671-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="b9671-953">向 Web API 添加身份验证支持 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="b9671-954">其他资源 2.1</span><span class="sxs-lookup"><span data-stu-id="b9671-954">Additional resources 2.1</span></span>

<span data-ttu-id="b9671-955">[查看或下载本教程的示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="b9671-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="b9671-956">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="b9671-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b9671-957">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="b9671-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="b9671-958">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="b9671-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="b9671-959">Microsoft Learn：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="b9671-959">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
