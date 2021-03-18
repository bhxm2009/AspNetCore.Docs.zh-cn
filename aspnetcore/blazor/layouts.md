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
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="c1f2b-103">ASP.NET Core Blazor 布局</span><span class="sxs-lookup"><span data-stu-id="c1f2b-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="c1f2b-104">有些应用元素（例如菜单、版权消息和公司徽标）通常是应用整体布局的一部分。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-104">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall presentation.</span></span> <span data-ttu-id="c1f2b-105">将这些元素的标记副本放入应用的所有组件是一种效率较低的做法。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-105">Placing a copy of the markup for these elements into all of the components of an app isn't efficient.</span></span> <span data-ttu-id="c1f2b-106">每次更新其中一个元素时，都必须同时更新使用该元素的每个组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-106">Every time that one of these elements is updated, every component that uses the element must be updated.</span></span> <span data-ttu-id="c1f2b-107">这种方法的维护成本很高，并且如果缺少更新，还可能会导致内容不一致。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-107">This approach is costly to maintain and can lead to inconsistent content if an update is missed.</span></span> <span data-ttu-id="c1f2b-108">“布局”可以解决这些问题。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-108">*Layouts* solve these problems.</span></span>

<span data-ttu-id="c1f2b-109">Blazor 布局是一个 Razor 组件，它与引用它的组件共享标记。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-109">A Blazor layout is a Razor component that shares markup with components that reference it.</span></span> <span data-ttu-id="c1f2b-110">布局可以使用[数据绑定](xref:blazor/components/data-binding)、[依赖关系注入](xref:blazor/fundamentals/dependency-injection)和组件的其他功能。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-110">Layouts can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other features of components.</span></span>

## <a name="layout-components"></a><span data-ttu-id="c1f2b-111">布局组件</span><span class="sxs-lookup"><span data-stu-id="c1f2b-111">Layout components</span></span>

### <a name="create-a-layout-component"></a><span data-ttu-id="c1f2b-112">创建布局组件</span><span class="sxs-lookup"><span data-stu-id="c1f2b-112">Create a layout component</span></span>

<span data-ttu-id="c1f2b-113">要创建布局组件：</span><span class="sxs-lookup"><span data-stu-id="c1f2b-113">To create a layout component:</span></span>

* <span data-ttu-id="c1f2b-114">创建由 Razor 模板或 C# 代码定义的 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-114">Create a Razor component defined by a Razor template or C# code.</span></span> <span data-ttu-id="c1f2b-115">基于 Razor 模板的布局组件像普通 Razor 组件一样使用 `.razor` 文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-115">Layout components based on a Razor template use the `.razor` file extension just like ordinary Razor components.</span></span> <span data-ttu-id="c1f2b-116">由于布局组件是在应用组件间共享的，因此它们通常放置在应用的 `Shared` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-116">Because layout components are shared across an app's components, they're usually placed in the app's `Shared` folder.</span></span> <span data-ttu-id="c1f2b-117">但是，布局可以放置在使用它的组件可访问的任何位置。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-117">However, layouts can be placed in any location accessible to the components that use it.</span></span> <span data-ttu-id="c1f2b-118">例如，可以将布局放在使用它的组件所在的同一文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-118">For example, a layout can be placed in the same folder as the components that use it.</span></span>
* <span data-ttu-id="c1f2b-119">组件继承自 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-119">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="c1f2b-120"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 为布局内呈现的内容定义 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 属性（<xref:Microsoft.AspNetCore.Components.RenderFragment> 类型）。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-120">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property (<xref:Microsoft.AspNetCore.Components.RenderFragment> type) for the rendered content inside the layout.</span></span>
* <span data-ttu-id="c1f2b-121">使用 Razor 语法 `@Body` 在布局标记中指定呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-121">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="c1f2b-122">以下 `DoctorWhoLayout` 组件显示布局组件的 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-122">The following `DoctorWhoLayout` component shows the Razor template of a layout component.</span></span> <span data-ttu-id="c1f2b-123">布局继承 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> 并在导航栏 (`<nav>...</nav>`) 和页脚 (`<footer>...</footer>`) 之间设置 `@Body`。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-123">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar (`<nav>...</nav>`) and the footer (`<footer>...</footer>`).</span></span>

<span data-ttu-id="c1f2b-124">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-124">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout.razor?highlight=1,13)]

::: moniker-end

### <a name="mainlayout-component"></a><span data-ttu-id="c1f2b-125">`MainLayout` 组件</span><span class="sxs-lookup"><span data-stu-id="c1f2b-125">`MainLayout` component</span></span>

<span data-ttu-id="c1f2b-126">在从 [Blazor 项目模板](xref:blazor/project-structure)创建的应用中，`MainLayout` 组件就是应用的[默认布局](#apply-a-default-layout-to-an-app)。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-126">In an app created from a [Blazor project template](xref:blazor/project-structure), the `MainLayout` component is the app's [default layout](#apply-a-default-layout-to-an-app).</span></span>

<span data-ttu-id="c1f2b-127">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-127">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

<span data-ttu-id="c1f2b-128">[Blazor 的 CSS 隔离功能](xref:blazor/components/css-isolation)将独立 CSS 样式应用于 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-128">[Blazor's CSS isolation feature](xref:blazor/components/css-isolation) applies isolated CSS styles to the `MainLayout` component.</span></span> <span data-ttu-id="c1f2b-129">按照惯例，样式由相同名称的随附样式表 `Shared/MainLayout.razor.css` 提供。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-129">By convention, the styles are provided by the accompanying stylesheet of the same name, `Shared/MainLayout.razor.css`.</span></span> <span data-ttu-id="c1f2b-130">ASP.NET Core 框架的样式表实现可以在 [ASP.NET Core 参考源（dotnet/aspnetcore GitHub 存储库）](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css)中查阅。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-130">The ASP.NET Core framework implementation of the stylesheet is available for inspection in the [ASP.NET Core reference source (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/Shared/MainLayout.razor.css).</span></span>

[!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/MainLayout.razor)]

::: moniker-end

## <a name="apply-a-layout"></a><span data-ttu-id="c1f2b-131">应用布局</span><span class="sxs-lookup"><span data-stu-id="c1f2b-131">Apply a layout</span></span>

### <a name="apply-a-layout-to-a-component"></a><span data-ttu-id="c1f2b-132">向组件应用布局</span><span class="sxs-lookup"><span data-stu-id="c1f2b-132">Apply a layout to a component</span></span>

<span data-ttu-id="c1f2b-133">使用 [`@layout`](xref:mvc/views/razor#layout) Razor 指令将布局应用于具有 [`@page`](xref:mvc/views/razor#page) 指令的可路由 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-133">Use the [`@layout`](xref:mvc/views/razor#layout) Razor directive to apply a layout to a routable Razor component that has an [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="c1f2b-134">编译器将 `@layout` 转换为 <xref:Microsoft.AspNetCore.Components.LayoutAttribute>，并将特性应用于组件类。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-134">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute> and applies the attribute to the component class.</span></span>

<span data-ttu-id="c1f2b-135">以下 `Episodes` 组件的内容插入到 `DoctorWhoLayout` 中的 `@Body` 位置：</span><span class="sxs-lookup"><span data-stu-id="c1f2b-135">The content of the following `Episodes` component is inserted into the `DoctorWhoLayout` at the position of `@Body`.</span></span>

<span data-ttu-id="c1f2b-136">`Pages/Episodes.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-136">`Pages/Episodes.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/layouts/Episodes.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="c1f2b-137">以下呈现的 HTML 标记由前面的 `DoctorWhoLayout` 和 `Episodes` 组件生成。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-137">The following rendered HTML markup is produced by the preceding `DoctorWhoLayout` and `Episodes` component.</span></span> <span data-ttu-id="c1f2b-138">此处不会出现无关标记，以使读者能够专注于这两个相关组件提供的内容：</span><span class="sxs-lookup"><span data-stu-id="c1f2b-138">Extraneous markup doesn't appear in order to focus on the content provided by the two components involved:</span></span>

* <span data-ttu-id="c1f2b-139">标头 (`<header>...</header>`) 中的 **Doctor Who&trade; Episode Database** 标题 (`<h1>...</h1>`)、导航栏 (`<nav>...</nav>`) 和页脚 (`<footer>...</footer>`) 中的商标信息元素 (`<div>...</div>`) 来自于 `DoctorWhoLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-139">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`) in the header (`<header>...</header>`), navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) in the footer (`<footer>...</footer>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="c1f2b-140">**Episodes** 标题 (`<h2>...</h2>`) 和剧集列表 (`<ul>...</ul>`) 来自于 `Episodes` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-140">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

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

<span data-ttu-id="c1f2b-141">直接在组件中指定布局会覆盖 *默认布局*：</span><span class="sxs-lookup"><span data-stu-id="c1f2b-141">Specifying the layout directly in a component overrides a *default layout*:</span></span>

* <span data-ttu-id="c1f2b-142">由从 `_Imports` 组件 (`_Imports.razor`) 导入的 `@layout` 指令设置，如下文[向组件文件夹应用布局](#apply-a-layout-to-a-folder-of-components)部分所述。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-142">Set by an `@layout` directive imported from an `_Imports` component (`_Imports.razor`), as described in the following [Apply a layout to a folder of components](#apply-a-layout-to-a-folder-of-components) section.</span></span>
* <span data-ttu-id="c1f2b-143">设置为应用的默认布局，详见本文后面的[将默认布局应用于应用](#apply-a-default-layout-to-an-app)部分所述。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-143">Set as the app's default layout, as described in the [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section later in this article.</span></span>

### <a name="apply-a-layout-to-a-folder-of-components"></a><span data-ttu-id="c1f2b-144">向组件文件夹应用布局</span><span class="sxs-lookup"><span data-stu-id="c1f2b-144">Apply a layout to a folder of components</span></span>

<span data-ttu-id="c1f2b-145">应用的每个文件夹都可以选择包含名为 `_Imports.razor` 的模板文件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-145">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="c1f2b-146">编译器将导入文件中指定的指令包括在同一文件夹中的所有 Razor 模板内，并在其所有子文件夹中以递归方式包括。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-146">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="c1f2b-147">因此，包含 `@layout DoctorWhoLayout` 的 `_Imports.razor` 文件可确保文件夹中的所有组件都使用 `DoctorWhoLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-147">Therefore, an `_Imports.razor` file containing `@layout DoctorWhoLayout` ensures that all of the components in a folder use the `DoctorWhoLayout` component.</span></span> <span data-ttu-id="c1f2b-148">无需将 `@layout DoctorWhoLayout` 重复添加到文件夹和子文件夹内的所有 Razor 组件 (`.razor`)。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-148">There's no need to repeatedly add `@layout DoctorWhoLayout` to all of the Razor components (`.razor`) within the folder and subfolders.</span></span>

<span data-ttu-id="c1f2b-149">`_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-149">`_Imports.razor`:</span></span>

```razor
@layout DoctorWhoLayout
...
```

<span data-ttu-id="c1f2b-150">`_Imports.razor` 文件类似于 [Razor 视图和页面的 _ViewImports.cshtml 文件](xref:mvc/views/layout#importing-shared-directives)，但专门应用于 Razor 组件文件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-150">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="c1f2b-151">在 `_Imports.razor` 中指定布局会覆盖指定为路由器的[默认应用布局](#apply-a-default-layout-to-an-app)的布局，如下一部分所述。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-151">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's [default app layout](#apply-a-default-layout-to-an-app), which is described in the following section.</span></span>

> [!WARNING]
> <span data-ttu-id="c1f2b-152">请 **勿** 向根 `_Imports.razor` 文件添加 Razor `@layout` 指令，这会导致布局形成无限循环。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-152">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts.</span></span> <span data-ttu-id="c1f2b-153">请在 `Router` 组件中指定布局，以控制默认应用布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-153">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="c1f2b-154">有关更多信息，请参阅下文[将默认布局应用于应用](#apply-a-default-layout-to-an-app)部分。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-154">For more information, see the following [Apply a default layout to an app](#apply-a-default-layout-to-an-app) section.</span></span>

> [!NOTE]
> <span data-ttu-id="c1f2b-155">[`@layout`](xref:mvc/views/razor#layout) Razor 指令只对具有 [`@page`](xref:mvc/views/razor#page) 指令的可路由 Razor 组件应用布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-155">The [`@layout`](xref:mvc/views/razor#layout) Razor directive only applies a layout to routable Razor components with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>

### <a name="apply-a-default-layout-to-an-app"></a><span data-ttu-id="c1f2b-156">将默认布局应用于应用</span><span class="sxs-lookup"><span data-stu-id="c1f2b-156">Apply a default layout to an app</span></span>

<span data-ttu-id="c1f2b-157">在 `App` 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件中指定默认应用布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-157">Specify the default app layout in the in the `App` component's <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="c1f2b-158">以下示例来自基于 [Blazor 项目模板](xref:blazor/project-structure)的应用，这个示例将默认布局设置为 `MainLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-158">The following example from an app based on a [Blazor project template](xref:blazor/project-structure) sets the default layout to the `MainLayout` component.</span></span>

<span data-ttu-id="c1f2b-159">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-159">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App1.razor?highlight=3)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="c1f2b-160">有关 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-160">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="c1f2b-161">在 `Router` 组件中将布局指定为默认布局是一种非常有用的做法，因为您可以根据本文前面几个部分所述，为每个组件或每个文件夹覆盖布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-161">Specifying the layout as a default layout in the `Router` component is a useful practice because you can override the layout on a per-component or per-folder basis, as described in the preceding sections of this article.</span></span> <span data-ttu-id="c1f2b-162">建议使用 `Router` 组件设置应用的默认布局，因为它是使用布局的最通用且灵活的方法。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-162">We recommend using the `Router` component to set the app's default layout because it's the most general and flexible approach for using layouts.</span></span>

### <a name="apply-a-layout-to-arbitrary-content-layoutview-component"></a><span data-ttu-id="c1f2b-163">将布局应用于任意内容（`LayoutView` 组件）</span><span class="sxs-lookup"><span data-stu-id="c1f2b-163">Apply a layout to arbitrary content (`LayoutView` component)</span></span>

<span data-ttu-id="c1f2b-164">若要为任意 Razor 模板内容设置布局，请使用 <xref:Microsoft.AspNetCore.Components.LayoutView> 组件指定布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-164">To set a layout for arbitrary Razor template content, specify the layout with a <xref:Microsoft.AspNetCore.Components.LayoutView> component.</span></span> <span data-ttu-id="c1f2b-165">您可以在任何 Razor 组件中使用 <xref:Microsoft.AspNetCore.Components.LayoutView>。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-165">You can use a <xref:Microsoft.AspNetCore.Components.LayoutView> in any Razor component.</span></span> <span data-ttu-id="c1f2b-166">下面的示例为 `MainLayout` 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板 (`<NotFound>...</NotFound>`) 设置了一个名为 `ErrorLayout` 的布局组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-166">The following example sets a layout component named `ErrorLayout` for the `MainLayout` component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template (`<NotFound>...</NotFound>`).</span></span>

<span data-ttu-id="c1f2b-167">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-167">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/layouts/App2.razor?name=snippet&highlight=6,9)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="nested-layouts"></a><span data-ttu-id="c1f2b-168">嵌套布局</span><span class="sxs-lookup"><span data-stu-id="c1f2b-168">Nested layouts</span></span>

<span data-ttu-id="c1f2b-169">组件可以引用一个布局，该布局又可以引用另一个布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-169">A component can reference a layout that in turn references another layout.</span></span> <span data-ttu-id="c1f2b-170">例如，嵌套布局可用于创建多级菜单结构。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-170">For example, nested layouts are used to create a multi-level menu structures.</span></span>

<span data-ttu-id="c1f2b-171">以下示例演示如何使用嵌套布局。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-171">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="c1f2b-172">[向组件应用布局](#apply-a-layout-to-a-component)部分中显示的 `Episodes` 组件是要显示的组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-172">The `Episodes` component shown in the [Apply a layout to a component](#apply-a-layout-to-a-component) section is the component to display.</span></span> <span data-ttu-id="c1f2b-173">该组件引用 `DoctorWhoLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-173">The component references the `DoctorWhoLayout` component.</span></span>

<span data-ttu-id="c1f2b-174">以下 `DoctorWhoLayout` 组件是前文所示示例的修改版本。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-174">The following `DoctorWhoLayout` component is a modified version of the example shown earlier in this article.</span></span> <span data-ttu-id="c1f2b-175">标头和页脚元素已经删除，并且布局引用了另一个布局 `ProductionsLayout`。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-175">The header and footer elements are removed, and the layout references another layout, `ProductionsLayout`.</span></span> <span data-ttu-id="c1f2b-176">在 `DoctorWhoLayout` 中出现 `@Body` 的位置，呈现 `Episodes` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-176">The `Episodes` component is rendered where `@Body` appears in the `DoctorWhoLayout`.</span></span>

<span data-ttu-id="c1f2b-177">`Shared/DoctorWhoLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-177">`Shared/DoctorWhoLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/DoctorWhoLayout2.razor?highlight=2,12)]

::: moniker-end

<span data-ttu-id="c1f2b-178">`ProductionsLayout` 组件包含顶级布局元素，其中包含有标头 (`<header>...</header>`) 和页脚 (`<footer>...</footer>`) 元素。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-178">The `ProductionsLayout` component contains the top-level layout elements, where the header (`<header>...</header>`) and footer (`<footer>...</footer>`) elements now reside.</span></span> <span data-ttu-id="c1f2b-179">具有 `Episodes` 组件的 `DoctorWhoLayout` 会在出现 `@Body` 的位置呈现。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-179">The `DoctorWhoLayout` with the `Episodes` component is rendered where `@Body` appears.</span></span>

<span data-ttu-id="c1f2b-180">`Shared/ProductionsLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="c1f2b-180">`Shared/ProductionsLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/layouts/ProductionsLayout.razor?highlight=13)]

::: moniker-end

<span data-ttu-id="c1f2b-181">以下呈现的 HTML 标记由前面的嵌套布局生成。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-181">The following rendered HTML markup is produced by the preceding nested layout.</span></span> <span data-ttu-id="c1f2b-182">此处不会出现无关标记，以使读者能够专注于这三个相关组件提供的嵌套内容：</span><span class="sxs-lookup"><span data-stu-id="c1f2b-182">Extraneous markup doesn't appear in order to focus on the nested content provided by the three components involved:</span></span>

* <span data-ttu-id="c1f2b-183">标头 (`<header>...</header>`)、生产导航栏 (`<nav>...</nav>`) 和页脚 (`<footer>...</footer>`) 元素以及它们的内容来自于 `ProductionsLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-183">The header (`<header>...</header>`), production navigation bar (`<nav>...</nav>`), and footer (`<footer>...</footer>`) elements and their content come from the `ProductionsLayout` component.</span></span>
* <span data-ttu-id="c1f2b-184">**Doctor Who&trade; Episode Database** 标题 (`<h1>...</h1>`)、剧集导航栏 (`<nav>...</nav>`) 和商标信息元素 (`<div>...</div>`) 来自于 `DoctorWhoLayout` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-184">The **Doctor Who&trade; Episode Database** heading (`<h1>...</h1>`), episode navigation bar (`<nav>...</nav>`), and trademark information element (`<div>...</div>`) come from the `DoctorWhoLayout` component.</span></span>
* <span data-ttu-id="c1f2b-185">**Episodes** 标题 (`<h2>...</h2>`) 和剧集列表 (`<ul>...</ul>`) 来自于 `Episodes` 组件。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-185">The **Episodes** heading (`<h2>...</h2>`) and episode list (`<ul>...</ul>`) come from the `Episodes` component.</span></span>

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

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="c1f2b-186">与集成组件共享 Razor Pages 布局</span><span class="sxs-lookup"><span data-stu-id="c1f2b-186">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="c1f2b-187">当可路由组件集成到 Razor Pages 应用中时，该应用的共享布局可与这些组件配合使用。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-187">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="c1f2b-188">有关详细信息，请参阅 <xref:blazor/components/prerendering-and-integration>。</span><span class="sxs-lookup"><span data-stu-id="c1f2b-188">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1f2b-189">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1f2b-189">Additional resources</span></span>

* <xref:mvc/views/layout>
