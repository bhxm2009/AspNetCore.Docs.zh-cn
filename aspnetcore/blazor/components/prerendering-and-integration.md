---
title: 预呈现和集成 ASP.NET Core Razor 组件
author: guardrex
description: 了解 Blazor 应用的 Razor 组件集成方案，包括在服务器上预呈现 Razor 组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2021
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d0be30e14c67119f0aadaaab415cf0ae53e82915
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081515"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="aeeaa-103">预呈现和集成 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aeeaa-104">Razor 组件可以通过托管的 Blazor WebAssembly 解决方案集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="aeeaa-105">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="solution-configuration"></a><span data-ttu-id="aeeaa-106">解决方案配置</span><span class="sxs-lookup"><span data-stu-id="aeeaa-106">Solution configuration</span></span>

### <a name="prerendering-configuration"></a><span data-ttu-id="aeeaa-107">预呈现配置</span><span class="sxs-lookup"><span data-stu-id="aeeaa-107">Prerendering configuration</span></span>

<span data-ttu-id="aeeaa-108">若要设置托管 Blazor WebAssembly 应用的预呈现，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-108">To set up prerendering for a hosted Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="aeeaa-109">将 Blazor WebAssembly 应用托管在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="aeeaa-110">可以将独立的 Blazor WebAssembly 应用添加到 ASP.NET Core 解决方案中，也可以使用通过托管选项从 [Blazor WebAssembly 项目模板](xref:blazor/tooling)创建的托管 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the [Blazor WebAssembly project template](xref:blazor/tooling) with the hosted option:</span></span>

   * <span data-ttu-id="aeeaa-111">Visual Studio：创建 Blazor WebAssembly 应用时，选中“高级” > “ASP.NET Core 托管”复选框。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-111">Visual Studio: Select the **Advanced** > **ASP.NET Core hosted** check box when creating the Blazor WebAssembly app.</span></span> <span data-ttu-id="aeeaa-112">在本文的示例中，该解决方案的名称为 `BlazorHosted`。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-112">In this article's examples, the solution is named `BlazorHosted`.</span></span>
   * <span data-ttu-id="aeeaa-113">Visual Studio Code/.NET CLI 命令行界面：`dotnet new blazorwasm -ho`（使用 `-ho|--hosted` 选项）。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-113">Visual Studio Code/.NET CLI command shell: `dotnet new blazorwasm -ho` (use the `-ho|--hosted` option).</span></span> <span data-ttu-id="aeeaa-114">使用 `-o|--output {LOCATION}` 选项为解决方案创建文件夹，并设置解决方案的项目命名空间。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-114">Use the `-o|--output {LOCATION}` option to create a folder for the solution and set the solution's project namespaces.</span></span> <span data-ttu-id="aeeaa-115">在本文的示例中，该解决方案的名称为 `BlazorHosted` (`dotnet new blazorwasm -ho -o BlazorHosted`)。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-115">In this article's examples, the solution is named `BlazorHosted` (`dotnet new blazorwasm -ho -o BlazorHosted`).</span></span>

   <span data-ttu-id="aeeaa-116">对于本文中的示例，客户端项目的命名空间为 `BlazorHosted.Client`，服务器项目的命名空间为 `BlazorHosted.Server`。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-116">For the examples in this article, the client project's namespace is `BlazorHosted.Client`, and the server project's namespace is `BlazorHosted.Server`.</span></span>

1. <span data-ttu-id="aeeaa-117">从 Blazor WebAssembly `Client` 项目删除 `wwwroot/index.html` 文件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-117">**Delete** the `wwwroot/index.html` file from the Blazor WebAssembly **`Client`** project.</span></span>

1. <span data-ttu-id="aeeaa-118">在 `Client` 项目中，删除 `Program.Main` (`Program.cs`) 中的以下行：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-118">In the **`Client`** project, **delete** the following line in `Program.Main` (`Program.cs`):</span></span>

   ```diff
   - builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="aeeaa-119">将 `Pages/_Host.cshtml` 文件添加到 `Server` 项目的 `Pages` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-119">Add a `Pages/_Host.cshtml` file to the **`Server`** project's `Pages` folder.</span></span> <span data-ttu-id="aeeaa-120">可以使用命令行界面中的 `dotnet new blazorserver -o BlazorServer` 命令获得 Blazor Server 模板创建的项目的 `_Host.cshtml` 文件（`-o BlazorServer` 选项为项目创建文件夹）。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-120">You can obtain a `_Host.cshtml` file from a project created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell (the `-o BlazorServer` option creates a folder for the project).</span></span> <span data-ttu-id="aeeaa-121">将 `Pages/_Host.cshtml` 文件放入托管 Blazor WebAssembly 解决方案的 `Server` 项目后，对此文件进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-121">After placing the `Pages/_Host.cshtml` file into the **`Server`** project of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="aeeaa-122">为 `Client` 项目提供 [`@using`](xref:mvc/views/razor#using) 指令（例如 `@using BlazorHosted.Client`）。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-122">Provide an [`@using`](xref:mvc/views/razor#using) directive for the **`Client`** project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="aeeaa-123">更新样式表链接，以指向 WebAssembly 项目的样式表。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-123">Update the stylesheet links to point to the WebAssembly project's stylesheets.</span></span> <span data-ttu-id="aeeaa-124">在下面的示例中，客户端项目的命名空间为 `BlazorHosted.Client`：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-124">In the following example, the client project's namespace is `BlazorHosted.Client`:</span></span>

     ```diff
     - <link href="css/site.css" rel="stylesheet" />
     - <link href="_content/BlazorServer/_framework/scoped.styles.css" rel="stylesheet" />
     + <link href="css/app.css" rel="stylesheet" />
     + <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

     > [!NOTE]
     > <span data-ttu-id="aeeaa-125">就地保留请求启动样式 (`css/bootstrap/bootstrap.min.css`) 表的 `<link>` 元素。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-125">Leave the `<link>` element that requests the Bootstrap stylesheet (`css/bootstrap/bootstrap.min.css`) in place.</span></span>

   * <span data-ttu-id="aeeaa-126">更新[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode`，以使用 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 预呈现根 `App` 组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-126">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component with <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>:</span></span>

     ```diff
     - <component type="typeof(App)" render-mode="ServerPrerendered" />
     + <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="aeeaa-127">更新 Blazor 脚本源，以使用客户端 Blazor WebAssembly 脚本：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-127">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```diff
     - <script src="_framework/blazor.server.js"></script>
     + <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="aeeaa-128">在 `Server` 项目的 `Startup.Configure` 中，将回退从 `index.html` 文件更改为 `_Host.cshtml` 页面。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-128">In `Startup.Configure` of the **`Server`** project, change the fallback from the `index.html` file to the `_Host.cshtml` page.</span></span>

   <span data-ttu-id="aeeaa-129">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-129">`Startup.cs`:</span></span>

   ```diff
   - endpoints.MapFallbackToFile("index.html");
   + endpoints.MapFallbackToPage("/_Host");
   ```

1. <span data-ttu-id="aeeaa-130">运行 `Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-130">Run the **`Server`** project.</span></span> <span data-ttu-id="aeeaa-131">托管 Blazor WebAssembly 应用由客户端的 `Server` 项目预呈现。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-131">The hosted Blazor WebAssembly app is prerendered by the **`Server`** project for clients.</span></span>

### <a name="configuration-for-embedding-razor-components-into-pages-and-views"></a><span data-ttu-id="aeeaa-132">用于将 Razor 组件嵌入到页面和视图中的配置</span><span class="sxs-lookup"><span data-stu-id="aeeaa-132">Configuration for embedding Razor components into pages and views</span></span>

<span data-ttu-id="aeeaa-133">本文中将客户端 Razor 应用的 Blazor WebAssembly 组件嵌入到服务器应用的页面和视图中的以下部分和示例需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-133">The following sections and examples in this article for embedding Razor components of the client Blazor WebAssembly app into pages and views of the server app require additional configuration.</span></span>

<span data-ttu-id="aeeaa-134">使用 `Server` 项目中的默认 Razor Pages 或 MVC 布局文件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-134">Use a default Razor Pages or MVC layout file in the **`Server`** project.</span></span> <span data-ttu-id="aeeaa-135">`Server` 项目必须具有以下文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-135">The **`Server`** project must have the following files and folders.</span></span>

<span data-ttu-id="aeeaa-136">Razor Pages：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-136">Razor Pages:</span></span>

* `Pages/Shared/_Layout.cshtml`
* `Pages/_ViewImports.cshtml`
* `Pages/_ViewStart.cshtml`

<span data-ttu-id="aeeaa-137">MVC：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-137">MVC:</span></span>

* `Views/Shared/_Layout.cshtml`
* `Views/_ViewImports.cshtml`
* `Views/_ViewStart.cshtml`

<span data-ttu-id="aeeaa-138">从 Razor Pages 或 MVC 项目模板创建的应用获取上述文件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-138">Obtain the preceding files from an app created from the Razor Pages or MVC project template.</span></span> <span data-ttu-id="aeeaa-139">有关详细信息，请参阅<xref:tutorials/razor-pages/razor-pages-start>或<xref:tutorials/first-mvc-app/start-mvc>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-139">For more information, see <xref:tutorials/razor-pages/razor-pages-start> or <xref:tutorials/first-mvc-app/start-mvc>.</span></span>

<span data-ttu-id="aeeaa-140">更新导入的 `_ViewImports.cshtml` 文件中的命名空间，使其与接收文件的 `Server` 项目所使用的命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-140">Update the namespaces in the imported `_ViewImports.cshtml` file to match those in use by the **`Server`** project receiving the files.</span></span>

<span data-ttu-id="aeeaa-141">更新导入的布局文件 (`_Layout.cshtml`) 以包含 `Client` 项目的样式。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-141">Update the imported layout file (`_Layout.cshtml`) to include the **`Client`** project's styles.</span></span> <span data-ttu-id="aeeaa-142">在下面的示例中，`Client` 项目的命名空间为 `BlazorHosted.Client`。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-142">In the following example, the **`Client`** project's namespace is `BlazorHosted.Client`.</span></span> <span data-ttu-id="aeeaa-143">可以同时更新的 `<title>` 元素。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-143">The `<title>` element can be updated at the same time.</span></span>

<span data-ttu-id="aeeaa-144">`Pages/Shared/_Layout.cshtml` (Razor Pages) 或 `Views/Shared/_Layout.cshtml` (MVC)：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-144">`Pages/Shared/_Layout.cshtml` (Razor Pages) or `Views/Shared/_Layout.cshtml` (MVC):</span></span>

```diff
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
-   <title>@ViewData["Title"] - DonorProject</title>
+   <title>@ViewData["Title"] - BlazorHosted</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" />
+   <link href="css/app.css" rel="stylesheet" />
+   <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="aeeaa-145">导入的布局包含 `Home` 和 `Privacy` 导航链接。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-145">The imported layout contains `Home` and `Privacy` navigation links.</span></span> <span data-ttu-id="aeeaa-146">若要使 `Home` 链接指向托管 Blazor WebAssembly 应用，请更改超链接：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-146">To make the `Home` link point to the hosted Blazor WebAssembly app, change the hyperlink:</span></span>

```diff
- <a class="nav-link text-dark" asp-area="" asp-page="/Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

<span data-ttu-id="aeeaa-147">在 MVC 布局文件中：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-147">In an MVC layout file:</span></span>

```diff
- <a class="nav-link text-dark" asp-area="" asp-controller="Home" 
-     asp-action="Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

<span data-ttu-id="aeeaa-148">若要使 `Privacy` 链接指向隐私页，请将隐私页添加到 `Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-148">To make the `Privacy` link lead to a privacy page, add a privacy page to the **`Server`** project.</span></span>

<span data-ttu-id="aeeaa-149">`Server` 项目中的 `Pages/Privacy.cshtml`：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-149">`Pages/Privacy.cshtml` in the **`Server`** project:</span></span>

```cshtml
@page
@model BlazorHosted.Server.Pages.PrivacyModel
@{
}

<h1>Privacy Policy</h1>
```

<span data-ttu-id="aeeaa-150">如果首选基于 MVC 的隐私视图，请在 `Server` 项目中创建一个隐私视图。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-150">If an MVC-based privacy view is preferred, create a privacy view in the **`Server`** project.</span></span>

<span data-ttu-id="aeeaa-151">`View/Home/Privacy.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-151">`View/Home/Privacy.cshtml`:</span></span>

```cshtml
@{
    ViewData["Title"] = "Privacy Policy";
}

<h1>@ViewData["Title"]</h1>
```

<span data-ttu-id="aeeaa-152">在 `Home` 控制器中，返回视图。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-152">In the `Home` controller, return the view.</span></span>

<span data-ttu-id="aeeaa-153">`Controllers/HomeController.cs`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-153">`Controllers/HomeController.cs`:</span></span>

```diff
+ public IActionResult Privacy()
+ {
+     return View();
+ }
```

<span data-ttu-id="aeeaa-154">从赞助商项目的 `wwwroot` 文件夹中将静态资产导入到 **`Server`** 项目：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-154">Import static assets to the **`Server`** project from the donor project's `wwwroot` folder:</span></span>

* <span data-ttu-id="aeeaa-155">`wwwroot/css` 文件夹和内容</span><span class="sxs-lookup"><span data-stu-id="aeeaa-155">`wwwroot/css` folder and contents</span></span>
* <span data-ttu-id="aeeaa-156">`wwwroot/js` 文件夹和内容</span><span class="sxs-lookup"><span data-stu-id="aeeaa-156">`wwwroot/js` folder and contents</span></span>
* <span data-ttu-id="aeeaa-157">`wwwroot/lib` 文件夹和内容</span><span class="sxs-lookup"><span data-stu-id="aeeaa-157">`wwwroot/lib` folder and contents</span></span>

<span data-ttu-id="aeeaa-158">如果从 ASP.NET Core 项目模板创建了赞助商项目，但未修改文件，则可以将整个 `wwwroot` 文件夹从赞助商项目复制到 `Server` 项目中，并删除 `favicon.ico` 图标文件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-158">If the donor project is created from an ASP.NET Core project template and the files aren't modified, you can copy the entire `wwwroot` folder from the donor project into the **`Server`** project and remove the `favicon.ico` icon file.</span></span>

> [!NOTE]
> <span data-ttu-id="aeeaa-159">如果 `Client` 和 `Server` 项目在其 `wwwroot` 文件夹中包含相同的静态资产（例如 `favicon.ico`），则会引发异常，因为每个文件夹中的静态资产共享同一个 Web 根路径：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-159">If the **`Client`** and **`Server`** projects contain the same static asset in their `wwwroot` folders (for example, `favicon.ico`), an exception is thrown because the static asset in each folder shares the same web root path:</span></span>
>
> > <span data-ttu-id="aeeaa-160">静态 Web 资源“...\favicon.ico”具有与项目文件“wwwroot\favicon.ico”冲突的 Web 根路径“/wwwroot/favicon.ico”。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-160">The static web asset '...\favicon.ico' has a conflicting web root path '/wwwroot/favicon.ico' with the project file 'wwwroot\favicon.ico'.</span></span>
>
> <span data-ttu-id="aeeaa-161">因此，在任意一个 `wwwroot` 文件夹（而不是两者）中托管静态资产。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-161">Therefore, host a static asset in either `wwwroot` folder, not both.</span></span>

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="aeeaa-162">使用组件标记帮助程序，通过页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-162">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="aeeaa-163">[配置解决方案](#solution-configuration)（包括[其他配置](#configuration-for-embedding-razor-components-into-pages-and-views)）后，[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)支持两种呈现模式来通过页面或视图呈现 Blazor WebAssembly 应用中的组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-163">After [configuring the solution](#solution-configuration), including the [additional configuration](#configuration-for-embedding-razor-components-into-pages-and-views), the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssembly>
* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>

<span data-ttu-id="aeeaa-164">在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-164">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="aeeaa-165">页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-165">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="aeeaa-166">为客户端项目的 `Pages` 命名空间添加 [`@using`](xref:mvc/views/razor#using) 指令，以避免将 `Counter` 组件的整个命名空间用于[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) (`{APP ASSEMBLY}.Pages.Counter`)。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-166">To avoid using the full namespace for the `Counter` component with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client project's `Pages` namespace.</span></span> <span data-ttu-id="aeeaa-167">在下面的示例中，`Client` 项目的命名空间为 `BlazorHosted.Client`。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-167">In the following example, the **`Client`** project's namespace is `BlazorHosted.Client`.</span></span>

<span data-ttu-id="aeeaa-168">在 `Server` 项目中，`Pages/RazorPagesCounter1.cshtml`：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-168">In the **`Server`** project, `Pages/RazorPagesCounter1.cshtml`:</span></span>

```cshtml
@page
@using BlazorHosted.Client.Pages

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="aeeaa-169">运行 `Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-169">Run the **`Server`** project.</span></span> <span data-ttu-id="aeeaa-170">导航到 `/razorpagescounter1` 中的 Razor 页。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-170">Navigate to the Razor page at `/razorpagescounter1`.</span></span> <span data-ttu-id="aeeaa-171">预呈现的 `Counter` 组件嵌入在页面中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-171">The prerendered `Counter` component is embedded in the page.</span></span>

<span data-ttu-id="aeeaa-172"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-172"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="aeeaa-173">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-173">Is prerendered into the page.</span></span>
* <span data-ttu-id="aeeaa-174">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-174">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="aeeaa-175">有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-175">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="aeeaa-176">可能需要额外的工作，具体取决于组件使用的静态资源以及布局页面在应用中的组织方式。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-176">Additional work might be required depending on the static resources that components use and how layout pages are organized in an app.</span></span> <span data-ttu-id="aeeaa-177">通常，脚本添加到了页面或视图的 `Scripts` 呈现部分，样式表添加到了布局的 `<head>` 元素内容。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-177">Typically, scripts are added to a page or view's `Scripts` render section and stylesheets are added to the layout's `<head>` element content.</span></span>

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="aeeaa-178">使用 CSS 选择器，通过页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-178">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="aeeaa-179">[配置解决方案](#solution-configuration)（包括[其他配置](#configuration-for-embedding-razor-components-into-pages-and-views)）后，将根组件添加到 `Program.Main` 中的托管 Blazor WebAssembly 解决方案的 `Client` 项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-179">After [configuring the solution](#solution-configuration), including the [additional configuration](#configuration-for-embedding-razor-components-into-pages-and-views), add root components to the **`Client`** project of a hosted Blazor WebAssembly solution in `Program.Main`.</span></span> <span data-ttu-id="aeeaa-180">下面的示例使用 CSS 选择器将 `Counter` 组件声明为根组件，该选择器会选择 `id` 与 `counter-component` 匹配的元素。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-180">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `counter-component`.</span></span> <span data-ttu-id="aeeaa-181">在下面的示例中，`Client` 项目的命名空间为 `BlazorHosted.Client`。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-181">In the following example, the **`Client`** project's namespace is `BlazorHosted.Client`.</span></span>

<span data-ttu-id="aeeaa-182">在 `Client` 项目的 `Program.cs` 中，将项目的 Razor 组件的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-182">In `Program.cs` of the **`Client`** project, add the namespace for the project's Razor components to the top of the file:</span></span>

```diff
+ using BlazorHosted.Client.Pages;
```

<span data-ttu-id="aeeaa-183">在 `Program.Main` 中建立 `builder` 后，将 `Counter` 组件添加为根组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-183">After the `builder` is established in `Program.Main`, add the `Counter` component as a root component:</span></span>

```diff
+ builder.RootComponents.Add<Counter>("#counter-component");
```

<span data-ttu-id="aeeaa-184">在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-184">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="aeeaa-185">页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-185">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span>

<span data-ttu-id="aeeaa-186">在 `Server` 项目中，`Pages/RazorPagesCounter2.cshtml`：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-186">In the **`Server`** project, `Pages/RazorPagesCounter2.cshtml`:</span></span>

```cshtml
@page

<div id="counter-component">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="aeeaa-187">运行 `Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-187">Run the **`Server`** project.</span></span> <span data-ttu-id="aeeaa-188">导航到 `/razorpagescounter2` 中的 Razor 页。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-188">Navigate to the Razor page at `/razorpagescounter2`.</span></span> <span data-ttu-id="aeeaa-189">预呈现的 `Counter` 组件嵌入在页面中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-189">The prerendered `Counter` component is embedded in the page.</span></span>

<span data-ttu-id="aeeaa-190">可能需要额外的工作，具体取决于组件使用的静态资源以及布局页面在应用中的组织方式。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-190">Additional work might be required depending on the static resources that components use and how layout pages are organized in an app.</span></span> <span data-ttu-id="aeeaa-191">通常，脚本添加到了页面或视图的 `Scripts` 呈现部分，样式表添加到了布局的 `<head>` 元素内容。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-191">Typically, scripts are added to a page or view's `Scripts` render section and stylesheets are added to the layout's `<head>` element content.</span></span>

> [!NOTE]
> <span data-ttu-id="aeeaa-192">如果 Blazor WebAssembly 应用已预呈现，并且使用 CSS 选择器同时集成到 Razor Pages 或 MVC 应用中，则前面的示例将引发 <xref:Microsoft.JSInterop.JSException>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-192">The preceding example throws a <xref:Microsoft.JSInterop.JSException> if a Blazor WebAssembly app is prerendered and integrated into a Razor Pages or MVC app **simultaneously** with a CSS selector.</span></span> <span data-ttu-id="aeeaa-193">导航到 `Client` 项目的 Razor 组件之一会引发以下异常：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-193">Navigating to one of the **`Client`** project's Razor components throws the following exception:</span></span>
>
> > <span data-ttu-id="aeeaa-194">Microsoft.JSInterop.JSException：找不到与选择器“#counter-component”匹配的任何元素。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-194">Microsoft.JSInterop.JSException: Could not find any element matching selector '#counter-component'.</span></span>
>
> <span data-ttu-id="aeeaa-195">这是正常行为，因为预呈现和集成具有可路由 Razor 组件的 Blazor WebAssembly 应用与使用 CSS 选择器不兼容。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-195">This is normal behavior because prerendering and integrating a Blazor WebAssembly app with routable Razor components is incompatible with the use of CSS selectors.</span></span>

## <a name="additional-blazor-webassembly-resources"></a><span data-ttu-id="aeeaa-196">其他 Blazor WebAssembly 资源</span><span class="sxs-lookup"><span data-stu-id="aeeaa-196">Additional Blazor WebAssembly resources</span></span>

* [<span data-ttu-id="aeeaa-197">状态管理：处理预呈现</span><span class="sxs-lookup"><span data-stu-id="aeeaa-197">State management: Handle prerendering</span></span>](xref:blazor/state-management?pivot=webassembly#handle-prerendering)
* [<span data-ttu-id="aeeaa-198">对程序集延迟加载的预呈现支持</span><span class="sxs-lookup"><span data-stu-id="aeeaa-198">Prerendering support with assembly lazy loading</span></span>](xref:blazor/webassembly-lazy-load-assemblies#assembly-load-logic-in-onnavigateasync)
* <span data-ttu-id="aeeaa-199">与预呈现相关的 Razor 组件生命周期主题</span><span class="sxs-lookup"><span data-stu-id="aeeaa-199">Razor component lifecycle subjects that pertain to prerendering</span></span>
  * [<span data-ttu-id="aeeaa-200">组件初始化 (`OnInitialized{Async}`)</span><span class="sxs-lookup"><span data-stu-id="aeeaa-200">Component initialization (`OnInitialized{Async}`)</span></span>](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [<span data-ttu-id="aeeaa-201">组件呈现后 (`OnAfterRender{Async}`)</span><span class="sxs-lookup"><span data-stu-id="aeeaa-201">After component render (`OnAfterRender{Async}`)</span></span>](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * <span data-ttu-id="aeeaa-202">[预呈现后的有状态重新连接](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)：尽管本部分中的内容重点介绍 Blazor Server 和有状态 SignalR 重新连接，但在托管 Blazor WebAssembly 应用 (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) 中预呈现的方案涉及相似的条件和防止执行两次开发人员代码的方法。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-202">[Stateful reconnection after prerendering](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering): Although the content in the section focuses on Blazor Server and stateful SignalR *reconnection*, the scenario for prerendering in hosted Blazor WebAssembly apps (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) involves similar conditions and approaches to prevent executing developer code twice.</span></span> <span data-ttu-id="aeeaa-203">新状态保留功能是针对 ASP.NET Core 6.0 版本计划的，该功能将改进在预呈现期间对初始化代码执行的管理。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-203">A *new state preservation feature* is planned for the ASP.NET Core 6.0 release that will improve the management of initialization code execution during prerendering.</span></span>
  * [<span data-ttu-id="aeeaa-204">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="aeeaa-204">Detect when the app is prerendering</span></span>](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* <span data-ttu-id="aeeaa-205">与预呈现相关的身份验证和授权主题</span><span class="sxs-lookup"><span data-stu-id="aeeaa-205">Authentication and authorization subjects that pertain to prerendering</span></span>
  * [<span data-ttu-id="aeeaa-206">一般方面</span><span class="sxs-lookup"><span data-stu-id="aeeaa-206">General aspects</span></span>](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
  * [<span data-ttu-id="aeeaa-207">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="aeeaa-207">Support prerendering with authentication</span></span>](xref:blazor/security/webassembly/additional-scenarios#support-prerendering-with-authentication)
* [<span data-ttu-id="aeeaa-208">托管和部署：Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="aeeaa-208">Host and deploy: Blazor WebAssembly</span></span>](xref:blazor/host-and-deploy/webassembly)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="aeeaa-209">.NET 5 或更高版本中的 ASP.NET Core 支持通过托管的 Blazor WebAssembly 解决方案将 Razor 组件集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-209">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="aeeaa-210">请选择本文的 .NET 5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-210">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="aeeaa-211">Razor 组件可以通过 Blazor Server 应用集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-211">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="aeeaa-212">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-212">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="aeeaa-213">[配置项目](#configuration)后，根据项目的要求按照下列部分中的指南操作：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-213">After [configuring the project](#configuration), use the guidance in the following sections depending on the project's requirements:</span></span>

* <span data-ttu-id="aeeaa-214">可路由组件：针对可直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-214">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="aeeaa-215">如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-215">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="aeeaa-216">在 Razor Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-216">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="aeeaa-217">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-217">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="aeeaa-218">[从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-218">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="aeeaa-219">如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-219">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="aeeaa-220">Configuration</span><span class="sxs-lookup"><span data-stu-id="aeeaa-220">Configuration</span></span>

<span data-ttu-id="aeeaa-221">现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-221">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="aeeaa-222">在项目的布局文件中：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-222">In the project's layout file:</span></span>

   * <span data-ttu-id="aeeaa-223">将以下 `<base>` 标记添加到 `Pages/Shared/_Layout.cshtml` (Razor Pages) 或 `Views/Shared/_Layout.cshtml` (MVC) 中的 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-223">Add the following `<base>` tag to the `<head>` element in `Pages/Shared/_Layout.cshtml` (Razor Pages) or `Views/Shared/_Layout.cshtml` (MVC):</span></span>

     ```diff
     + <base href="~/" />
     ```

     <span data-ttu-id="aeeaa-224">前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-224">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="aeeaa-225">如果应用是子应用程序，请按照 <xref:blazor/host-and-deploy/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-225">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

   * <span data-ttu-id="aeeaa-226">在紧接着 `Scripts` 呈现部分的前方为 `blazor.server.js` 脚本添加 `<script>` 标记。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-226">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section.</span></span>

     <span data-ttu-id="aeeaa-227">`Pages/Shared/_Layout.cshtml` (Razor Pages) 或 `Views/Shared/_Layout.cshtml` (MVC)：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-227">`Pages/Shared/_Layout.cshtml` (Razor Pages) or `Views/Shared/_Layout.cshtml` (MVC):</span></span>

     ```diff
         ...
     +   <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="aeeaa-228">框架会将 `blazor.server.js` 脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-228">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="aeeaa-229">无需手动将 `blazor.server.js` 脚本文件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-229">There's no need to manually add a `blazor.server.js` script file to the app.</span></span>

1. <span data-ttu-id="aeeaa-230">将具有以下内容的导入文件添加到项目的根文件夹。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-230">Add an imports file to the root folder of the project with the following content.</span></span> <span data-ttu-id="aeeaa-231">将 `{APP NAMESPACE}` 占位符更改为项目的命名空间。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-231">Change the `{APP NAMESPACE}` placeholder to the namespace of the project.</span></span>

   <span data-ttu-id="aeeaa-232">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-232">`_Imports.razor`:</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using {APP NAMESPACE}
   ```

1. <span data-ttu-id="aeeaa-233">在 `Startup.ConfigureServices` 中注册 Blazor Server 服务。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-233">Register the Blazor Server service in `Startup.ConfigureServices`.</span></span>

   <span data-ttu-id="aeeaa-234">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-234">`Startup.cs`:</span></span>

   ```diff
   + services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="aeeaa-235">将 Blazor 中心终结点添加到 `Startup.Configure` 的终结点 (`app.UseEndpoints`)。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-235">Add the Blazor Hub endpoint to the endpoints (`app.UseEndpoints`) of `Startup.Configure`.</span></span>

   <span data-ttu-id="aeeaa-236">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-236">`Startup.cs`:</span></span>

   ```diff
   + endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="aeeaa-237">将组件集成到任何页面或视图。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-237">Integrate components into any page or view.</span></span> <span data-ttu-id="aeeaa-238">例如，将 `Counter` 组件添加到项目的 `Shared` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-238">For example, add a `Counter` component to the project's `Shared` folder.</span></span>

   <span data-ttu-id="aeeaa-239">`Pages/Shared/Counter.razor` (Razor Pages) 或 `Views/Shared/Counter.razor` (MVC)：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-239">`Pages/Shared/Counter.razor` (Razor Pages) or `Views/Shared/Counter.razor` (MVC):</span></span>

   ```razor
   <h1>Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

   @code {
       private int currentCount = 0;

       private void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

   <span data-ttu-id="aeeaa-240">Razor Pages：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-240">**Razor Pages**:</span></span>

   <span data-ttu-id="aeeaa-241">在 Razor Pages 应用的项目 `Index` 页中，添加 `Counter` 组件的命名空间，并将组件嵌入到页面中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-241">In the project's `Index` page of a Razor Pages app, add the `Counter` component's namespace and embed the component into the page.</span></span> <span data-ttu-id="aeeaa-242">加载 `Index` 页面时，将在页面中预呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-242">When the `Index` page loads, the `Counter` component is prerendered in the page.</span></span> <span data-ttu-id="aeeaa-243">在下面的示例中，将 `{APP NAMESPACE}` 占位符替换为项目的命名空间。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-243">In the following example, replace the `{APP NAMESPACE}` placeholder with the project's namespace.</span></span>

   <span data-ttu-id="aeeaa-244">`Pages/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-244">`Pages/Index.cshtml`:</span></span>

   ```cshtml
   @page
   @using {APP NAMESPACE}.Pages.Shared
   @model IndexModel
   @{
       ViewData["Title"] = "Home page";
   }

   <div>
       <component type="typeof(Counter)" render-mode="ServerPrerendered" />
   </div>
   ```

   <span data-ttu-id="aeeaa-245">在上面的示例中，将 `{APP NAMESPACE}` 占位符替换为应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-245">In the preceding example, replace the `{APP NAMESPACE}` placeholder with the app's namespace.</span></span>

   <span data-ttu-id="aeeaa-246">MVC：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-246">**MVC**:</span></span>

   <span data-ttu-id="aeeaa-247">在 MVC 应用的项目 `Index` 视图中，添加 `Counter` 组件的命名空间，并将组件嵌入到视图中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-247">In the project's `Index` view of an MVC app, add the `Counter` component's namespace and embed the component into the view.</span></span> <span data-ttu-id="aeeaa-248">加载 `Index` 视图时，将在页面中预呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-248">When the `Index` view loads, the `Counter` component is prerendered in the page.</span></span> <span data-ttu-id="aeeaa-249">在下面的示例中，将 `{APP NAMESPACE}` 占位符替换为项目的命名空间。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-249">In the following example, replace the `{APP NAMESPACE}` placeholder with the project's namespace.</span></span>

   <span data-ttu-id="aeeaa-250">`Views/Home/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-250">`Views/Home/Index.cshtml`:</span></span>

   ```cshtml
   @using {APP NAMESPACE}.Views.Shared
   @{
       ViewData["Title"] = "Home Page";
   }

   <div>
       <component type="typeof(Counter)" render-mode="ServerPrerendered" />
   </div>
   ```

<span data-ttu-id="aeeaa-251">有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-251">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="aeeaa-252">在 Razor Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-252">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="aeeaa-253">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-253">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="aeeaa-254">在 Razor Pages 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-254">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="aeeaa-255">按照[配置](#configuration)部分中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-255">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="aeeaa-256">将具有以下内容的 `App` 组件添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-256">Add an `App` component to the project root with the following content.</span></span>

   <span data-ttu-id="aeeaa-257">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-257">`App.razor`:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="aeeaa-258">将具有以下内容的 `_Host` 页面添加到项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-258">Add a `_Host` page to the project with the following content.</span></span>

   <span data-ttu-id="aeeaa-259">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-259">`Pages/_Host.cshtml`:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="aeeaa-260">组件将共享 `_Layout.cshtml` 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-260">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="aeeaa-261"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-261"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="aeeaa-262">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-262">Is prerendered into the page.</span></span>
   * <span data-ttu-id="aeeaa-263">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-263">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="aeeaa-264">有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-264">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="aeeaa-265">在 `Startup.cs` 的 `Startup.Configure` 终结点中，将 `_Host` 页面的低优先级路由添加为最后一个终结点：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-265">In the `Startup.Configure` endpoints of `Startup.cs`, add a low-priority route for the `_Host` page as the last endpoint:</span></span>

   ```diff
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapRazorPages();
       endpoints.MapBlazorHub();
   +   endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="aeeaa-266">将可路由组件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-266">Add routable components to the project.</span></span>

   <span data-ttu-id="aeeaa-267">`Pages/RoutableCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-267">`Pages/RoutableCounter.razor`:</span></span>

   ```razor
   @page "/routable-counter"

   <h1>Routable Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

   @code {
       private int currentCount = 0;

       private void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

1. <span data-ttu-id="aeeaa-268">运行项目并导航到 `/routable-counter` 中的可路由 `RoutableCounter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-268">Run the project and navigate to the routable `RoutableCounter` component at `/routable-counter`.</span></span>

<span data-ttu-id="aeeaa-269">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-269">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="aeeaa-270">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-270">Use routable components in an MVC app</span></span>

<span data-ttu-id="aeeaa-271">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-271">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="aeeaa-272">在 MVC 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-272">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="aeeaa-273">按照[配置](#configuration)部分中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-273">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="aeeaa-274">将具有以下内容的 `App` 组件添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-274">Add an `App` component to the project root with the following content.</span></span>

   <span data-ttu-id="aeeaa-275">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-275">`App.razor`:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="aeeaa-276">将具有以下内容的 `_Host` 视图添加到项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-276">Add a `_Host` view to the project with the following content.</span></span>

   <span data-ttu-id="aeeaa-277">`Views/Home/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-277">`Views/Home/_Host.cshtml`:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="aeeaa-278">组件将共享 `_Layout.cshtml` 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-278">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="aeeaa-279"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-279"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="aeeaa-280">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-280">Is prerendered into the page.</span></span>
   * <span data-ttu-id="aeeaa-281">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-281">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="aeeaa-282">有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-282">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="aeeaa-283">向主控制器添加操作。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-283">Add an action to the Home controller.</span></span>

   <span data-ttu-id="aeeaa-284">`Controllers/HomeController.cs`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-284">`Controllers/HomeController.cs`:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="aeeaa-285">在 `Startup.cs` 的 `Startup.Configure` 终结点中，添加返回 `_Host` 视图的控制器操作的低优先级路由：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-285">In the `Startup.Configure` endpoints of `Startup.cs`, add a low-priority route for the controller action that returns the `_Host` view:</span></span>

   ```diff
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapControllerRoute(
           name: "default",
           pattern: "{controller=Home}/{action=Index}/{id?}");
       endpoints.MapBlazorHub();
   +   endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="aeeaa-286">将可路由组件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-286">Add routable components to the project.</span></span>

   <span data-ttu-id="aeeaa-287">`Pages/RoutableCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="aeeaa-287">`Pages/RoutableCounter.razor`:</span></span>

   ```razor
   @page "/routable-counter"

   <h1>Routable Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

   @code {
       private int currentCount = 0;

       private void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

1. <span data-ttu-id="aeeaa-288">运行项目并导航到 `/routable-counter` 中的可路由 `RoutableCounter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-288">Run the project and navigate to the routable `RoutableCounter` component at `/routable-counter`.</span></span>

<span data-ttu-id="aeeaa-289">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-289">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="aeeaa-290">从页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-290">Render components from a page or view</span></span>

<span data-ttu-id="aeeaa-291">本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-291">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="aeeaa-292">若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-292">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="aeeaa-293">呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-293">Render stateful interactive components</span></span>

<span data-ttu-id="aeeaa-294">可以将有状态的交互式组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-294">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="aeeaa-295">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-295">When the page or view renders:</span></span>

* <span data-ttu-id="aeeaa-296">该组件通过页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-296">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="aeeaa-297">用于预呈现的初始组件状态丢失。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-297">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="aeeaa-298">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-298">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="aeeaa-299">以下 Razor 页面将呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-299">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="aeeaa-300">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-300">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="aeeaa-301">呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="aeeaa-301">Render noninteractive components</span></span>

<span data-ttu-id="aeeaa-302">在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-302">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="aeeaa-303">由于该组件是以静态方式呈现的，因此它不是交互式组件：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-303">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="aeeaa-304">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-304">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="aeeaa-305">组件命名空间</span><span class="sxs-lookup"><span data-stu-id="aeeaa-305">Component namespaces</span></span>

<span data-ttu-id="aeeaa-306">使用自定义文件夹保存项目的 Razor 组件时，将表示文件夹的命名空间添加到页面/视图或 `_ViewImports.cshtml` 文件。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-306">When using a custom folder to hold the project's Razor components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="aeeaa-307">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="aeeaa-307">In the following example:</span></span>

* <span data-ttu-id="aeeaa-308">组件存储在项目的 `Components` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-308">Components are stored in the `Components` folder of the project.</span></span>
* <span data-ttu-id="aeeaa-309">`{APP NAMESPACE}` 占位符是项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-309">The `{APP NAMESPACE}` placeholder is the project's namespace.</span></span> <span data-ttu-id="aeeaa-310">`Components` 表示文件夹的名称。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-310">`Components` represents the name of the folder.</span></span>

```cshtml
@using {APP NAMESPACE}.Components
```

<span data-ttu-id="aeeaa-311">`_ViewImports.cshtml` 文件位于 Razor Pages 应用的 `Pages` 文件夹中，或是 MVC 应用的 `Views` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-311">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="aeeaa-312">有关详细信息，请参阅 <xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="aeeaa-312">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

## <a name="additional-blazor-server-resources"></a><span data-ttu-id="aeeaa-313">其他 Blazor Server 资源</span><span class="sxs-lookup"><span data-stu-id="aeeaa-313">Additional Blazor Server resources</span></span>

* [<span data-ttu-id="aeeaa-314">状态管理：处理预呈现</span><span class="sxs-lookup"><span data-stu-id="aeeaa-314">State management: Handle prerendering</span></span>](xref:blazor/state-management?pivot=server#handle-prerendering)
* <span data-ttu-id="aeeaa-315">与预呈现相关的 Razor 组件生命周期主题</span><span class="sxs-lookup"><span data-stu-id="aeeaa-315">Razor component lifecycle subjects that pertain to prerendering</span></span>
  * [<span data-ttu-id="aeeaa-316">组件初始化 (`OnInitialized{Async}`)</span><span class="sxs-lookup"><span data-stu-id="aeeaa-316">Component initialization (`OnInitialized{Async}`)</span></span>](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [<span data-ttu-id="aeeaa-317">组件呈现后 (`OnAfterRender{Async}`)</span><span class="sxs-lookup"><span data-stu-id="aeeaa-317">After component render (`OnAfterRender{Async}`)</span></span>](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * [<span data-ttu-id="aeeaa-318">预呈现后的有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="aeeaa-318">Stateful reconnection after prerendering</span></span>](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)
  * [<span data-ttu-id="aeeaa-319">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="aeeaa-319">Detect when the app is prerendering</span></span>](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* [<span data-ttu-id="aeeaa-320">身份验证和授权：一般方面</span><span class="sxs-lookup"><span data-stu-id="aeeaa-320">Authentication and authorization: General aspects</span></span>](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
* [<span data-ttu-id="aeeaa-321">Blazor Server 重新呈现</span><span class="sxs-lookup"><span data-stu-id="aeeaa-321">Blazor Server rerendering</span></span>](xref:blazor/fundamentals/handle-errors?pivot=server#blazor-server-prerendering-server)
* [<span data-ttu-id="aeeaa-322">托管和部署：Blazor Server</span><span class="sxs-lookup"><span data-stu-id="aeeaa-322">Host and deploy: Blazor Server</span></span>](xref:blazor/host-and-deploy/server)
* [<span data-ttu-id="aeeaa-323">威胁缓解：跨站点脚本 (XSS)</span><span class="sxs-lookup"><span data-stu-id="aeeaa-323">Threat mitigation: Cross-site scripting (XSS)</span></span>](xref:blazor/security/server/threat-mitigation#cross-site-scripting-xss)

::: zone-end
