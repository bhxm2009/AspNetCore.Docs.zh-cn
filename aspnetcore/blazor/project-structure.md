---
title: ASP.NET Core Blazor 项目结构
author: guardrex
description: 了解 ASP.NET Core Blazor 应用项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: fe42c2d43b79ea959bb0ba8e5b96e6c865b2a416
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394871"
---
# <a name="aspnet-core-blazor-project-structure"></a><span data-ttu-id="a2184-103">ASP.NET Core Blazor 项目结构</span><span class="sxs-lookup"><span data-stu-id="a2184-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="a2184-104">本文介绍了根据 Blazor 框架项目模板之一生成的 Blazor 应用中包含哪些文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="a2184-104">This article describes the files and folders that make up a Blazor app generated from one of the Blazor framework's project templates.</span></span> <span data-ttu-id="a2184-105">有关如何使用工具以 Blazor 项目模板创建 Blazor 应用的信息，请参阅“<xref:blazor/tooling>”。</span><span class="sxs-lookup"><span data-stu-id="a2184-105">For information on how to use tooling to create a Blazor app from a Blazor project template, see <xref:blazor/tooling>.</span></span> <span data-ttu-id="a2184-106">有关 Blazor 的托管模型、Blazor WebAssembly 和 Blazor Server 的信息，请参阅“<xref:blazor/hosting-models>”。</span><span class="sxs-lookup"><span data-stu-id="a2184-106">For information on Blazor's hosting models, Blazor WebAssembly and Blazor Server, see <xref:blazor/hosting-models>.</span></span>

## Blazor WebAssembly

<span data-ttu-id="a2184-107">Blazor WebAssembly项目模板：`blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="a2184-107">Blazor WebAssembly project template: `blazorwasm`</span></span>

<span data-ttu-id="a2184-108">Blazor WebAssembly 模板创建 Blazor WebAssembly 应用的初始文件和目录结构。</span><span class="sxs-lookup"><span data-stu-id="a2184-108">The Blazor WebAssembly template creates the initial files and directory structure for a Blazor WebAssembly app.</span></span> <span data-ttu-id="a2184-109">该应用中填充了一个 `FetchData` 组件的演示代码，该组件从静态资产 `weather.json` 加载数据，且用户与 `Counter` 组件交互。</span><span class="sxs-lookup"><span data-stu-id="a2184-109">The app is populated with demonstration code for a `FetchData` component that loads data from a static asset, `weather.json`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="a2184-110">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`)。</span><span class="sxs-lookup"><span data-stu-id="a2184-110">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app.</span></span> <span data-ttu-id="a2184-111">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="a2184-111">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a2184-112">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="a2184-112">The template includes the following components:</span></span>
  * <span data-ttu-id="a2184-113">`Counter` 组件 (`Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-113">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="a2184-114">`FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-114">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a2184-115">`Index` 组件 (`Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="a2184-115">`Index` component (`Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="a2184-116">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="a2184-116">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a2184-117">`Shared` 文件夹：包含以下共享组件和样式表：</span><span class="sxs-lookup"><span data-stu-id="a2184-117">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="a2184-118">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="a2184-118">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="a2184-119">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="a2184-119">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="a2184-120">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="a2184-120">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="a2184-121">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="a2184-121">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a2184-122"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-122">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="a2184-123">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="a2184-123">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="a2184-124">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-124">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a2184-125">`Shared` 文件夹：包含以下共享组件：</span><span class="sxs-lookup"><span data-stu-id="a2184-125">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="a2184-126">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="a2184-126">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="a2184-127">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="a2184-127">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="a2184-128">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="a2184-128">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a2184-129"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-129">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="a2184-130">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-130">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a2184-131">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。</span><span class="sxs-lookup"><span data-stu-id="a2184-131">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="a2184-132">`index.html` 网页是实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="a2184-132">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a2184-133">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="a2184-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a2184-134">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="a2184-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a2184-135">使用 `app` 的 `id` (`<div id="app">Loading...</div>`) 在 `div` DOM 元素的位置呈现组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-135">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a2184-136">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产，包括 `appsettings.json` 和[配置设置](xref:blazor/fundamentals/configuration)的环境应用设置文件。</span><span class="sxs-lookup"><span data-stu-id="a2184-136">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets, including `appsettings.json` and environmental app settings files for [configuration settings](xref:blazor/fundamentals/configuration).</span></span> <span data-ttu-id="a2184-137">`index.html` 网页是实现为 HTML 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="a2184-137">The `index.html` webpage is the root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a2184-138">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="a2184-138">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a2184-139">此页面指定根 `App` 组件的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="a2184-139">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a2184-140">组件呈现在 `app` DOM 元素 (`<app>Loading...</app>`) 的位置。</span><span class="sxs-lookup"><span data-stu-id="a2184-140">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a2184-141">添加到 `wwwroot/index.html` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。</span><span class="sxs-lookup"><span data-stu-id="a2184-141">JavaScript (JS) files added to the `wwwroot/index.html` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="a2184-142">在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="a2184-142">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="a2184-143">例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="a2184-143">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="a2184-144">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="a2184-144">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a2184-145">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="a2184-145">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a2184-146"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-146">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a2184-147">`Program.cs`：应用入口点，用于设置 WebAssembly 主机：</span><span class="sxs-lookup"><span data-stu-id="a2184-147">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>
  
  * <span data-ttu-id="a2184-148">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-148">The `App` component is the root component of the app.</span></span> <span data-ttu-id="a2184-149">对于根组件集合 (`builder.RootComponents.Add<App>("#app")`)，使用 `app` 的 `id`（`wwwroot/index.html` 中的 `<div id="app">Loading...</div>`）将 `App` 组件指定为 `div` DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="a2184-149">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
  * <span data-ttu-id="a2184-150">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="a2184-150">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a2184-151">`Program.cs`：应用入口点，用于设置 WebAssembly 主机：</span><span class="sxs-lookup"><span data-stu-id="a2184-151">`Program.cs`: The app's entry point that sets up the WebAssembly host:</span></span>

  * <span data-ttu-id="a2184-152">`App` 组件是应用的根组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-152">The `App` component is the root component of the app.</span></span> <span data-ttu-id="a2184-153">`App` 组件指定为根组件集合 (`builder.RootComponents.Add<App>("app")`) 的 `app` DOM 元素（`wwwroot/index.html` 中的 `<app>Loading...</app>`）。</span><span class="sxs-lookup"><span data-stu-id="a2184-153">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
  * <span data-ttu-id="a2184-154">添加并配置了[服务](xref:blazor/fundamentals/dependency-injection)（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>()`）。</span><span class="sxs-lookup"><span data-stu-id="a2184-154">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="a2184-155">Blazor Server项目模板：`blazorserver`</span><span class="sxs-lookup"><span data-stu-id="a2184-155">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="a2184-156">Blazor Server 模板创建 Blazor Server 应用的初始文件和目录结构。</span><span class="sxs-lookup"><span data-stu-id="a2184-156">The Blazor Server template creates the initial files and directory structure for a Blazor Server app.</span></span> <span data-ttu-id="a2184-157">该应用中填充了一个 `FetchData` 组件的演示代码，该组件从注册服务 `WeatherForecastService` 加载数据，且用户与 `Counter` 组件交互。</span><span class="sxs-lookup"><span data-stu-id="a2184-157">The app is populated with demonstration code for a `FetchData` component that loads data from a registered service, `WeatherForecastService`, and user interaction with a `Counter` component.</span></span>

* <span data-ttu-id="a2184-158">`Data` 文件夹：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。</span><span class="sxs-lookup"><span data-stu-id="a2184-158">`Data` folder: Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provides example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="a2184-159">`Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-159">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="a2184-160">每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。</span><span class="sxs-lookup"><span data-stu-id="a2184-160">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a2184-161">该模板包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="a2184-161">The template includes the following:</span></span>
  * <span data-ttu-id="a2184-162">`_Host.cshtml`：实现为 Razor 页面的应用的根页面：</span><span class="sxs-lookup"><span data-stu-id="a2184-162">`_Host.cshtml`: The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="a2184-163">最初请求应用的任何页面时，都会呈现此页面并在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="a2184-163">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="a2184-164">此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。</span><span class="sxs-lookup"><span data-stu-id="a2184-164">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="a2184-165">`Counter` 组件 (`Counter.razor`)：实现“计数器”页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-165">`Counter` component (`Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="a2184-166">`Error` 组件 (`Error.razor`)：当应用中发生未经处理的异常时呈现。</span><span class="sxs-lookup"><span data-stu-id="a2184-166">`Error` component (`Error.razor`): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="a2184-167">`FetchData` 组件 (`FetchData.razor`)：实现“提取数据”页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-167">`FetchData` component (`FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a2184-168">`Index` 组件 (`Index.razor`)：实现主页。</span><span class="sxs-lookup"><span data-stu-id="a2184-168">`Index` component (`Index.razor`): Implements the Home page.</span></span>

> [!NOTE]
> <span data-ttu-id="a2184-169">添加到 `Pages/_Host.cshtml` 文件的 JavaScript (JS) 文件应显示在关闭 `</body>` 标记之前。</span><span class="sxs-lookup"><span data-stu-id="a2184-169">JavaScript (JS) files added to the `Pages/_Host.cshtml` file should appear before the closing `</body>` tag.</span></span> <span data-ttu-id="a2184-170">在某些情况下，从 JS 文件加载自定义 JS 代码的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="a2184-170">The order that custom JS code is loaded from JS files is important in some scenarios.</span></span> <span data-ttu-id="a2184-171">例如，确保在 Blazor framework JS 文件之前包含带有互操作方法的 JS 文件。</span><span class="sxs-lookup"><span data-stu-id="a2184-171">For example, ensure that JS files with interop methods are included before Blazor framework JS files.</span></span>

* <span data-ttu-id="a2184-172">`Properties/launchSettings.json`：保留[开发环境配置](xref:fundamentals/environments#development-and-launchsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="a2184-172">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a2184-173">`Shared` 文件夹：包含以下共享组件和样式表：</span><span class="sxs-lookup"><span data-stu-id="a2184-173">`Shared` folder: Contains the following shared components and stylesheets:</span></span>
  * <span data-ttu-id="a2184-174">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="a2184-174">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="a2184-175">`MainLayout.razor.css`：应用主布局的样式表。</span><span class="sxs-lookup"><span data-stu-id="a2184-175">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="a2184-176">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="a2184-176">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="a2184-177">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="a2184-177">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a2184-178"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-178">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="a2184-179">`NavMenu.razor.css`：应用导航菜单的样式表。</span><span class="sxs-lookup"><span data-stu-id="a2184-179">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>
  * <span data-ttu-id="a2184-180">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-180">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a2184-181">`Shared` 文件夹：包含以下共享组件：</span><span class="sxs-lookup"><span data-stu-id="a2184-181">`Shared` folder: Contains the following shared components:</span></span>
  * <span data-ttu-id="a2184-182">`MainLayout` 组件 (`MainLayout.razor`)：应用的[布局组件](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="a2184-182">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="a2184-183">`NavMenu` 组件 (`NavMenu.razor`)：实现边栏导航。</span><span class="sxs-lookup"><span data-stu-id="a2184-183">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="a2184-184">包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。</span><span class="sxs-lookup"><span data-stu-id="a2184-184">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a2184-185"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-185">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="a2184-186">`SurveyPrompt` 组件 (`SurveyPrompt.razor`)：Blazor 调查组件。</span><span class="sxs-lookup"><span data-stu-id="a2184-186">`SurveyPrompt` component (`SurveyPrompt.razor`): Blazor survey component.</span></span>
  
::: moniker-end

* <span data-ttu-id="a2184-187">`wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。</span><span class="sxs-lookup"><span data-stu-id="a2184-187">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="a2184-188">`_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="a2184-188">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a2184-189">`App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。</span><span class="sxs-lookup"><span data-stu-id="a2184-189">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a2184-190"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。</span><span class="sxs-lookup"><span data-stu-id="a2184-190">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="a2184-191">`appsettings.json` 和环境应用设置文件：提供应用的[配置设置](xref:blazor/fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="a2184-191">`appsettings.json` and environmental app settings files: Provide [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span>

* <span data-ttu-id="a2184-192">`Program.cs`：应用的入口点，用于设置 ASP.NET Core [主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="a2184-192">`Program.cs`: The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span>

* <span data-ttu-id="a2184-193">`Startup.cs`：包含应用的启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="a2184-193">`Startup.cs`: Contains the app's startup logic.</span></span> <span data-ttu-id="a2184-194">`Startup` 类定义两个方法：</span><span class="sxs-lookup"><span data-stu-id="a2184-194">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="a2184-195">`ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。</span><span class="sxs-lookup"><span data-stu-id="a2184-195">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="a2184-196">通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。</span><span class="sxs-lookup"><span data-stu-id="a2184-196">Services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="a2184-197">`Configure`：配置应用的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="a2184-197">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="a2184-198">调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。</span><span class="sxs-lookup"><span data-stu-id="a2184-198"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="a2184-199">使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="a2184-199">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="a2184-200">调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。</span><span class="sxs-lookup"><span data-stu-id="a2184-200">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2184-201">其他资源</span><span class="sxs-lookup"><span data-stu-id="a2184-201">Additional resources</span></span>

* <xref:blazor/tooling>
* <xref:blazor/hosting-models>
