---
title: ASP.NET Core Blazor 布局
author: guardrex
description: 了解如何为 Blazor 应用创建可重用布局组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2021
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
uid: blazor/layouts
ms.openlocfilehash: 9f3400b766a043fdf3872838bffe392eddba4049
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102394937"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor 布局

有些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分。 将这些元素的标记副本放入应用的所有组件是一种效率较低的做法。 每次更新其中一个元素时，都必须同时更新使用该元素的每个组件。 这种方法的维护成本很高，并且如果缺少更新，还可能会导致内容不一致。 “布局”可以解决这些问题。

Blazor 布局是一个 Razor 组件，它与引用它的组件共享标记。 布局可以使用[数据绑定](xref:blazor/components/data-binding)、[依赖关系注入](xref:blazor/fundamentals/dependency-injection)和组件的其他功能。

## <a name="layout-components"></a>布局组件

### <a name="create-a-layout-component"></a>创建布局组件

要创建布局组件：

* 创建由 Razor 模板或 C# 代码定义的 Razor 组件。 基于 Razor 模板的布局组件像普通 Razor 组件一样使用 `.razor` 文件扩展名。 由于布局组件是在应用组件间共享的，因此它们通常放置在应用的 `Shared` 文件夹中。 但是，布局可以放置在使用它的组件可访问的任何位置。 例如，可以将布局放在使用它的组件所在的同一文件夹中。
* 组件继承自 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>。 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 为布局内呈现的内容定义 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 属性（<xref:Microsoft.AspNetCore.Components.RenderFragment> 类型）。
* 使用 Razor 语法 `@Body` 在布局标记中指定呈现内容的位置。

以下 `DoctorWhoLayout` 组件显示布局组件的 Razor 模板。 布局继承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 并在导航栏 (`<nav>...</nav>`) 和页脚 (`<footer>...</footer>`) 之间设置 `@Body`。

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a>`MainLayout` 组件

在从 [Blazor 项目模板](xref:blazor/project-structure)创建的应用中，`MainLayout` 组件就是应用的[默认布局](#apply-a-default-layout-to-an-app)。

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

[Blazor 的 CSS 隔离功能](xref:blazor/components/css-isolation)将独立 CSS 样式应用于 `MainLayout` 组件。 按照惯例，样式由相同名称的随附样式表 `Shared/MainLayout.razor.css` 提供。 ASP.NET Core 框架的样式表实现可以在 [ASP.NET Core 参考源（dotnet/aspnetcore GitHub 存储库）](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css)中查阅。

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a>应用布局

### <a name="apply-a-layout-to-a-component"></a>向组件应用布局

使用 [`@layout`](xref:mvc/views/razor#layout) Razor 指令将布局应用于具有 [`@page`](xref:mvc/views/razor#page) 指令的可路由 Razor 组件。 编译器将 `@layout` 转换为 <xref:Microsoft.AspNetCore.Components.LayoutAttribute>，并将特性应用于组件类。

以下 `Episodes` 组件的内容插入到 `DoctorWhoLayout` 中的 `@Body` 位置：

`Pages/Episodes.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

以下呈现的 HTML 标记由前面的 `DoctorWhoLayout` 和 `Episodes` 组件生成。 此处不会出现无关标记，以使读者能够专注于这两个相关组件提供的内容：

* 标头 (`<header>...</header>`) 中的 **Doctor Who&trade; Episode Database** 标题 (`<h1>...</h1>`)、导航栏 (`<nav>...</nav>`) 和页脚 (`<footer>...</footer>`) 中的商标信息元素 (`<div>...</div>`) 来自于 `DoctorWhoLayout` 组件。
* **Episodes** 标题 (`<h2>...</h2>`) 和剧集列表 (`<ul>...</ul>`) 来自于 `Episodes` 组件。

```html
<body>
    <div id="app">
        <header>
            <h1>Doctor Who&trade; Episode Database</h1>
        </header>

        <nav>
            <a href="masterlist">Master Episode List</a>
            <a href="search">Search</a>
            <a href="new">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <footer>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </footer>
    </div>
</body>
```

直接在组件中指定布局会覆盖 *默认布局*：

* 由从 `_Imports` 组件 (`_Imports.razor`) 导入的 `@layout` 指令设置，如下文[向组件文件夹应用布局](#apply-a-layout-to-a-folder-of-components)部分所述。
* 设置为应用的默认布局，详见本文后面的[将默认布局应用于应用](#apply-a-default-layout-to-an-app)部分所述。

### <a name="apply-a-layout-to-a-folder-of-components"></a>向组件文件夹应用布局

应用的每个文件夹都可以选择包含名为 `_Imports.razor` 的模板文件。 编译器将导入文件中指定的指令包括在同一文件夹中的所有 Razor 模板内，并在其所有子文件夹中以递归方式包括。 因此，包含 `@layout DoctorWhoLayout` 的 `_Imports.razor` 文件可确保文件夹中的所有组件都使用 `DoctorWhoLayout` 组件。 无需将 `@layout DoctorWhoLayout` 重复添加到文件夹和子文件夹内的所有 Razor 组件 (`.razor`)。

`_Imports.razor`:

```razor
@layout DoctorWhoLayout
...
```

`_Imports.razor` 文件类似于 [Razor 视图和页面的 _ViewImports.cshtml 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 Razor 组件文件。

在 `_Imports.razor` 中指定布局会覆盖指定为路由器的[默认应用布局](#apply-a-default-layout-to-an-app)的布局，如下一部分所述。

> [!WARNING]
> 请 **勿** 向根 `_Imports.razor` 文件添加 Razor `@layout` 指令，这会导致布局形成无限循环。 请在 `Router` 组件中指定布局，以控制默认应用布局。 有关更多信息，请参阅下文[将默认布局应用于应用](#apply-a-default-layout-to-an-app)部分。

> [!NOTE]
> [`@layout`](xref:mvc/views/razor#layout) Razor 指令只对具有 [`@page`](xref:mvc/views/razor#page) 指令的可路由 Razor 组件应用布局。

### <a name="apply-a-default-layout-to-an-app"></a>将默认布局应用于应用

在 `App` 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件中指定默认应用布局。 以下示例来自基于 [Blazor 项目模板](xref:blazor/project-structure)的应用，这个示例将默认布局设置为 `MainLayout` 组件。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

有关 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的详细信息，请参阅 <xref:blazor/fundamentals/routing>。

在 `Router` 组件中将布局指定为默认布局是一种非常有用的做法，因为您可以根据本文前面几个部分所述，为每个组件或每个文件夹覆盖布局。 建议使用 `Router` 组件设置应用的默认布局，因为它是使用布局的最通用且灵活的方法。

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a>将布局应用于任意内容（`LayoutView` 组件）

若要为任意 Razor 模板内容设置布局，请使用 <xref:Microsoft.AspNetCore.Components.LayoutView> 组件指定布局。 您可以在任何 Razor 组件中使用 <xref:Microsoft.AspNetCore.Components.LayoutView>。 下面的示例为 `MainLayout` 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板 (`<NotFound>...</NotFound>`) 设置了一个名为 `ErrorLayout` 的布局组件。

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a>嵌套布局

组件可以引用一个布局，该布局又可以引用另一个布局。 例如，嵌套布局可用于创建多级菜单结构。

以下示例演示如何使用嵌套布局。 [向组件应用布局](#apply-a-layout-to-a-component)部分中显示的 `Episodes` 组件是要显示的组件。 该组件引用 `DoctorWhoLayout` 组件。

以下 `DoctorWhoLayout` 组件是前文所示示例的修改版本。 标头和页脚元素已经删除，并且布局引用了另一个布局 `ProductionsLayout`。 在 `DoctorWhoLayout` 中出现 `@Body` 的位置，呈现 `Episodes` 组件。

`Shared/DoctorWhoLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

`ProductionsLayout` 组件包含顶级布局元素，其中包含有标头 (`<header>...</header>`) 和页脚 (`<footer>...</footer>`) 元素。 具有 `Episodes` 组件的 `DoctorWhoLayout` 会在出现 `@Body` 的位置呈现。

`Shared/ProductionsLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

以下呈现的 HTML 标记由前面的嵌套布局生成。 此处不会出现无关标记，以使读者能够专注于这三个相关组件提供的嵌套内容：

* 标头 (`<header>...</header>`)、生产导航栏 (`<nav>...</nav>`) 和页脚 (`<footer>...</footer>`) 元素以及它们的内容来自于 `ProductionsLayout` 组件。
* **Doctor Who&trade; Episode Database** 标题 (`<h1>...</h1>`)、剧集导航栏 (`<nav>...</nav>`) 和商标信息元素 (`<div>...</div>`) 来自于 `DoctorWhoLayout` 组件。
* **Episodes** 标题 (`<h2>...</h2>`) 和剧集列表 (`<ul>...</ul>`) 来自于 `Episodes` 组件。

```html
<body>
    <div id="app">
        <header>
            <h1>Productions</h1>
        </header>

        <nav>
            <a href="master-production-list">Master Production List</a>
            <a href="production-search">Search</a>
            <a href="new-production">Add Production</a>
        </nav>

        <h1>Doctor Who&trade; Episode Database</h1>

        <nav>
            <a href="episode-masterlist">Master Episode List</a>
            <a href="episode-search">Search</a>
            <a href="new-episode">Add Episode</a>
        </nav>

        <h2>Episodes</h2>

        <ul>
            <li>...</li>
            <li>...</li>
            <li>...</li>
        </ul>

        <div>
            Doctor Who is a registered trademark of the BBC. 
            https://www.doctorwho.tv/
        </div>

        <footer>
            Footer of Productions Layout
        </footer>
    </div>
</body>
```

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>与集成组件共享 Razor Pages 布局

当可路由组件集成到 Razor Pages 应用中时，该应用的共享布局可与这些组件配合使用。 有关详细信息，请参阅 <xref:blazor/components/prerendering-and-integration>。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/layout>
