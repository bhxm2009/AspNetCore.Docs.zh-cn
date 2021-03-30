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
ms.openlocfilehash: 99b3b4af31683feb10c01d1a7297a3489b01c797
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711693"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="fdb3f-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="fdb3f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="fdb3f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="fdb3f-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fdb3f-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fdb3f-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-107">Create a web API project.</span></span>
> * <span data-ttu-id="fdb3f-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="fdb3f-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="fdb3f-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="fdb3f-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="fdb3f-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="fdb3f-113">概述</span><span class="sxs-lookup"><span data-stu-id="fdb3f-113">Overview</span></span>

<span data-ttu-id="fdb3f-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="fdb3f-115">API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-115">API</span></span> | <span data-ttu-id="fdb3f-116">描述</span><span class="sxs-lookup"><span data-stu-id="fdb3f-116">Description</span></span> | <span data-ttu-id="fdb3f-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-117">Request body</span></span> | <span data-ttu-id="fdb3f-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="fdb3f-119">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-119">Get all to-do items</span></span> | <span data-ttu-id="fdb3f-120">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-120">None</span></span> | <span data-ttu-id="fdb3f-121">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="fdb3f-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="fdb3f-122">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-122">Get an item by ID</span></span> | <span data-ttu-id="fdb3f-123">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-123">None</span></span> | <span data-ttu-id="fdb3f-124">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="fdb3f-125">添加新项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-125">Add a new item</span></span> | <span data-ttu-id="fdb3f-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-126">To-do item</span></span> | <span data-ttu-id="fdb3f-127">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="fdb3f-128">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="fdb3f-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-129">To-do item</span></span> | <span data-ttu-id="fdb3f-130">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-130">None</span></span> |
|<span data-ttu-id="fdb3f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="fdb3f-132">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="fdb3f-133">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-133">None</span></span> | <span data-ttu-id="fdb3f-134">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-134">None</span></span>|

<span data-ttu-id="fdb3f-135">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-135">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="fdb3f-141">先决条件</span><span class="sxs-lookup"><span data-stu-id="fdb3f-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="fdb3f-145">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="fdb3f-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-147">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="fdb3f-148">选择“ASP.NET Core Web API”模板，然后单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-148">Select the **ASP.NET Core Web API** template and click **Next**.</span></span>
* <span data-ttu-id="fdb3f-149">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="fdb3f-150">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择 .NET Core 和 ASP.NET Core 5.0  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="fdb3f-151">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-151">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fdb3f-154">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fdb3f-155">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="fdb3f-156">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="fdb3f-157">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="fdb3f-158">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-158">The preceding commands:</span></span>

  * <span data-ttu-id="fdb3f-159">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="fdb3f-160">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fdb3f-162">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="fdb3f-164">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="fdb3f-165">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="fdb3f-167">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 5.x 目标框架。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="fdb3f-168">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-168">Select **Next**.</span></span>

* <span data-ttu-id="fdb3f-169">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="fdb3f-171">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="fdb3f-172">测试项目</span><span class="sxs-lookup"><span data-stu-id="fdb3f-172">Test the project</span></span>

<span data-ttu-id="fdb3f-173">项目模板创建了一个支持 [Swagger](xref:tutorials/web-api-help-pages-using-swagger) 的 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fdb3f-175">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="fdb3f-176">Visual Studio 将启动：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-176">Visual Studio launches:</span></span>

* <span data-ttu-id="fdb3f-177">IIS Express Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-177">The IIS Express web server.</span></span>
* <span data-ttu-id="fdb3f-178">默认浏览器，并导航到 `https://localhost:<port>/swagger/index.html`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="fdb3f-180">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="fdb3f-181">在浏览器中，转到以下 URL：[https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="fdb3f-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fdb3f-183">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="fdb3f-184">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="fdb3f-185">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="fdb3f-186">将 `/swagger` 追加到 URL（将 URL 更改为 `https://localhost:<port>/swagger`）。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="fdb3f-187">随即显示 Swagger 页面 `/swagger/index.html`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="fdb3f-188">选择 GET  > “试用” > “执行”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="fdb3f-189">页面将显示：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-189">The page displays:</span></span>

* <span data-ttu-id="fdb3f-190">用于测试 WeatherForecast API 的 [Curl](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="fdb3f-191">用于测试 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="fdb3f-192">响应代码、正文和标头。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="fdb3f-193">包含媒体类型、示例值和架构的下拉列表框。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-193">A drop down list box with media types and the example value and schema.</span></span>

<span data-ttu-id="fdb3f-194">如果 Swagger 页面未显示，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/21647)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-194">If the Swagger page doesn't appear, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21647).</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="fdb3f-195">Swagger 用于为 Web API 生成有用的文档和帮助页面。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-195">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="fdb3f-196">本教程重点介绍如何创建 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-196">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="fdb3f-197">有关 Swagger 的详细信息，请参阅 <xref:tutorials/web-api-help-pages-using-swagger>。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-197">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="fdb3f-198">将请求 URL 复制粘贴到浏览器中：`https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="fdb3f-198">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="fdb3f-199">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-199">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="fdb3f-200">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="fdb3f-200">Update the launchUrl</span></span>

<span data-ttu-id="fdb3f-201">在 Properties\launchSettings.json 中，将 `launchUrl` 从 `"swagger"` 更新为 `"api/TodoItems"`：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-201">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="fdb3f-202">由于已删除 Swagger，上述标记会将启动的 URL 更改为添加到以下部分的控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-202">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="fdb3f-203">添加模型类</span><span class="sxs-lookup"><span data-stu-id="fdb3f-203">Add a model class</span></span>

<span data-ttu-id="fdb3f-204">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-204">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="fdb3f-205">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-205">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-207">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-207">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="fdb3f-208">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-208">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fdb3f-209">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-209">Name the folder *Models*.</span></span>

* <span data-ttu-id="fdb3f-210">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-210">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fdb3f-211">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-211">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="fdb3f-212">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-212">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fdb3f-214">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-214">Add a folder named *Models*.</span></span>

* <span data-ttu-id="fdb3f-215">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-215">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-216">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fdb3f-217">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-217">Right-click the project.</span></span> <span data-ttu-id="fdb3f-218">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-218">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fdb3f-219">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-219">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="fdb3f-221">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-221">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="fdb3f-222">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-222">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="fdb3f-223">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-223">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="fdb3f-224">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-224">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="fdb3f-225">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-225">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="fdb3f-226">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-226">Add a database context</span></span>

<span data-ttu-id="fdb3f-227">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-227">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="fdb3f-228">此类由 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-228">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-229">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="fdb3f-230">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="fdb3f-230">Add NuGet packages</span></span>

* <span data-ttu-id="fdb3f-231">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-231">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="fdb3f-232">选择“浏览”选项卡，然后在搜索框中输入“`Microsoft.EntityFrameworkCore.InMemory`” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-232">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="fdb3f-233">在左窗格中，选择“`Microsoft.EntityFrameworkCore.InMemory`”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-233">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="fdb3f-234">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-234">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 程序包管理器](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="fdb3f-236">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="fdb3f-237">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fdb3f-238">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="fdb3f-240">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="fdb3f-241">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="fdb3f-242">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-242">Register the database context</span></span>

<span data-ttu-id="fdb3f-243">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fdb3f-244">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="fdb3f-245">使用以下代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="fdb3f-246">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-246">The preceding code:</span></span>

* <span data-ttu-id="fdb3f-247">删除 Swagger 调用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="fdb3f-248">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="fdb3f-249">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="fdb3f-250">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="fdb3f-251">构建控制器</span><span class="sxs-lookup"><span data-stu-id="fdb3f-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-253">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="fdb3f-254">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="fdb3f-255">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="fdb3f-256">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="fdb3f-257">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="fdb3f-258">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="fdb3f-259">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-260">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fdb3f-261">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="fdb3f-262">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-262">The preceding commands:</span></span>

* <span data-ttu-id="fdb3f-263">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="fdb3f-264">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="fdb3f-265">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="fdb3f-266">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-266">The generated code:</span></span>

* <span data-ttu-id="fdb3f-267">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="fdb3f-268">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="fdb3f-269">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="fdb3f-270">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="fdb3f-271">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="fdb3f-272">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="fdb3f-273">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="fdb3f-274">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="fdb3f-275">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="fdb3f-276">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="fdb3f-277">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="fdb3f-278">更新 `PostTodoItem` 中的 return 语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-278">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="fdb3f-279">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="fdb3f-280">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="fdb3f-281">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="fdb3f-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="fdb3f-283">如果成功，将返回 [HTTP 201 状态代码](https://developer.mozilla.org/docs/Web/HTTP/Status/201)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="fdb3f-284">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="fdb3f-285">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="fdb3f-286">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="fdb3f-287">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="fdb3f-288">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="fdb3f-289">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="fdb3f-290">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="fdb3f-290">Install Postman</span></span>

<span data-ttu-id="fdb3f-291">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="fdb3f-292">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="fdb3f-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="fdb3f-293">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-293">Start the web app.</span></span>
* <span data-ttu-id="fdb3f-294">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-294">Start Postman.</span></span>
* <span data-ttu-id="fdb3f-295">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="fdb3f-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="fdb3f-296">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="fdb3f-297">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="fdb3f-298">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="fdb3f-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="fdb3f-299">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-299">Create a new request.</span></span>
* <span data-ttu-id="fdb3f-300">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="fdb3f-301">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="fdb3f-302">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="fdb3f-303">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="fdb3f-304">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="fdb3f-305">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="fdb3f-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="fdb3f-306">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="fdb3f-307">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-307">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="fdb3f-309">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="fdb3f-309">Test the location header URI</span></span>

<span data-ttu-id="fdb3f-310">你可以在浏览器中测试位置标头 URI。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="fdb3f-311">将位置标头 URI 复制粘贴到浏览器中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="fdb3f-312">若要在 Postman 中测试，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-312">To test in Postman:</span></span>

* <span data-ttu-id="fdb3f-313">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="fdb3f-314">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-314">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="fdb3f-316">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="fdb3f-317">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="fdb3f-318">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="fdb3f-319">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="fdb3f-320">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-320">Examine the GET methods</span></span>

<span data-ttu-id="fdb3f-321">实现了两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="fdb3f-322">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="fdb3f-323">例如：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="fdb3f-324">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="fdb3f-325">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="fdb3f-325">Test Get with Postman</span></span>

* <span data-ttu-id="fdb3f-326">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-326">Create a new request.</span></span>
* <span data-ttu-id="fdb3f-327">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="fdb3f-328">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="fdb3f-329">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="fdb3f-330">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="fdb3f-331">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-331">Select **Send**.</span></span>

<span data-ttu-id="fdb3f-332">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-332">This app uses an in-memory database.</span></span> <span data-ttu-id="fdb3f-333">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="fdb3f-334">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="fdb3f-335">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="fdb3f-335">Routing and URL paths</span></span>

<span data-ttu-id="fdb3f-336">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="fdb3f-337">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="fdb3f-338">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="fdb3f-339">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="fdb3f-340">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="fdb3f-341">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="fdb3f-342">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="fdb3f-343">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-343">This sample doesn't use a template.</span></span> <span data-ttu-id="fdb3f-344">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="fdb3f-345">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="fdb3f-346">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="fdb3f-347">返回值</span><span class="sxs-lookup"><span data-stu-id="fdb3f-347">Return values</span></span>

<span data-ttu-id="fdb3f-348">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="fdb3f-349">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="fdb3f-350">此返回类型的响应代码为 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)（假设没有未处理的异常）。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="fdb3f-351">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="fdb3f-352">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="fdb3f-353">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="fdb3f-354">如果没有任何项与请求的 ID 匹配，该方法将返回 [404 状态](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="fdb3f-355">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="fdb3f-356">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="fdb3f-357">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-357">The PutTodoItem method</span></span>

<span data-ttu-id="fdb3f-358">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="fdb3f-359">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="fdb3f-360">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="fdb3f-361">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="fdb3f-362">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="fdb3f-363">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="fdb3f-364">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="fdb3f-365">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="fdb3f-366">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="fdb3f-367">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="fdb3f-368">更新 ID 为 1 的待办事项并将其名称设置为 `"feed fish"`：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="fdb3f-369">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-369">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="fdb3f-371">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="fdb3f-372">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="fdb3f-373">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="fdb3f-374">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="fdb3f-375">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="fdb3f-376">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="fdb3f-377">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="fdb3f-378">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="fdb3f-378">Prevent over-posting</span></span>

<span data-ttu-id="fdb3f-379">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="fdb3f-380">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="fdb3f-381">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="fdb3f-382">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="fdb3f-383">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="fdb3f-384">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-384">A DTO may be used to:</span></span>

* <span data-ttu-id="fdb3f-385">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-385">Prevent over-posting.</span></span>
* <span data-ttu-id="fdb3f-386">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="fdb3f-387">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="fdb3f-388">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="fdb3f-389">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="fdb3f-390">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="fdb3f-391">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="fdb3f-392">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="fdb3f-393">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="fdb3f-394">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="fdb3f-395">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="fdb3f-396">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="fdb3f-397">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="fdb3f-398">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fdb3f-399">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-399">Create a web API project.</span></span>
> * <span data-ttu-id="fdb3f-400">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="fdb3f-401">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="fdb3f-402">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="fdb3f-403">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-403">Call the web API with Postman.</span></span>

<span data-ttu-id="fdb3f-404">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="fdb3f-405">概述</span><span class="sxs-lookup"><span data-stu-id="fdb3f-405">Overview</span></span>

<span data-ttu-id="fdb3f-406">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="fdb3f-407">API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-407">API</span></span> | <span data-ttu-id="fdb3f-408">描述</span><span class="sxs-lookup"><span data-stu-id="fdb3f-408">Description</span></span> | <span data-ttu-id="fdb3f-409">请求正文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-409">Request body</span></span> | <span data-ttu-id="fdb3f-410">响应正文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="fdb3f-411">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-411">Get all to-do items</span></span> | <span data-ttu-id="fdb3f-412">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-412">None</span></span> | <span data-ttu-id="fdb3f-413">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="fdb3f-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="fdb3f-414">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-414">Get an item by ID</span></span> | <span data-ttu-id="fdb3f-415">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-415">None</span></span> | <span data-ttu-id="fdb3f-416">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="fdb3f-417">添加新项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-417">Add a new item</span></span> | <span data-ttu-id="fdb3f-418">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-418">To-do item</span></span> | <span data-ttu-id="fdb3f-419">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="fdb3f-420">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="fdb3f-421">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-421">To-do item</span></span> | <span data-ttu-id="fdb3f-422">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-422">None</span></span> |
|<span data-ttu-id="fdb3f-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="fdb3f-424">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="fdb3f-425">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-425">None</span></span> | <span data-ttu-id="fdb3f-426">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-426">None</span></span>|

<span data-ttu-id="fdb3f-427">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-427">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="fdb3f-433">先决条件</span><span class="sxs-lookup"><span data-stu-id="fdb3f-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="fdb3f-437">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="fdb3f-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-439">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="fdb3f-440">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="fdb3f-441">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="fdb3f-442">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="fdb3f-443">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-443">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fdb3f-446">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fdb3f-447">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="fdb3f-448">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="fdb3f-449">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="fdb3f-450">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-450">The preceding commands:</span></span>

  * <span data-ttu-id="fdb3f-451">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="fdb3f-452">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-453">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fdb3f-454">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-454">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="fdb3f-456">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="fdb3f-457">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="fdb3f-459">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 3.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="fdb3f-460">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-460">Select **Next**.</span></span>

* <span data-ttu-id="fdb3f-461">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="fdb3f-463">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-463">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="fdb3f-464">测试 API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-464">Test the API</span></span>

<span data-ttu-id="fdb3f-465">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="fdb3f-466">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fdb3f-468">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="fdb3f-469">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="fdb3f-470">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="fdb3f-471">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fdb3f-473">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="fdb3f-474">在浏览器中，转到以下 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-475">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fdb3f-476">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="fdb3f-477">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="fdb3f-478">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="fdb3f-479">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="fdb3f-480">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="fdb3f-481">添加模型类</span><span class="sxs-lookup"><span data-stu-id="fdb3f-481">Add a model class</span></span>

<span data-ttu-id="fdb3f-482">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="fdb3f-483">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-485">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="fdb3f-486">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fdb3f-487">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="fdb3f-488">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fdb3f-489">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="fdb3f-490">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fdb3f-492">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="fdb3f-493">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-494">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fdb3f-495">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-495">Right-click the project.</span></span> <span data-ttu-id="fdb3f-496">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fdb3f-497">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-497">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="fdb3f-499">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="fdb3f-500">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="fdb3f-501">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="fdb3f-502">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="fdb3f-503">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="fdb3f-504">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-504">Add a database context</span></span>

<span data-ttu-id="fdb3f-505">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="fdb3f-506">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="fdb3f-508">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="fdb3f-508">Add NuGet packages</span></span>

* <span data-ttu-id="fdb3f-509">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="fdb3f-510">选择“浏览”选项卡，然后在搜索框中输入“Microsoft.EntityFrameworkCore.InMemory” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="fdb3f-511">在左窗格中选择“Microsoft.EntityFrameworkCore.InMemory”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-511">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="fdb3f-512">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="fdb3f-514">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-514">Add the TodoContext database context</span></span>

* <span data-ttu-id="fdb3f-515">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-515">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fdb3f-516">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-516">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-517">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-517">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="fdb3f-518">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-518">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="fdb3f-519">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-519">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="fdb3f-520">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-520">Register the database context</span></span>

<span data-ttu-id="fdb3f-521">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-521">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fdb3f-522">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-522">The container provides the service to controllers.</span></span>

<span data-ttu-id="fdb3f-523">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-523">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="fdb3f-524">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-524">The preceding code:</span></span>

* <span data-ttu-id="fdb3f-525">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-525">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="fdb3f-526">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-526">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="fdb3f-527">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-527">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="fdb3f-528">构建控制器</span><span class="sxs-lookup"><span data-stu-id="fdb3f-528">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-529">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-529">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-530">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-530">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="fdb3f-531">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-531">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="fdb3f-532">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-532">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="fdb3f-533">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-533">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="fdb3f-534">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-534">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="fdb3f-535">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-535">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="fdb3f-536">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-536">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-537">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-537">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fdb3f-538">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-538">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="fdb3f-539">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-539">The preceding commands:</span></span>

* <span data-ttu-id="fdb3f-540">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-540">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="fdb3f-541">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-541">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="fdb3f-542">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-542">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="fdb3f-543">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-543">The generated code:</span></span>

* <span data-ttu-id="fdb3f-544">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-544">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="fdb3f-545">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-545">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="fdb3f-546">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-546">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="fdb3f-547">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-547">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="fdb3f-548">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-548">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="fdb3f-549">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-549">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="fdb3f-550">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-550">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="fdb3f-551">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-551">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="fdb3f-552">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-552">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="fdb3f-553">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-553">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="fdb3f-554">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-554">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="fdb3f-555">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-555">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="fdb3f-556">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-556">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="fdb3f-557">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="fdb3f-558">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-558">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="fdb3f-559"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-559">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="fdb3f-560">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-560">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="fdb3f-561">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="fdb3f-562">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-562">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="fdb3f-563">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-563">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="fdb3f-564">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="fdb3f-565">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="fdb3f-566">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="fdb3f-567">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="fdb3f-567">Install Postman</span></span>

<span data-ttu-id="fdb3f-568">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-568">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="fdb3f-569">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="fdb3f-569">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="fdb3f-570">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-570">Start the web app.</span></span>
* <span data-ttu-id="fdb3f-571">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-571">Start Postman.</span></span>
* <span data-ttu-id="fdb3f-572">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="fdb3f-572">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="fdb3f-573">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-573">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="fdb3f-574">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-574">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="fdb3f-575">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="fdb3f-575">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="fdb3f-576">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-576">Create a new request.</span></span>
* <span data-ttu-id="fdb3f-577">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-577">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="fdb3f-578">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-578">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="fdb3f-579">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-579">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="fdb3f-580">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-580">Select the **Body** tab.</span></span>
* <span data-ttu-id="fdb3f-581">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-581">Select the **raw** radio button.</span></span>
* <span data-ttu-id="fdb3f-582">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="fdb3f-582">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="fdb3f-583">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-583">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="fdb3f-584">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-584">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="fdb3f-586">使用 Postman 测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="fdb3f-586">Test the location header URI with Postman</span></span>

* <span data-ttu-id="fdb3f-587">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-587">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="fdb3f-588">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-588">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="fdb3f-590">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-590">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="fdb3f-591">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-591">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="fdb3f-592">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-592">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="fdb3f-593">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-593">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="fdb3f-594">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-594">Examine the GET methods</span></span>

<span data-ttu-id="fdb3f-595">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-595">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="fdb3f-596">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-596">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="fdb3f-597">例如：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-597">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="fdb3f-598">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-598">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="fdb3f-599">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="fdb3f-599">Test Get with Postman</span></span>

* <span data-ttu-id="fdb3f-600">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-600">Create a new request.</span></span>
* <span data-ttu-id="fdb3f-601">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-601">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="fdb3f-602">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-602">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="fdb3f-603">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-603">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="fdb3f-604">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-604">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="fdb3f-605">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-605">Select **Send**.</span></span>

<span data-ttu-id="fdb3f-606">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-606">This app uses an in-memory database.</span></span> <span data-ttu-id="fdb3f-607">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-607">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="fdb3f-608">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-608">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="fdb3f-609">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="fdb3f-609">Routing and URL paths</span></span>

<span data-ttu-id="fdb3f-610">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-610">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="fdb3f-611">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-611">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="fdb3f-612">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-612">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="fdb3f-613">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-613">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="fdb3f-614">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-614">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="fdb3f-615">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-615">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="fdb3f-616">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-616">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="fdb3f-617">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-617">This sample doesn't use a template.</span></span> <span data-ttu-id="fdb3f-618">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-618">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="fdb3f-619">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-619">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="fdb3f-620">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-620">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="fdb3f-621">返回值</span><span class="sxs-lookup"><span data-stu-id="fdb3f-621">Return values</span></span> 

<span data-ttu-id="fdb3f-622">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-622">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="fdb3f-623">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-623">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="fdb3f-624">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-624">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="fdb3f-625">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-625">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="fdb3f-626">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-626">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="fdb3f-627">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-627">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="fdb3f-628">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-628">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="fdb3f-629">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-629">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="fdb3f-630">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-630">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="fdb3f-631">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-631">The PutTodoItem method</span></span>

<span data-ttu-id="fdb3f-632">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-632">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="fdb3f-633">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-633">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="fdb3f-634">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-634">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="fdb3f-635">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-635">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="fdb3f-636">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-636">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="fdb3f-637">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-637">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="fdb3f-638">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-638">Test the PutTodoItem method</span></span>

<span data-ttu-id="fdb3f-639">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-639">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="fdb3f-640">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-640">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="fdb3f-641">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-641">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="fdb3f-642">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-642">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="fdb3f-643">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-643">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="fdb3f-645">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-645">The DeleteTodoItem method</span></span>

<span data-ttu-id="fdb3f-646">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-646">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="fdb3f-647">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="fdb3f-647">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="fdb3f-648">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-648">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="fdb3f-649">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-649">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="fdb3f-650">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-650">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="fdb3f-651">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-651">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="fdb3f-652">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="fdb3f-652">Prevent over-posting</span></span>

<span data-ttu-id="fdb3f-653">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-653">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="fdb3f-654">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-654">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="fdb3f-655">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-655">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="fdb3f-656">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-656">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="fdb3f-657">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-657">**DTO** is used in this article.</span></span>

<span data-ttu-id="fdb3f-658">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-658">A DTO may be used to:</span></span>

* <span data-ttu-id="fdb3f-659">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-659">Prevent over-posting.</span></span>
* <span data-ttu-id="fdb3f-660">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-660">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="fdb3f-661">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-661">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="fdb3f-662">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-662">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="fdb3f-663">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-663">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="fdb3f-664">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-664">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="fdb3f-665">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-665">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="fdb3f-666">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-666">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="fdb3f-667">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-667">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="fdb3f-668">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-668">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="fdb3f-669">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-669">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="fdb3f-670">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-670">Call the web API with JavaScript</span></span>

<span data-ttu-id="fdb3f-671">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-671">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fdb3f-672">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-672">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fdb3f-673">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-673">Create a web API project.</span></span>
> * <span data-ttu-id="fdb3f-674">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-674">Add a model class and a database context.</span></span>
> * <span data-ttu-id="fdb3f-675">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-675">Add a controller.</span></span>
> * <span data-ttu-id="fdb3f-676">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-676">Add CRUD methods.</span></span>
> * <span data-ttu-id="fdb3f-677">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-677">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="fdb3f-678">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-678">Specify return values.</span></span>
> * <span data-ttu-id="fdb3f-679">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-679">Call the web API with Postman.</span></span>
> * <span data-ttu-id="fdb3f-680">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-680">Call the web API with JavaScript.</span></span>

<span data-ttu-id="fdb3f-681">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-681">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="fdb3f-682">概述 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-682">Overview 2.1</span></span>

<span data-ttu-id="fdb3f-683">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-683">This tutorial creates the following API:</span></span>

|<span data-ttu-id="fdb3f-684">API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-684">API</span></span> | <span data-ttu-id="fdb3f-685">描述</span><span class="sxs-lookup"><span data-stu-id="fdb3f-685">Description</span></span> | <span data-ttu-id="fdb3f-686">请求正文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-686">Request body</span></span> | <span data-ttu-id="fdb3f-687">响应正文</span><span class="sxs-lookup"><span data-stu-id="fdb3f-687">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="fdb3f-688">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="fdb3f-688">GET /api/TodoItems</span></span> | <span data-ttu-id="fdb3f-689">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-689">Get all to-do items</span></span> | <span data-ttu-id="fdb3f-690">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-690">None</span></span> | <span data-ttu-id="fdb3f-691">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="fdb3f-691">Array of to-do items</span></span>|
|<span data-ttu-id="fdb3f-692">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="fdb3f-692">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="fdb3f-693">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-693">Get an item by ID</span></span> | <span data-ttu-id="fdb3f-694">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-694">None</span></span> | <span data-ttu-id="fdb3f-695">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-695">To-do item</span></span>|
|<span data-ttu-id="fdb3f-696">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="fdb3f-696">POST /api/TodoItems</span></span> | <span data-ttu-id="fdb3f-697">添加新项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-697">Add a new item</span></span> | <span data-ttu-id="fdb3f-698">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-698">To-do item</span></span> | <span data-ttu-id="fdb3f-699">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-699">To-do item</span></span> |
|<span data-ttu-id="fdb3f-700">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="fdb3f-700">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="fdb3f-701">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-701">Update an existing item &nbsp;</span></span> | <span data-ttu-id="fdb3f-702">待办事项</span><span class="sxs-lookup"><span data-stu-id="fdb3f-702">To-do item</span></span> | <span data-ttu-id="fdb3f-703">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-703">None</span></span> |
|<span data-ttu-id="fdb3f-704">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-704">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="fdb3f-705">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="fdb3f-705">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="fdb3f-706">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-706">None</span></span> | <span data-ttu-id="fdb3f-707">None</span><span class="sxs-lookup"><span data-stu-id="fdb3f-707">None</span></span>|

<span data-ttu-id="fdb3f-708">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-708">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="fdb3f-714">先决条件 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-714">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-715">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-715">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-716">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-716">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-717">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-717">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="fdb3f-718">创建 Web 项目 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-718">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-719">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-719">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-720">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-720">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="fdb3f-721">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-721">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="fdb3f-722">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-722">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="fdb3f-723">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-723">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="fdb3f-724">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-724">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="fdb3f-725">请不要选择“启用 Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-725">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-727">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-727">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fdb3f-728">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-728">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fdb3f-729">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-729">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="fdb3f-730">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-730">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="fdb3f-731">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-731">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="fdb3f-732">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-732">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-733">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-733">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fdb3f-734">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-734">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="fdb3f-736">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-736">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="fdb3f-737">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-737">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="fdb3f-738">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 2.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-738">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="fdb3f-739">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-739">Select **Next**.</span></span>

* <span data-ttu-id="fdb3f-740">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-740">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="fdb3f-742">测试 API 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-742">Test the API 2.1</span></span>

<span data-ttu-id="fdb3f-743">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-743">The project template creates a `values` API.</span></span> <span data-ttu-id="fdb3f-744">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-744">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-745">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-745">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fdb3f-746">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-746">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="fdb3f-747">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-747">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="fdb3f-748">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-748">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="fdb3f-749">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-749">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-750">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-750">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fdb3f-751">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-751">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="fdb3f-752">在浏览器中，转到以下 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-752">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-753">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-753">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fdb3f-754">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-754">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="fdb3f-755">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-755">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="fdb3f-756">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-756">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="fdb3f-757">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-757">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="fdb3f-758">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-758">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="fdb3f-759">添加模型类 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-759">Add a model class 2.1</span></span>

<span data-ttu-id="fdb3f-760">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-760">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="fdb3f-761">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-761">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-762">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-763">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-763">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="fdb3f-764">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-764">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fdb3f-765">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-765">Name the folder *Models*.</span></span>

* <span data-ttu-id="fdb3f-766">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-766">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fdb3f-767">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-767">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="fdb3f-768">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-768">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fdb3f-769">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fdb3f-769">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fdb3f-770">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-770">Add a folder named *Models*.</span></span>

* <span data-ttu-id="fdb3f-771">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-771">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-772">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-772">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fdb3f-773">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-773">Right-click the project.</span></span> <span data-ttu-id="fdb3f-774">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-774">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="fdb3f-775">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-775">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="fdb3f-777">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-777">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="fdb3f-778">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-778">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="fdb3f-779">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-779">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="fdb3f-780">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-780">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="fdb3f-781">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-781">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="fdb3f-782">添加数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-782">Add a database context 2.1</span></span>

<span data-ttu-id="fdb3f-783">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-783">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="fdb3f-784">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-784">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-785">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-785">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-786">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-786">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="fdb3f-787">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-787">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-788">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-788">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="fdb3f-789">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-789">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="fdb3f-790">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-790">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="fdb3f-791">注册数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-791">Register the database context 2.1</span></span>

<span data-ttu-id="fdb3f-792">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-792">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fdb3f-793">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-793">The container provides the service to controllers.</span></span>

<span data-ttu-id="fdb3f-794">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-794">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="fdb3f-795">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-795">The preceding code:</span></span>

* <span data-ttu-id="fdb3f-796">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-796">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="fdb3f-797">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-797">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="fdb3f-798">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-798">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="fdb3f-799">添加控制器 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-799">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-800">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-800">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-801">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-801">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="fdb3f-802">选择“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-802">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="fdb3f-803">在“添加新项”对话框中，选择“API 控制器类”模板 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-803">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="fdb3f-804">将类命名为 TodoController，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-804">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-806">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-806">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="fdb3f-807">在“控制器”文件夹中，创建名为 `TodoController` 的类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-807">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="fdb3f-808">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-808">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="fdb3f-809">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-809">The preceding code:</span></span>

* <span data-ttu-id="fdb3f-810">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-810">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="fdb3f-811">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-811">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="fdb3f-812">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-812">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="fdb3f-813">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-813">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="fdb3f-814">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-814">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="fdb3f-815">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-815">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="fdb3f-816">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-816">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="fdb3f-817">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-817">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="fdb3f-818">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-818">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="fdb3f-819">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-819">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="fdb3f-820">添加 Get 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-820">Add Get methods 2.1</span></span>

<span data-ttu-id="fdb3f-821">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-821">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="fdb3f-822">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-822">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="fdb3f-823">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-823">Stop the app if it's still running.</span></span> <span data-ttu-id="fdb3f-824">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-824">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="fdb3f-825">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-825">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="fdb3f-826">例如：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-826">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="fdb3f-827">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-827">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="fdb3f-828">路由和 URL 路径 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-828">Routing and URL paths 2.1</span></span>

<span data-ttu-id="fdb3f-829">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-829">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="fdb3f-830">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-830">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="fdb3f-831">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-831">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="fdb3f-832">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-832">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="fdb3f-833">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-833">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="fdb3f-834">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-834">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="fdb3f-835">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-835">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="fdb3f-836">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-836">This sample doesn't use a template.</span></span> <span data-ttu-id="fdb3f-837">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-837">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="fdb3f-838">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-838">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="fdb3f-839">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-839">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="fdb3f-840">返回值 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-840">Return values 2.1</span></span>

<span data-ttu-id="fdb3f-841">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-841">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="fdb3f-842">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-842">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="fdb3f-843">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-843">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="fdb3f-844">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-844">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="fdb3f-845">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-845">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="fdb3f-846">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-846">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="fdb3f-847">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-847">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="fdb3f-848">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-848">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="fdb3f-849">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-849">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="fdb3f-850">测试 GetTodoItems 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-850">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="fdb3f-851">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-851">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="fdb3f-852">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-852">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="fdb3f-853">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-853">Start the web app.</span></span>
* <span data-ttu-id="fdb3f-854">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-854">Start Postman.</span></span>
* <span data-ttu-id="fdb3f-855">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-855">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fdb3f-856">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fdb3f-856">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fdb3f-857">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-857">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fdb3f-858">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fdb3f-858">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="fdb3f-859">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”   。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-859">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="fdb3f-860">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-860">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="fdb3f-861">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-861">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="fdb3f-862">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-862">Create a new request.</span></span>
  * <span data-ttu-id="fdb3f-863">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-863">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="fdb3f-864">将请求 URI 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-864">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="fdb3f-865">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-865">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="fdb3f-866">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-866">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="fdb3f-867">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-867">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="fdb3f-869">添加 Create 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-869">Add a Create method 2.1</span></span>

<span data-ttu-id="fdb3f-870">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-870">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="fdb3f-871">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-871">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="fdb3f-872">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-872">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="fdb3f-873">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-873">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="fdb3f-874">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-874">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="fdb3f-875">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-875">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="fdb3f-876">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-876">Adds a `Location` header to the response.</span></span> <span data-ttu-id="fdb3f-877">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-877">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="fdb3f-878">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-878">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="fdb3f-879">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-879">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="fdb3f-880">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-880">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="fdb3f-881">测试 PostTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-881">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="fdb3f-882">生成项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-882">Build the project.</span></span>
* <span data-ttu-id="fdb3f-883">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-883">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="fdb3f-884">将 URI 设置为 `https://localhost:<port>/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-884">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="fdb3f-885">例如 `https://localhost:5001/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-885">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="fdb3f-886">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-886">Select the **Body** tab.</span></span>
* <span data-ttu-id="fdb3f-887">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-887">Select the **raw** radio button.</span></span>
* <span data-ttu-id="fdb3f-888">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="fdb3f-888">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="fdb3f-889">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-889">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="fdb3f-890">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-890">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="fdb3f-892">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-892">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="fdb3f-893">测试位置标头 URI 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-893">Test the location header URI 2.1</span></span>

* <span data-ttu-id="fdb3f-894">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-894">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="fdb3f-895">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-895">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="fdb3f-897">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-897">Set the method to GET.</span></span>
* <span data-ttu-id="fdb3f-898">将 URI 设置为 `https://localhost:<port>/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-898">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="fdb3f-899">例如 `https://localhost:5001/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-899">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="fdb3f-900">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-900">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="fdb3f-901">添加 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-901">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="fdb3f-902">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-902">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="fdb3f-903">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-903">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="fdb3f-904">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-904">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="fdb3f-905">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-905">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="fdb3f-906">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-906">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="fdb3f-907">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-907">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="fdb3f-908">测试 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-908">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="fdb3f-909">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-909">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="fdb3f-910">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-910">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="fdb3f-911">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-911">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="fdb3f-912">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-912">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="fdb3f-913">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-913">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="fdb3f-915">添加 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-915">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="fdb3f-916">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-916">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="fdb3f-917">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-917">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="fdb3f-918">测试 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-918">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="fdb3f-919">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-919">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="fdb3f-920">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-920">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="fdb3f-921">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-921">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="fdb3f-922">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-922">Select **Send**.</span></span>

<span data-ttu-id="fdb3f-923">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-923">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="fdb3f-924">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-924">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="fdb3f-925">使用 JavaScript 调用 Web API 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-925">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="fdb3f-926">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-926">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="fdb3f-927">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-927">jQuery initiates the request.</span></span> <span data-ttu-id="fdb3f-928">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-928">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="fdb3f-929">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)并[实现默认文件映射](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-929">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="fdb3f-930">在项目目录中创建 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-930">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="fdb3f-931">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-931">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="fdb3f-932">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-932">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="fdb3f-933">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-933">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="fdb3f-934">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-934">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="fdb3f-935">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-935">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="fdb3f-936">打开 Properties\launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-936">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="fdb3f-937">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-937">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="fdb3f-938">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-938">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="fdb3f-939">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-939">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="fdb3f-940">获取待办事项列表 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-940">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="fdb3f-941">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-941">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="fdb3f-942">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-942">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="fdb3f-943">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-943">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="fdb3f-944">添加待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-944">Add a to-do item 2.1</span></span>

<span data-ttu-id="fdb3f-945">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-945">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="fdb3f-946">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-946">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="fdb3f-947">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-947">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="fdb3f-948">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-948">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="fdb3f-949">更新待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-949">Update a to-do item 2.1</span></span>

<span data-ttu-id="fdb3f-950">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-950">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="fdb3f-951">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-951">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="fdb3f-952">删除待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-952">Delete a to-do item 2.1</span></span>

<span data-ttu-id="fdb3f-953">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-953">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="fdb3f-954">向 Web API 添加身份验证支持 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-954">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="fdb3f-955">其他资源 2.1</span><span class="sxs-lookup"><span data-stu-id="fdb3f-955">Additional resources 2.1</span></span>

<span data-ttu-id="fdb3f-956">[查看或下载本教程的示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-956">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="fdb3f-957">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="fdb3f-957">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="fdb3f-958">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="fdb3f-958">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="fdb3f-959">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="fdb3f-959">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="fdb3f-960">Microsoft Learn：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="fdb3f-960">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
