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
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a>预呈现和集成 ASP.NET Core Razor 组件

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor 组件可以通过托管的 Blazor WebAssembly 解决方案集成到 Razor Pages 和 MVC 应用。 呈现页面或视图时，可以同时预呈现组件。

## <a name="solution-configuration"></a>解决方案配置

### <a name="prerendering-configuration"></a>预呈现配置

若要设置托管 Blazor WebAssembly 应用的预呈现，请执行以下操作：

1. 将 Blazor WebAssembly 应用托管在 ASP.NET Core 应用中。 可以将独立的 Blazor WebAssembly 应用添加到 ASP.NET Core 解决方案中，也可以使用通过托管选项从 [Blazor WebAssembly 项目模板](xref:blazor/tooling)创建的托管 Blazor WebAssembly 应用：

   * Visual Studio：创建 Blazor WebAssembly 应用时，选中“高级” > “ASP.NET Core 托管”复选框。 在本文的示例中，该解决方案的名称为 `BlazorHosted`。
   * Visual Studio Code/.NET CLI 命令行界面：`dotnet new blazorwasm -ho`（使用 `-ho|--hosted` 选项）。 使用 `-o|--output {LOCATION}` 选项为解决方案创建文件夹，并设置解决方案的项目命名空间。 在本文的示例中，该解决方案的名称为 `BlazorHosted` (`dotnet new blazorwasm -ho -o BlazorHosted`)。

   对于本文中的示例，客户端项目的命名空间为 `BlazorHosted.Client`，服务器项目的命名空间为 `BlazorHosted.Server`。

1. 从 Blazor WebAssembly `Client` 项目删除 `wwwroot/index.html` 文件。

1. 在 `Client` 项目中，删除 `Program.Main` (`Program.cs`) 中的以下行：

   ```diff
   - builder.RootComponents.Add<App>("#app");
   ```

1. 将 `Pages/_Host.cshtml` 文件添加到 `Server` 项目的 `Pages` 文件夹。 可以使用命令行界面中的 `dotnet new blazorserver -o BlazorServer` 命令获得 Blazor Server 模板创建的项目的 `_Host.cshtml` 文件（`-o BlazorServer` 选项为项目创建文件夹）。 将 `Pages/_Host.cshtml` 文件放入托管 Blazor WebAssembly 解决方案的 `Server` 项目后，对此文件进行以下更改：

   * 为 `Client` 项目提供 [`@using`](xref:mvc/views/razor#using) 指令（例如 `@using BlazorHosted.Client`）。
   * 更新样式表链接，以指向 WebAssembly 项目的样式表。 在下面的示例中，客户端项目的命名空间为 `BlazorHosted.Client`：

     ```diff
     - <link href="css/site.css" rel="stylesheet" />
     - <link href="_content/BlazorServer/_framework/scoped.styles.css" rel="stylesheet" />
     + <link href="css/app.css" rel="stylesheet" />
     + <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

     > [!NOTE]
     > 就地保留请求启动样式 (`css/bootstrap/bootstrap.min.css`) 表的 `<link>` 元素。

   * 更新[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode`，以使用 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 预呈现根 `App` 组件：

     ```diff
     - <component type="typeof(App)" render-mode="ServerPrerendered" />
     + <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * 更新 Blazor 脚本源，以使用客户端 Blazor WebAssembly 脚本：

     ```diff
     - <script src="_framework/blazor.server.js"></script>
     + <script src="_framework/blazor.webassembly.js"></script>
     ```

1. 在 `Server` 项目的 `Startup.Configure` 中，将回退从 `index.html` 文件更改为 `_Host.cshtml` 页面。

   `Startup.cs`:

   ```diff
   - endpoints.MapFallbackToFile("index.html");
   + endpoints.MapFallbackToPage("/_Host");
   ```

1. 运行 `Server` 项目。 托管 Blazor WebAssembly 应用由客户端的 `Server` 项目预呈现。

### <a name="configuration-for-embedding-razor-components-into-pages-and-views"></a>用于将 Razor 组件嵌入到页面和视图中的配置

本文中将客户端 Razor 应用的 Blazor WebAssembly 组件嵌入到服务器应用的页面和视图中的以下部分和示例需要其他配置。

使用 `Server` 项目中的默认 Razor Pages 或 MVC 布局文件。 `Server` 项目必须具有以下文件和文件夹。

Razor Pages：

* `Pages/Shared/_Layout.cshtml`
* `Pages/_ViewImports.cshtml`
* `Pages/_ViewStart.cshtml`

MVC：

* `Views/Shared/_Layout.cshtml`
* `Views/_ViewImports.cshtml`
* `Views/_ViewStart.cshtml`

从 Razor Pages 或 MVC 项目模板创建的应用获取上述文件。 有关详细信息，请参阅<xref:tutorials/razor-pages/razor-pages-start>或<xref:tutorials/first-mvc-app/start-mvc>。

更新导入的 `_ViewImports.cshtml` 文件中的命名空间，使其与接收文件的 `Server` 项目所使用的命名空间相匹配。

更新导入的布局文件 (`_Layout.cshtml`) 以包含 `Client` 项目的样式。 在下面的示例中，`Client` 项目的命名空间为 `BlazorHosted.Client`。 可以同时更新的 `<title>` 元素。

`Pages/Shared/_Layout.cshtml` (Razor Pages) 或 `Views/Shared/_Layout.cshtml` (MVC)：

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

导入的布局包含 `Home` 和 `Privacy` 导航链接。 若要使 `Home` 链接指向托管 Blazor WebAssembly 应用，请更改超链接：

```diff
- <a class="nav-link text-dark" asp-area="" asp-page="/Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

在 MVC 布局文件中：

```diff
- <a class="nav-link text-dark" asp-area="" asp-controller="Home" 
-     asp-action="Index">Home</a>
+ <a class="nav-link text-dark" href="/">Home</a>
```

若要使 `Privacy` 链接指向隐私页，请将隐私页添加到 `Server` 项目。

`Server` 项目中的 `Pages/Privacy.cshtml`：

```cshtml
@page
@model BlazorHosted.Server.Pages.PrivacyModel
@{
}

<h1>Privacy Policy</h1>
```

如果首选基于 MVC 的隐私视图，请在 `Server` 项目中创建一个隐私视图。

`View/Home/Privacy.cshtml`:

```cshtml
@{
    ViewData["Title"] = "Privacy Policy";
}

<h1>@ViewData["Title"]</h1>
```

在 `Home` 控制器中，返回视图。

`Controllers/HomeController.cs`:

```diff
+ public IActionResult Privacy()
+ {
+     return View();
+ }
```

从赞助商项目的 `wwwroot` 文件夹中将静态资产导入到 **`Server`** 项目：

* `wwwroot/css` 文件夹和内容
* `wwwroot/js` 文件夹和内容
* `wwwroot/lib` 文件夹和内容

如果从 ASP.NET Core 项目模板创建了赞助商项目，但未修改文件，则可以将整个 `wwwroot` 文件夹从赞助商项目复制到 `Server` 项目中，并删除 `favicon.ico` 图标文件。

> [!NOTE]
> 如果 `Client` 和 `Server` 项目在其 `wwwroot` 文件夹中包含相同的静态资产（例如 `favicon.ico`），则会引发异常，因为每个文件夹中的静态资产共享同一个 Web 根路径：
>
> > 静态 Web 资源“...\favicon.ico”具有与项目文件“wwwroot\favicon.ico”冲突的 Web 根路径“/wwwroot/favicon.ico”。
>
> 因此，在任意一个 `wwwroot` 文件夹（而不是两者）中托管静态资产。

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>使用组件标记帮助程序，通过页面或视图呈现组件

[配置解决方案](#solution-configuration)（包括[其他配置](#configuration-for-embedding-razor-components-into-pages-and-views)）后，[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)支持两种呈现模式来通过页面或视图呈现 Blazor WebAssembly 应用中的组件：

* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssembly>
* <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>

在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。 页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件。 为客户端项目的 `Pages` 命名空间添加 [`@using`](xref:mvc/views/razor#using) 指令，以避免将 `Counter` 组件的整个命名空间用于[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) (`{APP ASSEMBLY}.Pages.Counter`)。 在下面的示例中，`Client` 项目的命名空间为 `BlazorHosted.Client`。

在 `Server` 项目中，`Pages/RazorPagesCounter1.cshtml`：

```cshtml
@page
@using BlazorHosted.Client.Pages

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

运行 `Server` 项目。 导航到 `/razorpagescounter1` 中的 Razor 页。 预呈现的 `Counter` 组件嵌入在页面中。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：

* 在页面中预呈现。
* 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

可能需要额外的工作，具体取决于组件使用的静态资源以及布局页面在应用中的组织方式。 通常，脚本添加到了页面或视图的 `Scripts` 呈现部分，样式表添加到了布局的 `<head>` 元素内容。

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>使用 CSS 选择器，通过页面或视图呈现组件

[配置解决方案](#solution-configuration)（包括[其他配置](#configuration-for-embedding-razor-components-into-pages-and-views)）后，将根组件添加到 `Program.Main` 中的托管 Blazor WebAssembly 解决方案的 `Client` 项目。 下面的示例使用 CSS 选择器将 `Counter` 组件声明为根组件，该选择器会选择 `id` 与 `counter-component` 匹配的元素。 在下面的示例中，`Client` 项目的命名空间为 `BlazorHosted.Client`。

在 `Client` 项目的 `Program.cs` 中，将项目的 Razor 组件的命名空间添加到文件顶部：

```diff
+ using BlazorHosted.Client.Pages;
```

在 `Program.Main` 中建立 `builder` 后，将 `Counter` 组件添加为根组件：

```diff
+ builder.RootComponents.Add<Counter>("#counter-component");
```

在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。 页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件。

在 `Server` 项目中，`Pages/RazorPagesCounter2.cshtml`：

```cshtml
@page

<div id="counter-component">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

运行 `Server` 项目。 导航到 `/razorpagescounter2` 中的 Razor 页。 预呈现的 `Counter` 组件嵌入在页面中。

可能需要额外的工作，具体取决于组件使用的静态资源以及布局页面在应用中的组织方式。 通常，脚本添加到了页面或视图的 `Scripts` 呈现部分，样式表添加到了布局的 `<head>` 元素内容。

> [!NOTE]
> 如果 Blazor WebAssembly 应用已预呈现，并且使用 CSS 选择器同时集成到 Razor Pages 或 MVC 应用中，则前面的示例将引发 <xref:Microsoft.JSInterop.JSException>。 导航到 `Client` 项目的 Razor 组件之一会引发以下异常：
>
> > Microsoft.JSInterop.JSException：找不到与选择器“#counter-component”匹配的任何元素。
>
> 这是正常行为，因为预呈现和集成具有可路由 Razor 组件的 Blazor WebAssembly 应用与使用 CSS 选择器不兼容。

## <a name="additional-blazor-webassembly-resources"></a>其他 Blazor WebAssembly 资源

* [状态管理：处理预呈现](xref:blazor/state-management?pivot=webassembly#handle-prerendering)
* [对程序集延迟加载的预呈现支持](xref:blazor/webassembly-lazy-load-assemblies#assembly-load-logic-in-onnavigateasync)
* 与预呈现相关的 Razor 组件生命周期主题
  * [组件初始化 (`OnInitialized{Async}`)](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [组件呈现后 (`OnAfterRender{Async}`)](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * [预呈现后的有状态重新连接](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)：尽管本部分中的内容重点介绍 Blazor Server 和有状态 SignalR 重新连接，但在托管 Blazor WebAssembly 应用 (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) 中预呈现的方案涉及相似的条件和防止执行两次开发人员代码的方法。 新状态保留功能是针对 ASP.NET Core 6.0 版本计划的，该功能将改进在预呈现期间对初始化代码执行的管理。
  * [检测应用何时预呈现](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* 与预呈现相关的身份验证和授权主题
  * [一般方面](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
  * [支持预呈现身份验证](xref:blazor/security/webassembly/additional-scenarios#support-prerendering-with-authentication)
* [托管和部署：Blazor WebAssembly](xref:blazor/host-and-deploy/webassembly)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

.NET 5 或更高版本中的 ASP.NET Core 支持通过托管的 Blazor WebAssembly 解决方案将 Razor 组件集成到 Razor Pages 和 MVC 应用。 请选择本文的 .NET 5 或更高版本。

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor 组件可以通过 Blazor Server 应用集成到 Razor Pages 和 MVC 应用。 呈现页面或视图时，可以同时预呈现组件。

[配置项目](#configuration)后，根据项目的要求按照下列部分中的指南操作：

* 可路由组件：针对可直接通过用户请求进行路由的组件。 如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。
  * [在 Razor Pages 应用中使用可路由组件](#use-routable-components-in-a-razor-pages-app)
  * [在 MVC 应用中使用可路由组件](#use-routable-components-in-an-mvc-app)
* [从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。 如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。

## <a name="configuration"></a>Configuration

现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：

1. 在项目的布局文件中：

   * 将以下 `<base>` 标记添加到 `Pages/Shared/_Layout.cshtml` (Razor Pages) 或 `Views/Shared/_Layout.cshtml` (MVC) 中的 `<head>` 元素：

     ```diff
     + <base href="~/" />
     ```

     前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。 如果应用是子应用程序，请按照 <xref:blazor/host-and-deploy/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。

   * 在紧接着 `Scripts` 呈现部分的前方为 `blazor.server.js` 脚本添加 `<script>` 标记。

     `Pages/Shared/_Layout.cshtml` (Razor Pages) 或 `Views/Shared/_Layout.cshtml` (MVC)：

     ```diff
         ...
     +   <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     框架会将 `blazor.server.js` 脚本添加到应用。 无需手动将 `blazor.server.js` 脚本文件添加到应用。

1. 将具有以下内容的导入文件添加到项目的根文件夹。 将 `{APP NAMESPACE}` 占位符更改为项目的命名空间。

   `_Imports.razor`:

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

1. 在 `Startup.ConfigureServices` 中注册 Blazor Server 服务。

   `Startup.cs`:

   ```diff
   + services.AddServerSideBlazor();
   ```

1. 将 Blazor 中心终结点添加到 `Startup.Configure` 的终结点 (`app.UseEndpoints`)。

   `Startup.cs`:

   ```diff
   + endpoints.MapBlazorHub();
   ```

1. 将组件集成到任何页面或视图。 例如，将 `Counter` 组件添加到项目的 `Shared` 文件夹。

   `Pages/Shared/Counter.razor` (Razor Pages) 或 `Views/Shared/Counter.razor` (MVC)：

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

   Razor Pages：

   在 Razor Pages 应用的项目 `Index` 页中，添加 `Counter` 组件的命名空间，并将组件嵌入到页面中。 加载 `Index` 页面时，将在页面中预呈现 `Counter` 组件。 在下面的示例中，将 `{APP NAMESPACE}` 占位符替换为项目的命名空间。

   `Pages/Index.cshtml`:

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

   在上面的示例中，将 `{APP NAMESPACE}` 占位符替换为应用的命名空间。

   MVC：

   在 MVC 应用的项目 `Index` 视图中，添加 `Counter` 组件的命名空间，并将组件嵌入到视图中。 加载 `Index` 视图时，将在页面中预呈现 `Counter` 组件。 在下面的示例中，将 `{APP NAMESPACE}` 占位符替换为项目的命名空间。

   `Views/Home/Index.cshtml`:

   ```cshtml
   @using {APP NAMESPACE}.Views.Shared
   @{
       ViewData["Title"] = "Home Page";
   }

   <div>
       <component type="typeof(Counter)" render-mode="ServerPrerendered" />
   </div>
   ```

有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。

## <a name="use-routable-components-in-a-razor-pages-app"></a>在 Razor Pages 应用中使用可路由组件

本部分介绍如何添加可直接从用户请求路由的组件。

在 Razor Pages 应用中支持可路由 Razor 组件：

1. 按照[配置](#configuration)部分中的指南操作。

1. 将具有以下内容的 `App` 组件添加到项目根。

   `App.razor`:

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

1. 将具有以下内容的 `_Host` 页面添加到项目。

   `Pages/_Host.cshtml`:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件将共享 `_Layout.cshtml` 文件用于布局。

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：

   * 在页面中预呈现。
   * 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

   有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

1. 在 `Startup.cs` 的 `Startup.Configure` 终结点中，将 `_Host` 页面的低优先级路由添加为最后一个终结点：

   ```diff
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapRazorPages();
       endpoints.MapBlazorHub();
   +   endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 将可路由组件添加到项目。

   `Pages/RoutableCounter.razor`:

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

1. 运行项目并导航到 `/routable-counter` 中的可路由 `RoutableCounter` 组件。

有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。

## <a name="use-routable-components-in-an-mvc-app"></a>在 MVC 应用中使用可路由组件

本部分介绍如何添加可直接从用户请求路由的组件。

在 MVC 应用中支持可路由 Razor 组件：

1. 按照[配置](#configuration)部分中的指南操作。

1. 将具有以下内容的 `App` 组件添加到项目根。

   `App.razor`:

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

1. 将具有以下内容的 `_Host` 视图添加到项目。

   `Views/Home/_Host.cshtml`:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   组件将共享 `_Layout.cshtml` 文件用于布局。

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：

   * 在页面中预呈现。
   * 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

   有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

1. 向主控制器添加操作。

   `Controllers/HomeController.cs`:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. 在 `Startup.cs` 的 `Startup.Configure` 终结点中，添加返回 `_Host` 视图的控制器操作的低优先级路由：

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

1. 将可路由组件添加到项目。

   `Pages/RoutableCounter.razor`:

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

1. 运行项目并导航到 `/routable-counter` 中的可路由 `RoutableCounter` 组件。

有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。

## <a name="render-components-from-a-page-or-view"></a>从页面或视图呈现组件

本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。

若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)。

### <a name="render-stateful-interactive-components"></a>呈现有状态交互式组件

可以将有状态的交互式组件添加到 Razor 页面或视图。

呈现页面或视图时：

* 该组件通过页面或视图预呈现。
* 用于预呈现的初始组件状态丢失。
* 建立 SignalR 连接时，将创建新的组件状态。

以下 Razor 页面将呈现 `Counter` 组件：

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

### <a name="render-noninteractive-components"></a>呈现非交互式组件

在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件。 由于该组件是以静态方式呈现的，因此它不是交互式组件：

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

有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

## <a name="component-namespaces"></a>组件命名空间

使用自定义文件夹保存项目的 Razor 组件时，将表示文件夹的命名空间添加到页面/视图或 `_ViewImports.cshtml` 文件。 如下示例中：

* 组件存储在项目的 `Components` 文件夹中。
* `{APP NAMESPACE}` 占位符是项目的文件夹。 `Components` 表示文件夹的名称。

```cshtml
@using {APP NAMESPACE}.Components
```

`_ViewImports.cshtml` 文件位于 Razor Pages 应用的 `Pages` 文件夹中，或是 MVC 应用的 `Views` 文件夹中。

有关详细信息，请参阅 <xref:blazor/components/index#namespaces>。

## <a name="additional-blazor-server-resources"></a>其他 Blazor Server 资源

* [状态管理：处理预呈现](xref:blazor/state-management?pivot=server#handle-prerendering)
* 与预呈现相关的 Razor 组件生命周期主题
  * [组件初始化 (`OnInitialized{Async}`)](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)
  * [组件呈现后 (`OnAfterRender{Async}`)](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)
  * [预呈现后的有状态重新连接](xref:blazor/components/lifecycle#stateful-reconnection-after-prerendering)
  * [检测应用何时预呈现](xref:blazor/components/lifecycle#detect-when-the-app-is-prerendering)
* [身份验证和授权：一般方面](xref:blazor/security/index#aspnet-core-blazor-authentication-and-authorization)
* [Blazor Server 重新呈现](xref:blazor/fundamentals/handle-errors?pivot=server#blazor-server-prerendering-server)
* [托管和部署：Blazor Server](xref:blazor/host-and-deploy/server)
* [威胁缓解：跨站点脚本 (XSS)](xref:blazor/security/server/threat-mitigation#cross-site-scripting-xss)

::: zone-end
