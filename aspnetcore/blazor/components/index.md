---
title: ASP.NET Core Razor 组件
author: guardrex
description: 了解如何在 Blazor 应用中创建和使用 Razor 组件，包括有关组件中的 Razor 语法、组件命名、命名空间和组件参数的指导。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2021
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
uid: blazor/components/index
ms.openlocfilehash: 723142ab59511392e6fc6d779537bb3cfa01eaa3
ms.sourcegitcommit: 79126facbd939cd21bd3efd02dd5daa4985c6d6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715746"
---
# <a name="aspnet-core-razor-components"></a><span data-ttu-id="5d5d6-103">ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="5d5d6-103">ASP.NET Core Razor components</span></span>

<span data-ttu-id="5d5d6-104">Blazor 应用是使用 Razor 组件构建的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-104">Blazor apps are built using *Razor components*.</span></span> <span data-ttu-id="5d5d6-105">组件是用户界面 (UI) 的自包含部分，具有用于启用动态行为的处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-105">A component is a self-contained portion of user interface (UI) with processing logic to enable dynamic behavior.</span></span> <span data-ttu-id="5d5d6-106">组件可在项目之间嵌套、重复使用和共享。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-106">Components can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="5d5d6-107">组件类</span><span class="sxs-lookup"><span data-stu-id="5d5d6-107">Component classes</span></span>

<span data-ttu-id="5d5d6-108">组件是使用 C# 和 HTML 标记的组合在 [Razor](xref:mvc/views/razor) 组件文件（文件扩展名为 `.razor`）中实现的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-108">Components are implemented using a combination of C# and HTML markup in [Razor](xref:mvc/views/razor) component files with the `.razor` file extension.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="5d5d6-109">Razor 语法</span><span class="sxs-lookup"><span data-stu-id="5d5d6-109">Razor syntax</span></span>

<span data-ttu-id="5d5d6-110">组件使用 [Razor 语法](xref:mvc/views/razor)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-110">Components use [Razor syntax](xref:mvc/views/razor).</span></span> <span data-ttu-id="5d5d6-111">组件广泛使用了两个 Razor 功能，即指令和指令特性 。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-111">Two Razor features are extensively used by components, *directives* and *directive attributes*.</span></span> <span data-ttu-id="5d5d6-112">这两个功能是前缀为 `@` 的保留关键字，出现在 Razor 标记中：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-112">These are reserved keywords prefixed with `@` that appear in Razor markup:</span></span>

* <span data-ttu-id="5d5d6-113">[指令](xref:mvc/views/razor#directives)：更改组件标记的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-113">[Directives](xref:mvc/views/razor#directives): Change the way component markup is parsed or functions.</span></span> <span data-ttu-id="5d5d6-114">例如，[`@page`][9] 指令使用路由模板指定可路由组件，可以由用户请求在浏览器中按特定 URL 直接访问。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-114">For example, the [`@page`][9] directive specifies a routable component with a route template and can be reached directly by a user's request in the browser at a specific URL.</span></span>
* <span data-ttu-id="5d5d6-115">[指令特性](xref:mvc/views/razor#directive-attributes)：更改组件元素的分析方式或运行方式。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-115">[Directive attributes](xref:mvc/views/razor#directive-attributes): Change the way a component element is parsed or functions.</span></span> <span data-ttu-id="5d5d6-116">例如，`<input>` 元素的 [`@bind`][10] 指令特性会将数据绑定到元素的值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-116">For example, the [`@bind`][10] directive attribute for an `<input>` element binds data to the element's value.</span></span>

<span data-ttu-id="5d5d6-117">本文和 Blazor 文档集的其他文章中进一步说明了在组件中使用的指令和指令特性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-117">Directives and directive attributes used in components are explained further in this article and other articles of the Blazor documentation set.</span></span> <span data-ttu-id="5d5d6-118">有关 Razor 语法的一般信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-118">For general information on Razor syntax, see <xref:mvc/views/razor>.</span></span>

### <a name="names"></a><span data-ttu-id="5d5d6-119">名称</span><span class="sxs-lookup"><span data-stu-id="5d5d6-119">Names</span></span>

<span data-ttu-id="5d5d6-120">组件的名称必须以大写字符开头：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-120">A component's name must start with an uppercase character:</span></span>

* <span data-ttu-id="5d5d6-121">`ProductDetail.razor` 有效。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-121">`ProductDetail.razor` is valid.</span></span>
* <span data-ttu-id="5d5d6-122">`productDetail.razor` 无效。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-122">`productDetail.razor` is invalid.</span></span>

<span data-ttu-id="5d5d6-123">整个 Blazor 文档中使用的常见 Blazor 命名约定包括：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-123">Common Blazor naming conventions used throughout the Blazor documentation include:</span></span>

* <span data-ttu-id="5d5d6-124">组件文件路径使用 Pascal 大小写 &dagger;，在显示组件代码示例之前出现。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-124">Component file paths use Pascal case&dagger; and appear before showing component code examples.</span></span> <span data-ttu-id="5d5d6-125">路径指示典型文件夹位置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-125">Paths indicate typical folder locations.</span></span> <span data-ttu-id="5d5d6-126">例如，`Pages/ProductDetail.razor` 指示 `ProductDetail` 组件具有文件名 `ProductDetail.razor`，并位于应用的 `Pages` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-126">For example, `Pages/ProductDetail.razor` indicates that the `ProductDetail` component has a file name of `ProductDetail.razor` and resides in the `Pages` folder of the app.</span></span>
* <span data-ttu-id="5d5d6-127">可路由组件的组件文件路径与其 URL 匹配，对于组件路由模板中各单词之间的空格会显示连字符。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-127">Component file paths for routable components match their URLs with hyphens appearing for spaces between words in a component's route template.</span></span> <span data-ttu-id="5d5d6-128">例如，在浏览器中，通过相对 URL `/product-detail` 请求具有路由模板 `/product-detail` (`@page "/product-detail"`) 的 `ProductDetail` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-128">For example, a `ProductDetail` component with a route template of `/product-detail` (`@page "/product-detail"`) is requested in a browser at the relative URL `/product-detail`.</span></span>

<span data-ttu-id="5d5d6-129">&dagger;Pascal 大小写（大写 camel 形式）是不带空格和标点符号的命名约定，其中每个单词的首字母大写（包括第一个单词）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-129">&dagger;Pascal case (upper camel case) is a naming convention without spaces and punctuation and with the first letter of each word capitalized, including the first word.</span></span>

### <a name="routing"></a><span data-ttu-id="5d5d6-130">路由</span><span class="sxs-lookup"><span data-stu-id="5d5d6-130">Routing</span></span>

<span data-ttu-id="5d5d6-131">可以通过使用 [`@page`][9] 指令为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-131">Routing in Blazor is achieved by providing a route template to each accessible component in the app with an [`@page`][9] directive.</span></span> <span data-ttu-id="5d5d6-132">编译具有 [`@page`][9] 指令的 Razor 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-132">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="5d5d6-133">在运行时，路由器将使用 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 搜索组件类，并呈现具有与请求的 URL 匹配的路由模板的任何组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-133">At runtime, the router searches for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="5d5d6-134">以下 `HelloWorld` 组件使用路由模板 `/hello-world`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-134">The following `HelloWorld` component uses a route template of `/hello-world`.</span></span> <span data-ttu-id="5d5d6-135">可通过相对 URL `/hello-world` 访问组件呈现的网页。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-135">The rendered webpage for the component is reached at the relative URL `/hello-world`.</span></span> <span data-ttu-id="5d5d6-136">当使用默认协议、主机和端口在本地运行 Blazor 应用时，会在浏览器中通过 `https://localhost:5001/hello-world` 请求 `HelloWorld` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-136">When running a Blazor app locally with the default protocol, host, and port, the `HelloWorld` component is requested in the browser at `https://localhost:5001/hello-world`.</span></span> <span data-ttu-id="5d5d6-137">生成网页的组件通常位于 `Pages` 文件夹中，但可以使用任何文件夹保存组件（包括在嵌套文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-137">Components that produce webpages usually reside in the `Pages` folder, but you can use any folder to hold components, including within nested folders.</span></span>

<span data-ttu-id="5d5d6-138">`Pages/HelloWorld.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-138">`Pages/HelloWorld.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/HelloWorld.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/HelloWorld.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-139">前面的组件在浏览器中通过 `/hello-world` 进行加载，无论是否将组件添加到应用的 UI 导航。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-139">The preceding component loads in the browser at `/hello-world` regardless of whether or not you add the component to the app's UI navigation.</span></span> <span data-ttu-id="5d5d6-140">（可选）组件可以添加到 `NavMenu` 组件，以便在应用基于 UI 的导航中显示组件链接。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-140">Optionally, components can be added to the `NavMenu` component so that a link to the component appears in the app's UI-based navigation.</span></span>

<span data-ttu-id="5d5d6-141">对于前面的 `HelloWorld` 组件，将以下 `NavLink` 组件添加到 `NavMenu` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-141">For the preceding `HelloWorld` component, add the following `NavLink` component to the `NavMenu` component.</span></span> <span data-ttu-id="5d5d6-142">在无序列表标记 `<ul>...</ul>` 之间，将 `NavLink` 组件添加到新列表项中 (`<li>...</li>`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-142">Add the `NavLink` component in a new list item (`<li>...</li>`) between the unordered list tags (`<ul>...</ul>`).</span></span>

<span data-ttu-id="5d5d6-143">`Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-143">`Shared/NavMenu.razor`:</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="hello-world">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Hello World!
    </NavLink>
</li>
```

<span data-ttu-id="5d5d6-144">有关详细信息（包括 `NavLink` 和 `NavMenu` 组件的说明），请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-144">For more information, including descriptions of the `NavLink` and `NavMenu` components, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="markup"></a><span data-ttu-id="5d5d6-145">标记</span><span class="sxs-lookup"><span data-stu-id="5d5d6-145">Markup</span></span>

<span data-ttu-id="5d5d6-146">组件的 UI 使用由 Razor 标记、C# 和 HTML 组成的 [Razor 语法](xref:mvc/views/razor)进行定义。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-146">A component's UI is defined using [Razor syntax](xref:mvc/views/razor), which consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="5d5d6-147">在编译应用时，HTML 标记和 C# 呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-147">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="5d5d6-148">生成的类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-148">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="5d5d6-149">组件类的成员在一个或多个 [`@code`][1] 块中定义。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-149">Members of the component class are defined in one or more [`@code`][1] blocks.</span></span> <span data-ttu-id="5d5d6-150">在 [`@code`][1] 块中，组件状态使用 C# 进行指定和处理：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-150">In [`@code`][1] blocks, component state is specified and processed with C#:</span></span>

* <span data-ttu-id="5d5d6-151">属性和字段初始化表达式。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-151">Property and field initializers.</span></span>
* <span data-ttu-id="5d5d6-152">由父组件和路由参数传递的自变量的参数值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-152">Parameter values from arguments passed by parent components and route parameters.</span></span>
* <span data-ttu-id="5d5d6-153">用于用户事件处理、生命周期事件和自定义组件逻辑的方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-153">Methods for user event handling, lifecycle events, and custom component logic.</span></span>

<span data-ttu-id="5d5d6-154">组件成员使用以 `@` 符号开头的 C# 表达式在呈现逻辑中进行使用。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-154">Component members are used in rendering logic using C# expressions that start with the `@` symbol.</span></span> <span data-ttu-id="5d5d6-155">例如，通过为字段名称添加 `@` 前缀来呈现 C# 字段。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-155">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="5d5d6-156">下面 `Markup` 示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-156">The following `Markup` component evaluates and renders:</span></span>

* <span data-ttu-id="5d5d6-157">`headingFontStyle`，表示标题元素的 CSS 属性值 `font-style`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-157">`headingFontStyle` for the CSS property value `font-style` of the heading element.</span></span>
* <span data-ttu-id="5d5d6-158">`headingText`，表示标题元素的内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-158">`headingText` for the content of the heading element.</span></span>

<span data-ttu-id="5d5d6-159">`Pages/Markup.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-159">`Pages/Markup.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/Markup.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/Markup.razor)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5d5d6-160">Blazor 文档中的示例会为私有成员指定 [`private` 访问修饰符](/dotnet/csharp/language-reference/keywords/private)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-160">Examples throughout the Blazor documentation specify the [`private` access modifier](/dotnet/csharp/language-reference/keywords/private) for private members.</span></span> <span data-ttu-id="5d5d6-161">私有成员的范围限定为组件的类。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-161">Private members are scoped to a component's class.</span></span> <span data-ttu-id="5d5d6-162">但是，C# 会在没有访问修饰符存在时采用 `private` 访问修饰符，因此在自己的代码中将成员显式标记为“`private`”是可选的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-162">However, C# assumes the `private` access modifier when no access modifier is present, so explicitly marking members "`private`" in your own code is optional.</span></span> <span data-ttu-id="5d5d6-163">有关访问修饰符的详细信息，请参阅[访问修饰符（C# 编程指南）](/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-163">For more information on access modifiers, see [Access Modifiers (C# Programming Guide)](/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers).</span></span>

<span data-ttu-id="5d5d6-164">Blazor 框架在内部将组件作为[呈现树](https://developer.mozilla.org/docs/Web/Performance/How_browsers_work#render)进行处理，该树是组件的[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 和[级联样式表对象模型 (CSSOM)](https://developer.mozilla.org/docs/Web/API/CSS_Object_Model) 的组合。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-164">The Blazor framework processes a component internally as a [*render tree*](https://developer.mozilla.org/docs/Web/Performance/How_browsers_work#render), which is the combination of a component's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) and [Cascading Style Sheet Object Model (CSSOM)](https://developer.mozilla.org/docs/Web/API/CSS_Object_Model).</span></span> <span data-ttu-id="5d5d6-165">最初呈现组件后，会重新生成组件的呈现树以响应事件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-165">After the component is initially rendered, the component's render tree is regenerated in response to events.</span></span> <span data-ttu-id="5d5d6-166">Blazor 会将新呈现树与以前的呈现树进行比较，并将所有修改应用于浏览器的 DOM 以进行显示。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-166">Blazor compares the new render tree against the previous render tree and applies any modifications to the browser's DOM for display.</span></span> <span data-ttu-id="5d5d6-167">有关更多信息，请参见<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-167">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="5d5d6-168">组件是普通 [C# 类](/dotnet/csharp/programming-guide/classes-and-structs/classes)，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-168">Components are ordinary [C# classes](/dotnet/csharp/programming-guide/classes-and-structs/classes) and can be placed anywhere within a project.</span></span> <span data-ttu-id="5d5d6-169">生成网页的组件通常位于 `Pages` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-169">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="5d5d6-170">非页面组件通常放置在 `Shared` 文件夹或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-170">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="nested-components"></a><span data-ttu-id="5d5d6-171">嵌套组件</span><span class="sxs-lookup"><span data-stu-id="5d5d6-171">Nested components</span></span>

<span data-ttu-id="5d5d6-172">通过使用 HTML 语法声明组件，组件可以包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-172">Components can include other components by declaring them using HTML syntax.</span></span> <span data-ttu-id="5d5d6-173">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-173">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="5d5d6-174">请考虑以下 `Heading` 组件，其他组件可以使用该组件显示标题。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-174">Consider the following `Heading` component, which can be used by other components to display a heading.</span></span>

<span data-ttu-id="5d5d6-175">`Shared/Heading.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-175">`Shared/Heading.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/Heading.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/Heading.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-176">`HeadingExample` 组件中的以下标记会在 `<Heading />` 标记出现的位置呈现前面的 `Heading` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-176">The following markup in the `HeadingExample` component renders the preceding `Heading` component at the location where the `<Heading />` tag appears.</span></span>

<span data-ttu-id="5d5d6-177">`Pages/HeadingExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-177">`Pages/HeadingExample.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/HeadingExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/HeadingExample.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-178">如果某个组件包含一个 HTML 元素，该元素的大写首字母与相同命名空间中的组件名称不匹配，则会发出警告，指示该元素名称异常。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-178">If a component contains an HTML element with an uppercase first letter that doesn't match a component name within the same namespace, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="5d5d6-179">为组件的命名空间添加 [`@using`][2] 指令使组件可用，从而解决此警告。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-179">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span> <span data-ttu-id="5d5d6-180">有关详细信息，请参阅[命名空间](#namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-180">For more information, see the [Namespaces](#namespaces) section.</span></span>

<span data-ttu-id="5d5d6-181">此部分中显示的 `Heading` 组件示例没有 [`@page`][9] 指令，因此用户无法在浏览器中通过直接请求直接访问 `Heading` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-181">The `Heading` component example shown in this section doesn't have an [`@page`][9] directive, so the `Heading` component isn't directly accessible to a user via a direct request in the browser.</span></span> <span data-ttu-id="5d5d6-182">但是，具有 [`@page`][9] 指令的任何组件都可以嵌套在另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-182">However, any component with an [`@page`][9] directive can be nested in another component.</span></span> <span data-ttu-id="5d5d6-183">如果通过在 Razor 文件顶部包含 `@page "/heading"` 可直接访问 `Heading`组件，则会在 `/heading` 和 `/heading-example` 处为浏览器请求呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-183">If the `Heading` component was directly accessible by including `@page "/heading"` at the top of its Razor file, then the component would be rendered for browser requests at both `/heading` and `/heading-example`.</span></span>

### <a name="namespaces"></a><span data-ttu-id="5d5d6-184">命名空间</span><span class="sxs-lookup"><span data-stu-id="5d5d6-184">Namespaces</span></span>

<span data-ttu-id="5d5d6-185">通常，组件的命名空间是从应用的根命名空间和该组件在应用内的位置（文件夹）派生而来的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-185">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="5d5d6-186">如果应用的根命名空间是 `BlazorSample`，并且 `Counter` 组件位于 `Pages` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-186">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="5d5d6-187">`Counter` 组件的命名空间为 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-187">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="5d5d6-188">组件的完全限定类型名称为 `BlazorSample.Pages.Counter`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-188">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="5d5d6-189">对于保存组件的自定义文件夹，将 [`@using`][2] 指令添加到父组件或应用的 `_Imports.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-189">For custom folders that hold components, add an [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="5d5d6-190">下面的示例提供 `Components` 文件夹中的组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-190">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

> [!NOTE]
> <span data-ttu-id="5d5d6-191">`_Imports.razor` 文件中的 [`@using`][2] 指令仅适用于 Razor 文件 (`.razor`)，而不适用于 C# 文件 (`.cs`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-191">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

<span data-ttu-id="5d5d6-192">还可以使用其完全限定的名称来引用组件，而不需要 [`@using`][2] 指令。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-192">Components can also be referenced using their fully qualified names, which doesn't require an [`@using`][2] directive.</span></span> <span data-ttu-id="5d5d6-193">以下示例直接引用应用的 `Components` 文件夹中的 `ProductDetail` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-193">The following example directly references the `ProductDetail` component in the `Components` folder of the app:</span></span>

```razor
<BlazorSample.Components.ProductDetail />
```

<span data-ttu-id="5d5d6-194">使用 Razor 创建的组件的命名空间基于以下内容（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-194">The namespace of a component authored with Razor is based on the following (in priority order):</span></span>

* <span data-ttu-id="5d5d6-195">Razor 文件标记中的 [`@namespace`][8] 指令（例如 `@namespace BlazorSample.CustomNamespace`）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-195">The [`@namespace`][8] directive in the Razor file's markup (for example, `@namespace BlazorSample.CustomNamespace`).</span></span>
* <span data-ttu-id="5d5d6-196">项目文件中项目的 `RootNamespace`（例如 `<RootNamespace>BlazorSample</RootNamespace>`）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-196">The project's `RootNamespace` in the project file (for example, `<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="5d5d6-197">项目名称，取自项目文件的文件名 (`.csproj`)，以及从项目根到组件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-197">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="5d5d6-198">例如，框架将具有项目命名空间 `BlazorSample` (`BlazorSample.csproj`) 的 `{PROJECT ROOT}/Pages/Index.razor` 解析到 `Index` 组件的命名空间 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-198">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` with a project namespace of `BlazorSample` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages` for the `Index` component.</span></span> <span data-ttu-id="5d5d6-199">`{PROJECT ROOT}` 是项目根路径。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-199">`{PROJECT ROOT}` is the project root path.</span></span> <span data-ttu-id="5d5d6-200">组件遵循 C# 名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-200">Components follow C# name binding rules.</span></span> <span data-ttu-id="5d5d6-201">对于本示例中的 `Index` 组件，范围内的组件是所有组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-201">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="5d5d6-202">在同一文件夹 `Pages` 中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-202">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="5d5d6-203">未显式指定其他命名空间的项目根中的组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-203">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="5d5d6-204">不支持以下项目：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-204">The following are **not** supported:</span></span>

* <span data-ttu-id="5d5d6-205">`global::` 限定。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-205">The `global::` qualification.</span></span>
* <span data-ttu-id="5d5d6-206">导入具有别名 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 语句的组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-206">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements.</span></span> <span data-ttu-id="5d5d6-207">例如，`@using Foo = Bar` 不受支持。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-207">For example, `@using Foo = Bar` isn't supported.</span></span>
* <span data-ttu-id="5d5d6-208">部分限定的名称。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-208">Partially-qualified names.</span></span> <span data-ttu-id="5d5d6-209">例如，无法将 `@using BlazorSample` 添加到组件中，然后使用 `<Shared.NavMenu></Shared.NavMenu>` 在应用的 `Shared` 文件夹中引用 `NavMenu` 组件 (`Shared/NavMenu.razor`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-209">For example, you can't add `@using BlazorSample` to a component and then reference the `NavMenu` component in the app's `Shared` folder (`Shared/NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>`.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="5d5d6-210">分部类支持</span><span class="sxs-lookup"><span data-stu-id="5d5d6-210">Partial class support</span></span>

<span data-ttu-id="5d5d6-211">组件以 [C# 分部类](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)的形式生成，使用以下任一方法进行创作：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-211">Components are generated as [C# partial classes](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) and are authored using either of the following approaches:</span></span>

* <span data-ttu-id="5d5d6-212">单个文件包含在一个或多个 [`@code`][1] 块、HTML 标记和 Razor 标记中定义的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-212">A single file contains C# code defined in one or more [`@code`][1] blocks, HTML markup, and Razor markup.</span></span> <span data-ttu-id="5d5d6-213">Blazor 项目模板使用此单文件方法来定义其组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-213">Blazor project templates define their components using this single-file approach.</span></span>
* <span data-ttu-id="5d5d6-214">HTML 和 Razor 标记位于 Razor 文件 (`.razor`) 中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-214">HTML and Razor markup are placed in a Razor file (`.razor`).</span></span> <span data-ttu-id="5d5d6-215">C# 代码位于定义为分部类的代码隐藏文件 (`.cs`) 中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-215">C# code is placed in a code-behind file defined as a partial class (`.cs`).</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5d5d6-216">定义特定于组件的样式的组件样式表是单独的文件 (`.css`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-216">A component stylesheet that defines component-specific styles is a separate file (`.css`).</span></span> <span data-ttu-id="5d5d6-217">Blazor CSS 隔离以后在 <xref:blazor/components/css-isolation>中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-217">Blazor CSS isolation is described later in <xref:blazor/components/css-isolation>.</span></span>

::: moniker-end

<span data-ttu-id="5d5d6-218">下面的示例显示了从 Blazor 项目模板生成的应用中具有 [`@code`][1] 块的默认 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-218">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor project template.</span></span> <span data-ttu-id="5d5d6-219">标记和 C# 代码位于同一个文件中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-219">Markup and C# code are in the same file.</span></span> <span data-ttu-id="5d5d6-220">这是在创作组件时采用的最常见方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-220">This is the most common approach taken in component authoring.</span></span>

<span data-ttu-id="5d5d6-221">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-221">`Pages/Counter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/Counter.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/Counter.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-222">以下 `Counter` 组件使用带有分部类的代码隐藏文件从 C# 代码中拆分 HTML 和 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-222">The following `Counter` component splits HTML and Razor markup from  C# code using a code-behind file with a partial class:</span></span>

<span data-ttu-id="5d5d6-223">`Pages/CounterPartialClass.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-223">`Pages/CounterPartialClass.razor`:</span></span>

```razor
@page "/counter-partial-class"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="5d5d6-224">`Pages/CounterPartialClass.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-224">`Pages/CounterPartialClass.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class CounterPartialClass
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="5d5d6-225">`_Imports.razor` 文件中的 [`@using`][2] 指令仅适用于 Razor 文件 (`.razor`)，而不适用于 C# 文件 (`.cs`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-225">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span> <span data-ttu-id="5d5d6-226">根据需要将命名空间添加到分部类文件中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-226">Add namespaces to a partial class file as needed.</span></span>

<span data-ttu-id="5d5d6-227">组件使用的典型命名空间：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-227">Typical namespaces used by components:</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
using Microsoft.AspNetCore.Components.Web.Virtualization;
using Microsoft.JSInterop;
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
using Microsoft.JSInterop;
```

::: moniker-end

<span data-ttu-id="5d5d6-228">典型命名空间还包含应用的命名空间以及与应用的 `Shared` 文件夹对应的命名空间：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-228">Typical namespaces also include the namespace of the app and the namespace corresponding to the app's `Shared` folder:</span></span>

```csharp
using BlazorSample;
using BlazorSample.Shared;
```

### <a name="specify-a-base-class"></a><span data-ttu-id="5d5d6-229">指定基类</span><span class="sxs-lookup"><span data-stu-id="5d5d6-229">Specify a base class</span></span>

<span data-ttu-id="5d5d6-230">[`@inherits`][6] 指令用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-230">The [`@inherits`][6] directive is used to specify a base class for a component.</span></span> <span data-ttu-id="5d5d6-231">下面的示例演示组件如何继承基类以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-231">The following example shows how a component can inherit a base class to provide the component's properties and methods.</span></span> <span data-ttu-id="5d5d6-232">`BlazorRocksBase` 基类派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-232">The `BlazorRocksBase` base class derives from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="5d5d6-233">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-233">`Pages/BlazorRocks.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/BlazorRocks.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/BlazorRocks.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-234">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-234">`BlazorRocksBase.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/BlazorRocksBase.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/BlazorRocksBase.cs)]

::: moniker-end

## <a name="component-parameters"></a><span data-ttu-id="5d5d6-235">组件参数</span><span class="sxs-lookup"><span data-stu-id="5d5d6-235">Component parameters</span></span>

<span data-ttu-id="5d5d6-236">组件参数将数据传递给组件，使用组件类中包含 [`[Parameter]` 特性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)的公共 [C# 属性](/csharp/programming-guide/classes-and-structs/properties)进行定义。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-236">*Component parameters* pass data to components and are defined using public [C# properties](/csharp/programming-guide/classes-and-structs/properties) on the component class with the [`[Parameter]` attribute](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span></span> <span data-ttu-id="5d5d6-237">在下面的示例中，内置引用类型 (<xref:System.String?displayProperty=fullName>) 和用户定义的引用类型 (`PanelBody`) 作为组件参数进行传递。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-237">In the following example, a built-in reference type (<xref:System.String?displayProperty=fullName>) and a user-defined reference type (`PanelBody`) are passed as component parameters.</span></span>

<span data-ttu-id="5d5d6-238">`PanelBody.cs`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-238">`PanelBody.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/PanelBody.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/PanelBody.cs)]

::: moniker-end

<span data-ttu-id="5d5d6-239">`Shared/ParameterChild.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-239">`Shared/ParameterChild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/ParameterChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/ParameterChild.razor)]

::: moniker-end

> [!WARNING]
> <span data-ttu-id="5d5d6-240">支持为组件参数提供初始值，但不会创建在首次呈现后向自身参数写入的组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-240">Providing initial values for component parameters is supported, but don't create a component that writes to its own parameters after the component is rendered for the first time.</span></span> <span data-ttu-id="5d5d6-241">有关详细信息，请参阅本文的[重写参数](#overwritten-parameters)部分。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-241">For more information, see the [Overwritten parameters](#overwritten-parameters) section of this article.</span></span>

<span data-ttu-id="5d5d6-242">`ParameterChild` 组件的 `Title` 和 `Body` 组件参数通过自变量在呈现组件实例的 HTML 标记中进行设置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-242">The `Title` and `Body` component parameters of the `ParameterChild` component are set by arguments in the HTML tag that renders the instance of the component.</span></span> <span data-ttu-id="5d5d6-243">以下 `ParameterParent` 组件会呈现两个 `ParameterChild` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-243">The following `ParameterParent` component renders two `ParameterChild` components:</span></span>

* <span data-ttu-id="5d5d6-244">第一个 `ParameterChild` 组件在呈现时不提供参数自变量。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-244">The first `ParameterChild` component is rendered without supplying parameter arguments.</span></span>
* <span data-ttu-id="5d5d6-245">第二个 `ParameterChild` 组件从 `ParameterParent` 组件接收 `Title` 和 `Body` 的值，后者使用[显式 C# 表达式](xref:mvc/views/razor#explicit-razor-expressions)设置 `PanelBody` 的属性值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-245">The second `ParameterChild` component receives values for `Title` and `Body` from the `ParameterParent` component, which uses an [explicit C# expression](xref:mvc/views/razor#explicit-razor-expressions) to set the values of the `PanelBody`'s properties.</span></span>

<span data-ttu-id="5d5d6-246">`Pages/ParameterParent.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-246">`Pages/ParameterParent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ParameterParent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ParameterParent.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-247">当 `ParameterParent` 组件未提供组件参数值时，来自 `ParameterParent` 组件的以下呈现 HTML 标记会显示 `ParameterChild` 组件默认值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-247">The following rendered HTML markup from the `ParameterParent` component shows `ParameterChild` component default values when the `ParameterParent` component doesn't supply component parameter values.</span></span> <span data-ttu-id="5d5d6-248">当 `ParameterParent` 组件提供组件参数值时，它们会替换 `ParameterChild` 组件的默认值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-248">When the `ParameterParent` component provides component parameter values, they replace the `ParameterChild` component's default values.</span></span>

> [!NOTE]
> <span data-ttu-id="5d5d6-249">为进行阐明，呈现 CSS 样式类未显示在以下呈现 HTML 标记中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-249">For clarify, rendered CSS style classes aren't shown in the following rendered HTML markup.</span></span>

```html
<h1>Child component (without attribute values)</h1>

<div>
    <div>Set By Child</div>
    <div>Set by child.</div>
</div>

<h1>Child component (with attribute values)</h1>

<div>
    <div>Set by Parent</div>
    <div>Set by parent.</div>
</div>
```

<span data-ttu-id="5d5d6-250">使用 [Razor 的保留 `@` 符号](xref:mvc/views/razor#razor-syntax)，将方法的 C# 字段、属性或结果作为 HTML 特性值分配给组件参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-250">Assign a C# field, property, or result of a method to a component parameter as an HTML attribute value using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax).</span></span> <span data-ttu-id="5d5d6-251">以下 `ParameterParent2` 组件显示前面 `ParameterChild` 组件的四个实例，并将其 `Title` 参数值设置为：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-251">The following `ParameterParent2` component displays four instances of the preceding `ParameterChild` component and sets their `Title` parameter values to:</span></span>

* <span data-ttu-id="5d5d6-252">`title` 字段的值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-252">The value of the `title` field.</span></span>
* <span data-ttu-id="5d5d6-253">`GetTitle` C# 方法的结果。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-253">The result of the `GetTitle` C# method.</span></span>
* <span data-ttu-id="5d5d6-254">带有<xref:System.DateTime.ToLongDateString%2A> 的长格式当前本地日期，使用[隐式 C# 表达式](xref:mvc/views/razor#implicit-razor-expressions)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-254">The current local date in long format with <xref:System.DateTime.ToLongDateString%2A>, which uses an [implicit C# expression](xref:mvc/views/razor#implicit-razor-expressions).</span></span>
* <span data-ttu-id="5d5d6-255">`panelData` 对象的 `Title` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-255">The `panelData` object's `Title` property.</span></span>

<span data-ttu-id="5d5d6-256">`Pages/ParameterParent2.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-256">`Pages/ParameterParent2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ParameterParent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ParameterParent2.razor)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5d5d6-257">将 C# 成员分配给组件参数时，请使用符号 `@` 为成员添加前缀，绝不要为参数的 HTML 特性添加前缀。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-257">When assigning a C# member to a component parameter, prefix the member with the `@` symbol and never prefix the parameter's HTML attribute.</span></span>
>
> <span data-ttu-id="5d5d6-258">正确：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-258">Correct:</span></span>
>
> ```razor
> <ParameterChild Title="@title" />
> ```
>
> <span data-ttu-id="5d5d6-259">不正确：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-259">Incorrect:</span></span>
>
> ```razor
> <ParameterChild @Title="title" />
> ```

<span data-ttu-id="5d5d6-260">与 Razor 页面 (`.cshtml`) 不同，在呈现组件时，Blazor 不能在 Razor 表达式中执行异步工作。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-260">Unlike in Razor pages (`.cshtml`), Blazor can't perform asynchronous work in a Razor expression while rendering a component.</span></span> <span data-ttu-id="5d5d6-261">这是因为 Blazor 是为呈现交互式 UI 而设计的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-261">This is because Blazor is designed for rendering interactive UIs.</span></span> <span data-ttu-id="5d5d6-262">在交互式 UI 中，屏幕必须始终显示某些内容，因此阻止呈现流是没有意义的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-262">In an interactive UI, the screen must always display something, so it doesn't make sense to block the rendering flow.</span></span> <span data-ttu-id="5d5d6-263">相反，异步工作是在一个[异步生命周期事件](xref:blazor/components/lifecycle)期间执行的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-263">Instead, asynchronous work is performed during one of the [asynchronous lifecycle events](xref:blazor/components/lifecycle).</span></span> <span data-ttu-id="5d5d6-264">在每个异步生命周期事件之后，组件可能会再次呈现。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-264">After each asynchronous lifecycle event, the component may render again.</span></span> <span data-ttu-id="5d5d6-265">不支持以下 Razor 语法：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-265">The following Razor syntax is **not** supported:</span></span>

```razor
<ParameterChild Title="@await ..." />
```

<span data-ttu-id="5d5d6-266">生成应用时，前面示例中的代码会生成编译器错误：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-266">The code in the preceding example generates a *compiler error* when the app is built:</span></span>

> <span data-ttu-id="5d5d6-267">“await”运算符只能用于异步方法中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-267">The 'await' operator can only be used within an async method.</span></span> <span data-ttu-id="5d5d6-268">请考虑用“async”修饰符标记此方法，并将其返回类型更改为“Task”。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-268">Consider marking this method with the 'async' modifier and changing its return type to 'Task'.</span></span>

<span data-ttu-id="5d5d6-269">若要在前面的示例中异步获取 `Title` 参数的值，组件可以使用 [`OnInitializedAsync` 生命周期事件](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-269">To obtain a value for the `Title` parameter in the preceding example asychronously, the component can use the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-oninitializedasync), as the following example demonstrates:</span></span>

```razor
<ParameterChild Title="@title" />

@code {
    private string title;
    
    protected override async Task OnInitializedAsync()
    {
        title = await ...;
    }
}
```

<span data-ttu-id="5d5d6-270">有关更多信息，请参见<xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-270">For more information, see <xref:blazor/components/lifecycle>.</span></span>

<span data-ttu-id="5d5d6-271">不支持使用显式 Razor 表达式连接文本和表达式结果以赋值给参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-271">Use of an explicit Razor expression to concatenate text with an expression result for assignment to a parameter is **not** supported.</span></span> <span data-ttu-id="5d5d6-272">下面的示例尝试将文本“`Set by `”与对象属性值连接在一起。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-272">The following example seeks to concatenate the text "`Set by `" with an object's property value.</span></span> <span data-ttu-id="5d5d6-273">尽管 Razor 页面 (`.cshtml`) 支持此语法，但对在组件中赋值给子级的 `Title` 参数无效。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-273">Although this syntax is supported in a Razor page (`.cshtml`), it isn't valid for assignment to the child's `Title` parameter in a component.</span></span> <span data-ttu-id="5d5d6-274">不支持以下 Razor 语法：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-274">The following Razor syntax is **not** supported:</span></span>

```razor
<ParameterChild Title="Set by @(panelData.Title)" />
```

<span data-ttu-id="5d5d6-275">生成应用时，前面示例中的代码会生成编译器错误：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-275">The code in the preceding example generates a *compiler error* when the app is built:</span></span>

> <span data-ttu-id="5d5d6-276">组件属性不支持复杂内容（混合 C# 和标记）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-276">Component attributes do not support complex content (mixed C# and markup).</span></span>

<span data-ttu-id="5d5d6-277">若要支持组合值赋值，请使用方法、字段或属性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-277">To support the assignment of a composed value, use a method, field, or property.</span></span> <span data-ttu-id="5d5d6-278">下面的示例在 C# 方法 `GetTitle` 中将“`Set by `”与对象属性值连接在一起：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-278">The following example performs the concatination of "`Set by `" and an object's property value in the C# method `GetTitle`:</span></span>

<span data-ttu-id="5d5d6-279">`Pages/ParameterParent3.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-279">`Pages/ParameterParent3.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ParameterParent3.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ParameterParent3.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-280">有关详细信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-280">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="5d5d6-281">支持为组件参数提供初始值，但不会创建在首次呈现后向自身参数写入的组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-281">Providing initial values for component parameters is supported, but don't create a component that writes to its own parameters after the component is rendered for the first time.</span></span> <span data-ttu-id="5d5d6-282">有关详细信息，请参阅本文的[重写参数](#overwritten-parameters)部分。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-282">For more information, see the [Overwritten parameters](#overwritten-parameters) section of this article.</span></span>

<span data-ttu-id="5d5d6-283">应将组件参数声明为自动属性，这意味着它们不应在其 `get` 或 `set` 访问器中包含自定义逻辑。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-283">Component parameters should be declared as *auto-properties*, meaning that they shouldn't contain custom logic in their `get` or `set` accessors.</span></span> <span data-ttu-id="5d5d6-284">例如，下面的 `StartData` 属性是自动属性：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-284">For example, the following `StartData` property is an auto-property:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

<span data-ttu-id="5d5d6-285">不要在 `get` 或 `set` 访问器中放置自定义逻辑，因为组件参数专门用作父组件向子组件传送信息的通道。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-285">Don't place custom logic in the `get` or `set` accessor because component parameters are purely intended for use as a channel for a parent component to flow information to a child component.</span></span> <span data-ttu-id="5d5d6-286">如果子组件属性的 `set` 访问器包含导致父组件重新呈现的逻辑，则会导致一个无限的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-286">If a `set` accessor of a child component property contains logic that causes rerendering of the parent component, an infinite rendering loop results.</span></span>

<span data-ttu-id="5d5d6-287">若要转换已接收的参数值，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-287">To transform a received parameter value:</span></span>

* <span data-ttu-id="5d5d6-288">将参数属性保留为自动属性，以表示所提供的原始数据。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-288">Leave the parameter property as an auto-property to represent the supplied raw data.</span></span>
* <span data-ttu-id="5d5d6-289">创建另一个属性或方法，用于基于参数属性提供转换后的数据。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-289">Create a different property or method to supply the transformed data based on the parameter property.</span></span>

<span data-ttu-id="5d5d6-290">重写 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set-onparameterssetasync) 以在每次收到新数据时转换接收到的参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-290">Override [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set-onparameterssetasync) to transform a received parameter each time new data is received.</span></span>

<span data-ttu-id="5d5d6-291">支持将初始值写入组件参数，因为初始值赋值不会干扰 Blazor 的自动组件呈现。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-291">Writing an initial value to a component parameter is supported because initial value assignments don't interfere with the Blazor's automatic component rendering.</span></span> <span data-ttu-id="5d5d6-292">在组件中，使用 <xref:System.DateTime.Now?displayProperty=nameWithType> 将当前本地 <xref:System.DateTime> 赋予 `StartData` 是有效语法：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-292">The following assignment of the current local <xref:System.DateTime> with <xref:System.DateTime.Now?displayProperty=nameWithType> to `StartData` is valid syntax in a component:</span></span>

```csharp
[Parameter]
public DateTime StartData { get; set; } = DateTime.Now;
```

<span data-ttu-id="5d5d6-293">进行 <xref:System.DateTime.Now?displayProperty=nameWithType> 的初始赋值之后，请勿在开发人员代码中向 `StartData` 赋值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-293">After the initial assignment of <xref:System.DateTime.Now?displayProperty=nameWithType>, do **not** assign a value to `StartData` in developer code.</span></span> <span data-ttu-id="5d5d6-294">有关详细信息，请参阅本文的[重写参数](#overwritten-parameters)部分。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-294">For more information, see the [Overwritten parameters](#overwritten-parameters) section of this article.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="5d5d6-295">路由参数</span><span class="sxs-lookup"><span data-stu-id="5d5d6-295">Route parameters</span></span>

<span data-ttu-id="5d5d6-296">组件可以在 [`@page`][9] 指令的路由模板中指定路由参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-296">Components can specify route parameters in the route template of the [`@page`][9] directive.</span></span> <span data-ttu-id="5d5d6-297">[Blazor 路由器](xref:blazor/fundamentals/routing)使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-297">The [Blazor router](xref:blazor/fundamentals/routing) uses route parameters to populate corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5d5d6-298">支持可选路由参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-298">Optional route parameters are supported.</span></span> <span data-ttu-id="5d5d6-299">在下面的示例中，`text` 可选参数将 route 段的值赋给组件的 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-299">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="5d5d6-300">如果该段不存在，则在 [`OnInitialized` 生命周期方法](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)中将 `Text` 的值设置为 `fantastic`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-300">If the segment isn't present, the value of `Text` is set to "`fantastic`" in the [`OnInitialized` lifecycle method](xref:blazor/components/lifecycle#component-initialization-oninitializedasync).</span></span>

<span data-ttu-id="5d5d6-301">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-301">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/RouteParameter.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5d5d6-302">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-302">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="5d5d6-303">不支持可选路由参数，因此在前面的示例中应用了两个 [`@page`][9] 指令。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-303">Optional route parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="5d5d6-304">第一个 [`@page`][9] 指令允许导航到没有路由参数的组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-304">The first [`@page`][9] directive permits navigation to the component without a route parameter.</span></span> <span data-ttu-id="5d5d6-305">第二个 [`@page`][9] 指令会接收 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-305">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="5d5d6-306">要了解 catch-all 路由参数 `{*pageRoute}`（它可跨多个文件夹边界捕获路径），请参阅 <xref:blazor/fundamentals/routing#catch-all-route-parameters>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-306">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="5d5d6-307">重写参数</span><span class="sxs-lookup"><span data-stu-id="5d5d6-307">Overwritten parameters</span></span>

<span data-ttu-id="5d5d6-308">Blazor 框架通常会施加安全的父级到子级参数的赋值：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-308">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="5d5d6-309">不会意外覆盖参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-309">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="5d5d6-310">最大程度地减少副作用。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-310">Side-effects are minimized.</span></span> <span data-ttu-id="5d5d6-311">例如，可避免附加的呈现，因为它们可能会创建无限的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-311">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="5d5d6-312">当父组件重新呈现时，子组件会接收可能覆盖现有值的新参数值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-312">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="5d5d6-313">当开发带有一个或多个数据绑定参数的组件，并且开发人员直接写入子组件中的参数时，经常会发生意外覆盖子组件中的参数值：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-313">Accidentally overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="5d5d6-314">子组件通过父组件中的一个或多个参数值呈现。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-314">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="5d5d6-315">子级直接写入参数的值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-315">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="5d5d6-316">父组件重新呈现并覆盖子参数的值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-316">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="5d5d6-317">覆盖参数值的可能性也会延伸到子组件的属性 `set` 访问器中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-317">The potential for overwriting parameter values extends into the child component's property `set` accessors, too.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5d5d6-318">我们的通用指南不是创建在首次呈现后直接向自身参数写入的组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-318">Our general guidance is not to create components that directly write to their own parameters after the component is rendered for the first time.</span></span>

<span data-ttu-id="5d5d6-319">请考虑使用以下有故障的 `Expander` 组件，它们会：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-319">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="5d5d6-320">呈现子内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-320">Renders child content.</span></span>
* <span data-ttu-id="5d5d6-321">切换以使用组件参数 (`Expanded`) 显示子内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-321">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="5d5d6-322">组件直接写入 `Expanded` 参数，该参数演示了覆盖的参数存在的问题，应避免这样做。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-322">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

<span data-ttu-id="5d5d6-323">在下面的 `Expander` 组件演示了此方案的不正确方法之后，会显示修改后的 `Expander` 组件以演示正确的方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-323">After the following `Expander` component demonstrates the incorrect approach for this scenario, a modified `Expander` component is shown to demonstrate the correct approach.</span></span> <span data-ttu-id="5d5d6-324">下面的示例可以放置在本地示例应中，以体验所述的行为。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-324">The following examples can be placed in a local sample app to experience the behaviors described.</span></span>

<span data-ttu-id="5d5d6-325">`Shared/Expander.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-325">`Shared/Expander.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/BadExpander.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/BadExpander.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-326">`Expander` 组件会添加到可调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 的以下 `ExpanderExample` 父组件中：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-326">The `Expander` component is added to the following `ExpanderExample` parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

* <span data-ttu-id="5d5d6-327">在开发人员代码中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会向组件通知其状态已更改，并且通常会触发组件呈现以更新 UI。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-327">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in developer code notifies a component that its state has changed and typically triggers component rerendering to update the UI.</span></span> <span data-ttu-id="5d5d6-328">以后在 <xref:blazor/components/lifecycle>和 <xref:blazor/components/rendering>中会更详细地讨论 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-328"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is covered in more detail later in <xref:blazor/components/lifecycle> and <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="5d5d6-329">按钮的 `@onclick` 指令特性会将事件处理程序附加到按钮的 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-329">The button's `@onclick` directive attribute attaches an event handler to the button's `onclick` event.</span></span> <span data-ttu-id="5d5d6-330">以后在 <xref:blazor/components/event-handling>中会更详细地讨论事件处理。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-330">Event handling is covered in more detail later in <xref:blazor/components/event-handling>.</span></span>

<span data-ttu-id="5d5d6-331">`Pages/ExpanderExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-331">`Pages/ExpanderExample.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ExpanderExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ExpanderExample.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-332">最初，在切换 `Expanded` 属性时，`Expander` 组件独立地作出行为。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-332">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="5d5d6-333">子组件会按预期方式维护其状态。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-333">The child components maintain their states as expected.</span></span> <span data-ttu-id="5d5d6-334">在父组件中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 时，第一个子组件的 `Expanded` 会重新重置为它的初始值 (`true`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-334">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="5d5d6-335">第二个 `Expander` 组件的 `Expanded` 值不会重置，因为第二个组件中没有呈现任何子内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-335">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="5d5d6-336">要维持在前述情况中的状态，请在 `Expander` 组件中使用私有字段来保留它的切换状态。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-336">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="5d5d6-337">以下经修定的 `Expander` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-337">The following revised `Expander` component:</span></span>

* <span data-ttu-id="5d5d6-338">接受父项中的 `Expanded` 组件参数值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-338">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="5d5d6-339">将组件参数值分配给 [`OnInitialized` 事件](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)中的私有字段 (`expanded`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-339">Assigns the component parameter value to a *private field* (`expanded`) in the [`OnInitialized` event](xref:blazor/components/lifecycle#component-initialization-oninitializedasync).</span></span>
* <span data-ttu-id="5d5d6-340">使用私有字段来维护其内部切换状态，该状态演示如何避免直接写入参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-340">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

> [!NOTE]
> <span data-ttu-id="5d5d6-341">此部分中的建议可扩展到组件参数 `set` 访问器中的类似逻辑，这可能会产生类似的不良副作用。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-341">The advice in this section extends to similar logic in component parameter `set` accessors, which can result in similar undesirable side-effects.</span></span>

<span data-ttu-id="5d5d6-342">`Shared/Expander.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-342">`Shared/Expander.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/Expander.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/Expander.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-343">有关其他信息，请参阅 [Blazor 双向绑定错误 (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-343">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span>

## <a name="child-content"></a><span data-ttu-id="5d5d6-344">子内容</span><span class="sxs-lookup"><span data-stu-id="5d5d6-344">Child content</span></span>

<span data-ttu-id="5d5d6-345">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-345">Components can set the content of another component.</span></span> <span data-ttu-id="5d5d6-346">分配组件提供子组件的开始标记与结束标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-346">The assigning component provides the content between the child component's opening and closing tags.</span></span>

<span data-ttu-id="5d5d6-347">在下面的示例中，`RenderFragmentChild` 组件具有一个 `ChildContent` 属性，它将要呈现的 UI 段表示为 <xref:Microsoft.AspNetCore.Components.RenderFragment>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-347">In the following example, the `RenderFragmentChild` component has a `ChildContent` property that represents a segment of the UI to render as a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="5d5d6-348">`ChildContent` 在组件 Razor 标记中的位置是在最终 HTML 输出中呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-348">The position of `ChildContent` in the component's Razor markup is where the content is rendered in the final HTML output.</span></span>

<span data-ttu-id="5d5d6-349">`Shared/RenderFragmentChild.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-349">`Shared/RenderFragmentChild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/RenderFragmentChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/RenderFragmentChild.razor)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="5d5d6-350">必须按约定将接收 <xref:Microsoft.AspNetCore.Components.RenderFragment> 内容的属性命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-350">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="5d5d6-351">以下 `RenderFragmentParent` 组件通过将内容置于子组件的开始标记和结束标记内，来提供用于呈现 `RenderFragmentChild` 的内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-351">The following `RenderFragmentParent` component provides content for rendering the `RenderFragmentChild` by placing the content inside the child component's opening and closing tags.</span></span>

<span data-ttu-id="5d5d6-352">`Pages/RenderFragmentParent.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-352">`Pages/RenderFragmentParent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/RenderFragmentParent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/RenderFragmentParent.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-353">由于 Blazor 呈现子内容的方式，如果在 `RenderFragmentChild` 组件的内容中使用递增循环变量，则在 [`for`](/dotnet/csharp/language-reference/keywords/for) 循环内呈现组件需要本地索引变量。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-353">Due to the way that Blazor renders child content, rendering components inside a [`for`](/dotnet/csharp/language-reference/keywords/for) loop requires a local index variable if the incrementing loop variable is used in the `RenderFragmentChild` component's content.</span></span> <span data-ttu-id="5d5d6-354">下面的示例可以添加到前面的 `RenderFragmentParent` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-354">The following example can be added to the preceding `RenderFragmentParent` component:</span></span>

```razor
<h1>Three children with an index variable</h1>

@for (int c = 0; c < 3; c++)
{
    var current = c;

    <RenderFragmentChild>
        Count: @current
    </RenderFragmentChild>
}
```

<span data-ttu-id="5d5d6-355">或者，将 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环与 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> 结合使用，而不是使用 [`for`](/dotnet/csharp/language-reference/keywords/for) 循环。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-355">Alternatively, use a [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> instead of a [`for`](/dotnet/csharp/language-reference/keywords/for) loop.</span></span> <span data-ttu-id="5d5d6-356">下面的示例可以添加到前面的 `RenderFragmentParent` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-356">The following example can be added to the preceding `RenderFragmentParent` component:</span></span>

```razor
<h1>Second example of three children with an index variable</h1>

@foreach (var c in Enumerable.Range(0,3))
{
    <RenderFragmentChild>
        Count: @c
    </RenderFragmentChild>
}
```

<span data-ttu-id="5d5d6-357">若要了解如何将 <xref:Microsoft.AspNetCore.Components.RenderFragment> 用作组件 UI 的模板，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-357">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="5d5d6-358">属性展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="5d5d6-358">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="5d5d6-359">除了组件的声明参数外，组件还可以捕获和呈现其他属性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-359">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="5d5d6-360">其他特性可以在字典中捕获，然后在使用 [`@attributes`][3] Razor 指令特性呈现组件时，将其展开到元素上。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-360">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive attribute.</span></span> <span data-ttu-id="5d5d6-361">对于定义生成支持各种自定义项的标记元素的组件，此方案非常有用。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-361">This scenario is useful for defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="5d5d6-362">例如，为支持多个参数的 `<input>` 单独定义属性可能比较繁琐。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-362">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="5d5d6-363">在下面的 `Splat` 组件中：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-363">In the following `Splat` component:</span></span>

* <span data-ttu-id="5d5d6-364">第一个 `<input>` 元素 (`id="useIndividualParams"`) 使用单个组件参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-364">The first `<input>` element (`id="useIndividualParams"`) uses individual component parameters.</span></span>
* <span data-ttu-id="5d5d6-365">第二个 `<input>` 元素 (`id="useAttributesDict"`) 使用特性展开。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-365">The second `<input>` element (`id="useAttributesDict"`) uses attribute splatting.</span></span>

<span data-ttu-id="5d5d6-366">`Pages/Splat.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-366">`Pages/Splat.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/Splat.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/Splat.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-367">网页中呈现的 `<input>` 元素是相同的：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-367">The rendered `<input>` elements in the webpage are identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="5d5d6-368">若要接受任意特性，请定义[组件参数](#component-parameters)，并将 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-368">To accept arbitrary attributes, define a [component parameter](#component-parameters) with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="5d5d6-369">借助 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 上的 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性，参数可以匹配所有不匹配其他任何参数的特性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-369">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="5d5d6-370">组件只能使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-370">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="5d5d6-371">与 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 一起使用的属性类型必须可以使用字符串键从 [`Dictionary<string, object>`](xref:System.Collections.Generic.Dictionary%602) 中分配。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-371">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from [`Dictionary<string, object>`](xref:System.Collections.Generic.Dictionary%602) with string keys.</span></span> <span data-ttu-id="5d5d6-372">使用 [`IEnumerable<KeyValuePair<string, object>>`](xref:System.Collections.Generic.IEnumerable%601) 或 [`IReadOnlyDictionary<string, object>`](xref:System.Collections.Generic.IReadOnlyDictionary%602) 也是此方案中的选项。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-372">Use of [`IEnumerable<KeyValuePair<string, object>>`](xref:System.Collections.Generic.IEnumerable%601) or [`IReadOnlyDictionary<string, object>`](xref:System.Collections.Generic.IReadOnlyDictionary%602) are also options in this scenario.</span></span>

<span data-ttu-id="5d5d6-373">相对于元素特性位置的 [`@attributes`][3] 位置很重要。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-373">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="5d5d6-374">在元素上展开 [`@attributes`][3] 时，将从右到左（从最后一个到第一个）处理特性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-374">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="5d5d6-375">请考虑以下使用子组件的父组件示例：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-375">Consider the following example of a parent component that consumes a child component:</span></span>

<span data-ttu-id="5d5d6-376">`Shared/AttributeOrderChild1.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-376">`Shared/AttributeOrderChild1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild1.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-377">`Pages/AttributeOrderParent1.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-377">`Pages/AttributeOrderParent1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent1.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-378">`AttributeOrderChild1` 组件的 `extra` 属性设置为 [`@attributes`][3] 右侧。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-378">The `AttributeOrderChild1` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="5d5d6-379">通过附加特性传递时，`AttributeOrderParent1` 组件的呈现的 `<div>` 包含 `extra="5"`，因为特性是从右到左（从最后一个到第一个）处理的：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-379">The `AttributeOrderParent1` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="5d5d6-380">在下面的示例中，`extra` 和 [`@attributes`][3] 的顺序在子组件的 `<div>` 中反转：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-380">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the child component's `<div>`:</span></span>

<span data-ttu-id="5d5d6-381">`Shared/AttributeOrderChild2.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-381">`Shared/AttributeOrderChild2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild2.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-382">`Pages/AttributeOrderParent2.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-382">`Pages/AttributeOrderParent2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent2.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-383">通过附加特性传递时，父组件呈现的网页中的 `<div>` 包含 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-383">The `<div>` in the parent component's rendered webpage contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="5d5d6-384">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="5d5d6-384">Capture references to components</span></span>

<span data-ttu-id="5d5d6-385">组件引用提供了一种引用组件实例以便发出命令的方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-385">Component references provide a way to reference a component instance for issuing commands.</span></span> <span data-ttu-id="5d5d6-386">若要捕获组件引用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-386">To capture a component reference:</span></span>

* <span data-ttu-id="5d5d6-387">向子组件添加 [`@ref`][4] 特性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-387">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="5d5d6-388">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-388">Define a field with the same type as the child component.</span></span>

<span data-ttu-id="5d5d6-389">呈现组件时，将用组件实例填充字段。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-389">When the component is rendered, the field is populated with the component instance.</span></span> <span data-ttu-id="5d5d6-390">然后，可以在实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-390">You can then invoke .NET methods on the instance.</span></span>

<span data-ttu-id="5d5d6-391">请考虑以下 `ReferenceChild` 组件，它会在调用其 `ChildMethod` 时记录消息。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-391">Consider the following `ReferenceChild` component that logs a message when its `ChildMethod` is called.</span></span>

<span data-ttu-id="5d5d6-392">`Shared/ReferenceChild.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-392">`Shared/ReferenceChild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/ReferenceChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/ReferenceChild.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-393">组件引用仅在呈现组件后才进行填充，其输出包含 `ReferenceChild` 的元素。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-393">A component reference is only populated after the component is rendered and its output includes `ReferenceChild`'s element.</span></span> <span data-ttu-id="5d5d6-394">在呈现组件之前，没有任何可引用的内容。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-394">Until the component is rendered, there's nothing to reference.</span></span>

<span data-ttu-id="5d5d6-395">若要在组件完成呈现后操作组件引用，请使用 [`OnAfterRender` 或 `OnAfterRenderAsync` 方法](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-395">To manipulate component references after the component has finished rendering, use the [`OnAfterRender` or `OnAfterRenderAsync` methods](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync).</span></span>

<span data-ttu-id="5d5d6-396">若要结合使用事件处理程序和引用变量，请使用 Lambda 表达式，或在 [`OnAfterRender` 或 `OnAfterRenderAsync` 方法](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)中分配事件处理程序委托。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-396">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRender` or `OnAfterRenderAsync` methods](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync).</span></span> <span data-ttu-id="5d5d6-397">这可确保在分配事件处理程序之前先分配引用变量。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-397">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>

<span data-ttu-id="5d5d6-398">以下 lambda 方法使用前面的 `ReferenceChild` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-398">The following lambda approach uses the preceding `ReferenceChild` component.</span></span>

<span data-ttu-id="5d5d6-399">`Pages/ReferenceParent1.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-399">`Pages/ReferenceParent1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent1.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-400">以下委托方法使用前面的 `ReferenceChild` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-400">The following delegate approach uses the preceding `ReferenceChild` component.</span></span>

<span data-ttu-id="5d5d6-401">`Pages/ReferenceParent2.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-401">`Pages/ReferenceParent2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent2.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-402">在循环中使用集合引用组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-402">Use a collection to reference components in a loop.</span></span> <span data-ttu-id="5d5d6-403">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-403">In the following example:</span></span>

* <span data-ttu-id="5d5d6-404">组件会添加到 <xref:System.Collections.Generic.List%601> 中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-404">Components are added to a <xref:System.Collections.Generic.List%601>.</span></span>
* <span data-ttu-id="5d5d6-405">会为每个组件创建一个按钮，它通过其在 `ChildMethod` 中的组件索引触发相应组件的 <xref:System.Collections.Generic.List%601>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-405">A button is created for each component that triggers the corresponding component's `ChildMethod` by its component index in the <xref:System.Collections.Generic.List%601>.</span></span>

<span data-ttu-id="5d5d6-406">`Pages/ReferenceParent3.razor` 使用前面的 `ReferenceChild` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-406">`Pages/ReferenceParent3.razor` using the preceding `ReferenceChild` component:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent3.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent3.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-407">尽管捕获组件引用使用与[捕获元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)类似的语法，但捕获组件引用不是 JavaScript 互操作功能。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-407">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), capturing component references isn't a JavaScript interop feature.</span></span> <span data-ttu-id="5d5d6-408">组件引用不会传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-408">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="5d5d6-409">组件引用只在 .NET 代码中使用。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-409">Component references are only used in .NET code.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5d5d6-410">不要使用组件引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-410">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="5d5d6-411">请改用常规声明性组件参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-411">Instead, use normal declarative component parameters to pass data to child components.</span></span> <span data-ttu-id="5d5d6-412">使用组件参数使子组件在正确的时间自动重新呈现。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-412">Use of component parameters result in child components that rerender at the correct times automatically.</span></span> <span data-ttu-id="5d5d6-413">有关详细信息，请参阅[组件参数](#component-parameters)部分和文章 <xref:blazor/components/data-binding>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-413">For more information, see the [component parameters](#component-parameters) section and the <xref:blazor/components/data-binding> article.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="5d5d6-414">同步上下文</span><span class="sxs-lookup"><span data-stu-id="5d5d6-414">Synchronization context</span></span>

<span data-ttu-id="5d5d6-415">Blazor 使用同步上下文 (<xref:System.Threading.SynchronizationContext>) 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-415">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="5d5d6-416">组件的[生命周期方法](xref:blazor/components/lifecycle)和 Blazor 引发的事件回调都在此同步上下文上执行。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-416">A component's [lifecycle methods](xref:blazor/components/lifecycle) and event callbacks raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="5d5d6-417">Blazor Server的同步上下文尝试模拟单线程环境，使其与浏览器中的单线程 WebAssembly 模型紧密匹配。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-417">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="5d5d6-418">在任意给定的时间点，工作只在一个线程上执行，这会造成单个逻辑线程的印象。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-418">At any given point in time, work is performed on exactly one thread, which yields the impression of a single logical thread.</span></span> <span data-ttu-id="5d5d6-419">不会同时执行两个操作。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-419">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="5d5d6-420">避免阻止线程的调用</span><span class="sxs-lookup"><span data-stu-id="5d5d6-420">Avoid thread-blocking calls</span></span>

<span data-ttu-id="5d5d6-421">通常，不要在组件中调用以下方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-421">Generally, don't call the following methods in components.</span></span> <span data-ttu-id="5d5d6-422">以下方法阻止执行线程，进而阻止应用继续工作，直到基础 <xref:System.Threading.Tasks.Task> 完成：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-422">The following methods block the execution thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

> [!NOTE]
> <span data-ttu-id="5d5d6-423">使用此部分中所述的线程阻止方法的 Blazor 文档示例只是使用方法进行演示，而不是用作建议编码指导。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-423">Blazor documentation examples that use the thread-blocking methods mentioned in this section are only using the methods for demonstration purposes, not as recommended coding guidance.</span></span> <span data-ttu-id="5d5d6-424">例如，一些组件代码演示通过调用 <xref:System.Threading.Thread.Sleep%2A?displayProperty=nameWithType> 来模拟长时间运行的进程。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-424">For example, a few component code demonstrations simulate a long-running process by calling <xref:System.Threading.Thread.Sleep%2A?displayProperty=nameWithType>.</span></span>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="5d5d6-425">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="5d5d6-425">Invoke component methods externally to update state</span></span>

<span data-ttu-id="5d5d6-426">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法，它将代码执行调度到 Blazor 的同步上下文。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-426">In the event a component must be updated based on an external event, such as a timer or other notification, use the `InvokeAsync` method, which dispatches code execution to Blazor's synchronization context.</span></span> <span data-ttu-id="5d5d6-427">例如，请考虑以下通告程序服务，它可向任何侦听组件通知更新的状态。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-427">For example, consider the following *notifier service* that can notify any listening component about updated state.</span></span> <span data-ttu-id="5d5d6-428">可以从应用中的任何位置调用 `Update` 方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-428">The `Update` method can be called from anywhere in the app.</span></span>

<span data-ttu-id="5d5d6-429">`Notifier.cs`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-429">`Notifier.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Notifier.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Notifier.cs)]

::: moniker-end

<span data-ttu-id="5d5d6-430">注册 `Notifier` 服务：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-430">Register the `Notifier` service:</span></span>

* <span data-ttu-id="5d5d6-431">在 Blazor WebAssembly 应用中，在 `Program.Main` 中将服务注册为单一实例：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-431">In a Blazor WebAssembly app, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<Notifier>();
  ```

* <span data-ttu-id="5d5d6-432">在 Blazor Server 应用中，在 `Startup.ConfigureServices` 中将服务注册为有作用域：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-432">In a Blazor Server app, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<Notifier>();
  ```

<span data-ttu-id="5d5d6-433">使用 `Notifier` 服务更新组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-433">Use the `Notifier` service to update a component.</span></span>

<span data-ttu-id="5d5d6-434">`Pages/NotifierExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-434">`Pages/NotifierExample.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/NotifierExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/NotifierExample.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-435">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-435">In the preceding example:</span></span>

* <span data-ttu-id="5d5d6-436">`Notifier` 在 Blazor 的同步上下文之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-436">`Notifier` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="5d5d6-437">`InvokeAsync` 用于切换到正确的上下文，并将呈现排入队列。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-437">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="5d5d6-438">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-438">For more information, see <xref:blazor/components/rendering>.</span></span>
* <span data-ttu-id="5d5d6-439">组件会实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-439">The component implements <xref:System.IDisposable>.</span></span> <span data-ttu-id="5d5d6-440">`OnNotify` 委托在 `Dispose` 方法中取消订阅，在释放组件时，框架会调用此方法。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-440">The `OnNotify` delegate is unsubscribed in the `Dispose` method, which is called by the framework when the component is disposed.</span></span> <span data-ttu-id="5d5d6-441">有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-441">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="5d5d6-442">使用 \@ 键控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="5d5d6-442">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="5d5d6-443">在呈现元素或组件的列表并且元素或组件随后发生更改时，Blazor 必须决定之前的元素或组件中有哪些可以保留，以及模型对象应如何映射到这些元素或组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-443">When rendering a list of elements or components and the elements or components subsequently change, Blazor must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="5d5d6-444">通常，此过程是自动的，可以忽略，但在某些情况下，可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-444">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="5d5d6-445">请考虑使用以下 `Details` 和 `People` 组件：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-445">Consider the following `Details` and `People` components:</span></span>

* <span data-ttu-id="5d5d6-446">`Details` 组件从 `People` 父组件接收数据 `Data`，这些数据会显示在 `<input>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-446">The `Details` component receives data (`Data`) from the parent `People` component, which is displayed in an `<input>` element.</span></span> <span data-ttu-id="5d5d6-447">当用户选择一个 `<input>` 元素时，显示的任何给定 `<input>` 元素都可以从用户接收页面焦点。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-447">Any given displayed `<input>` element can receive the focus of the page from the user when they select one of the `<input>` elements.</span></span>
* <span data-ttu-id="5d5d6-448">`People` 组件使用 `Details` 组件创建人员对象列表以进行显示 。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-448">The `People` component creates a list of person objects for display using the `Details` component.</span></span> <span data-ttu-id="5d5d6-449">每三秒向集合中添加一个新人员。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-449">Every three seconds, a new person is added to the collection.</span></span>

<span data-ttu-id="5d5d6-450">此演示使你可以：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-450">This demonstration allows you to:</span></span>

* <span data-ttu-id="5d5d6-451">从多个呈现的 `Details` 组件中选择一个 `<input>`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-451">Select an `<input>` from among several rendered `Details` components.</span></span>
* <span data-ttu-id="5d5d6-452">随着人员集合自动增长，研究页面焦点的行为。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-452">Study the behavior of the page's focus as the people collection automatically grows.</span></span>

<span data-ttu-id="5d5d6-453">`Shared/Details.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-453">`Shared/Details.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/Details.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/Details.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-454">在以下 `People` 组件中，在 `OnTimerCallback` 中添加人员的每个迭代都会导致 Blazor 重新生成整个集合。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-454">In the following `People` component, each iteration of adding a person in `OnTimerCallback` results in Blazor rebuilding the entire collection.</span></span> <span data-ttu-id="5d5d6-455">页面的焦点保持在 `<input>` 元素的相同索引位置处，因此每次添加人员时，焦点都会移动。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-455">The page's focus remains on the *same index* position of `<input>` elements, so the focus shifts each time a person is added.</span></span> <span data-ttu-id="5d5d6-456">将焦点从用户选择的内容移开是不可取的行为。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-456">*Shifting the focus away from what the user selected isn't desirable behavior.*</span></span> <span data-ttu-id="5d5d6-457">使用以下组件演示不良行为后，使用 [`@key`][5] 指令特性改善用户的体验。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-457">After demonstrating the poor behavior with the following component, the [`@key`][5] directive attribute is used to improve the user's experience.</span></span>

<span data-ttu-id="5d5d6-458">`Pages/People.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-458">`Pages/People.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/People.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/People.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-459">`people` 集合的内容会随插入、删除或重新排序的条目而更改。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-459">The contents of the `people` collection changes with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="5d5d6-460">重新呈现可能会导致可见的行为差异。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-460">Rerendering can lead to visible behavior differences.</span></span> <span data-ttu-id="5d5d6-461">每次向 `people` 集合中插入一个人员时，当前具有焦点的元素的前一个元素便会接收焦点。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-461">Each time a person is inserted into the `people` collection, the *preceding element* of the currently focused element receives the focus.</span></span> <span data-ttu-id="5d5d6-462">用户的焦点会丢失。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-462">The user's focus is lost.</span></span>

<span data-ttu-id="5d5d6-463">可通过 [`@key`][5] 指令特性来控制元素或组件到集合的映射过程。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-463">The mapping process of elements or components to a collection can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="5d5d6-464">使用 [`@key`][5] 可保证基于键的值保留元素或组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-464">Use of [`@key`][5] guarantees the preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="5d5d6-465">如果前面示例中的 `Details` 组件在 `person` 项上键入，则 Blazor 会忽略重新呈现未更改的 `Details` 组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-465">If the `Details` component in the preceding example is keyed on the `person` item, Blazor ignores rerendering `Details` components that haven't changed.</span></span>

<span data-ttu-id="5d5d6-466">若要修改 `People` 组件以将 [`@key`][5] 指令特性用于 `people` 集合，请将 `<Details>` 元素更新为以下内容：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-466">To modify the `People` component to use the [`@key`][5] directive attribute with the `people` collection, update the `<Details>` element to the following:</span></span>

```razor
<Details @key="person" Data="@person.Data" />
```

<span data-ttu-id="5d5d6-467">当 `people` 集合发生更改时，会保留 `Details` 实例与 `person` 实例之间的关联。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-467">When the `people` collection changes, the association between `Details` instances and `person` instances is retained.</span></span> <span data-ttu-id="5d5d6-468">当在集合的开头插入 `Person` 时，会在相应位置插入一个新的 `Details` 实例。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-468">When a `Person` is inserted at the beginning of the collection, one new `Details` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="5d5d6-469">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-469">Other instances are left unchanged.</span></span> <span data-ttu-id="5d5d6-470">因此，在将人员添加到集合时，用户的焦点不会丢失。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-470">Therefore, the user's focus isn't lost as people are added to the collection.</span></span>

<span data-ttu-id="5d5d6-471">使用 [`@key`][5] 指令特性时，其他集合更新会表现出相同的行为：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-471">Other collection updates exhibit the same behavior when the [`@key`][5] directive attribute is used:</span></span>

* <span data-ttu-id="5d5d6-472">如果从集合中删除实例，则仅从 UI 中删除相应的组件实例。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-472">If an instance is deleted from the collection, only the corresponding component instance is removed from the UI.</span></span> <span data-ttu-id="5d5d6-473">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-473">Other instances are left unchanged.</span></span>
* <span data-ttu-id="5d5d6-474">如果对集合项进行重新排序，则保留相应的组件实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-474">If collection entries are re-ordered, the corresponding component instances are preserved and re-ordered in the UI.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5d5d6-475">对于每个容器元素或组件而言，键是本地的。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-475">Keys are local to each container element or component.</span></span> <span data-ttu-id="5d5d6-476">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-476">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="5d5d6-477">何时使用 \@key</span><span class="sxs-lookup"><span data-stu-id="5d5d6-477">When to use \@key</span></span>

<span data-ttu-id="5d5d6-478">通常，每当呈现列表（例如，在 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 块中）以及存在适当的值定义 [`@key`][5] 时，都可以使用 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-478">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="5d5d6-479">在对象未更改时，也可以使用 [`@key`][5] 保留元素或组件子树，如下面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-479">You can also use [`@key`][5] to preserve an element or component subtree when an object doesn't change, as the following examples show.</span></span>

<span data-ttu-id="5d5d6-480">示例 1：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-480">Example 1:</span></span>

```razor
<li @key="person">
    <input value="@person.Data" />
</li>
```

<span data-ttu-id="5d5d6-481">示例 2：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-481">Example 2:</span></span>

```razor
<div @key="person">
    @* other HTML elements *@
</div>
```

<span data-ttu-id="5d5d6-482">如果 `person` 实例更改，则 [`@key`][5] 特性指令会强制 Blazor：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-482">If an `person` instance changes, the [`@key`][5] attribute directive forces Blazor to:</span></span>

* <span data-ttu-id="5d5d6-483">放弃整个 `<li>` 或 `<div>` 及其后代。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-483">Discard the entire `<li>` or `<div>` and their descendants.</span></span>
* <span data-ttu-id="5d5d6-484">在 UI 中使用新元素和组件重新生成子树。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-484">Rebuild the subtree within the UI with new elements and components.</span></span>

<span data-ttu-id="5d5d6-485">这有助于保证在子树中的集合发生更改时不保留 UI 状态。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-485">This is useful to guarantee that no UI state is preserved when the collection changes within a subtree.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="5d5d6-486">何时不使用 \@key</span><span class="sxs-lookup"><span data-stu-id="5d5d6-486">When not to use \@key</span></span>

<span data-ttu-id="5d5d6-487">使用 [`@key`][5] 进行呈现时，会产生性能成本。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-487">There's a performance cost when rendering with [`@key`][5].</span></span> <span data-ttu-id="5d5d6-488">性能成本不是很大，但仅在保留元素或组件对应用有利的情况下才指定 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-488">The performance cost isn't large, but only specify [`@key`][5] if preserving the element or component benefits the app.</span></span>

<span data-ttu-id="5d5d6-489">即使未使用 [`@key`][5]，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-489">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="5d5d6-490">使用 [`@key`][5] 的唯一优点是控制如何将模型实例映射到保留的组件实例，而不是选择映射的 Blazor。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-490">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of Blazor selecting the mapping.</span></span>

### <a name="values-to-use-for-key"></a><span data-ttu-id="5d5d6-491">要用于 \@key 的值</span><span class="sxs-lookup"><span data-stu-id="5d5d6-491">Values to use for \@key</span></span>

<span data-ttu-id="5d5d6-492">通常，为 [`@key`][5] 提供以下值之一：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-492">Generally, it makes sense to supply one of the following values for [`@key`][5]:</span></span>

* <span data-ttu-id="5d5d6-493">模型对象实例。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-493">Model object instances.</span></span> <span data-ttu-id="5d5d6-494">例如，在前面的示例中使用了 `Person` 实例 (`person`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-494">For example, the `Person` instance (`person`) was used in the earlier example.</span></span> <span data-ttu-id="5d5d6-495">这可确保基于对象引用相等性的保留。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-495">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="5d5d6-496">唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-496">Unique identifiers.</span></span> <span data-ttu-id="5d5d6-497">例如，唯一标识符可以基于类型为 `int`、`string` 或 `Guid` 的主键值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-497">For example, unique identifiers can be based on primary key values of type `int`, `string`, or `Guid`.</span></span>

<span data-ttu-id="5d5d6-498">确保用于 [`@key`][5] 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-498">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="5d5d6-499">如果在同一父元素内检测到冲突值，则 Blazor 引发异常，因为它无法明确地将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-499">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="5d5d6-500">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-500">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="apply-an-attribute"></a><span data-ttu-id="5d5d6-501">应用属性</span><span class="sxs-lookup"><span data-stu-id="5d5d6-501">Apply an attribute</span></span>

<span data-ttu-id="5d5d6-502">可以通过 [`@attribute`][7] 指令将特性应用于组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-502">Attributes can be applied to components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="5d5d6-503">下面的示例将 [`[Authorize]` 特性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)应用于组件的类：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-503">The following example applies the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) to the component's class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="5d5d6-504">条件 HTML 元素属性</span><span class="sxs-lookup"><span data-stu-id="5d5d6-504">Conditional HTML element attributes</span></span>

<span data-ttu-id="5d5d6-505">HTML 元素特性属性基于 .NET 值有条件地设置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-505">HTML element attribute properties are conditionally set based on the .NET value.</span></span> <span data-ttu-id="5d5d6-506">如果值为 `false` 或 `null`，则属性未设置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-506">If the value is `false` or `null`, the property isn't set.</span></span> <span data-ttu-id="5d5d6-507">如果值为 `true`，则属性已设置。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-507">If the value is `true`, the property is set.</span></span>

<span data-ttu-id="5d5d6-508">在下面的示例中，`IsCompleted` 确定是否设置了 `<input>` 元素的 `checked` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-508">In the following example, `IsCompleted` determines if the `<input>` element's `checked` property is set.</span></span>

<span data-ttu-id="5d5d6-509">`Pages/ConditionalAttribute.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-509">`Pages/ConditionalAttribute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ConditionalAttribute.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ConditionalAttribute.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-510">有关详细信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-510">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="5d5d6-511">.NET 类型为 `bool` 时，某些 HTML 属性（如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-511">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="5d5d6-512">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-512">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="5d5d6-513">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="5d5d6-513">Raw HTML</span></span>

<span data-ttu-id="5d5d6-514">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文字文本。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-514">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="5d5d6-515">若要呈现原始 HTML，请将 HTML 内容包装在 <xref:Microsoft.AspNetCore.Components.MarkupString> 值中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-515">To render raw HTML, wrap the HTML content in a <xref:Microsoft.AspNetCore.Components.MarkupString> value.</span></span> <span data-ttu-id="5d5d6-516">将该值分析为 HTML 或 SVG，并插入到 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-516">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="5d5d6-517">呈现从任何不受信任的源构造的原始 HTML 存在安全风险，应始终避免 。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-517">Rendering raw HTML constructed from any untrusted source is a **security risk** and should **always** be avoided.</span></span>

<span data-ttu-id="5d5d6-518">下面的示例演示如何使用 <xref:Microsoft.AspNetCore.Components.MarkupString> 类型向组件的呈现输出添加静态 HTML 内容块。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-518">The following example shows using the <xref:Microsoft.AspNetCore.Components.MarkupString> type to add a block of static HTML content to the rendered output of a component.</span></span>

<span data-ttu-id="5d5d6-519">`Pages/MarkupStringExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-519">`Pages/MarkupStringExample.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/MarkupStringExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/MarkupStringExample.razor)]

::: moniker-end

## <a name="razor-templates"></a><span data-ttu-id="5d5d6-520">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="5d5d6-520">Razor templates</span></span>

<span data-ttu-id="5d5d6-521">可以使用 Razor 模板语法定义呈现片段，从而定义 UI 片段。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-521">Render fragments can be defined using Razor template syntax to define a UI snippet.</span></span> <span data-ttu-id="5d5d6-522">Razor 模板使用以下格式：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-522">Razor templates use the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="5d5d6-523">下面的示例演示如何在组件中指定 <xref:Microsoft.AspNetCore.Components.RenderFragment> 和 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 值并直接呈现模板。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-523">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="5d5d6-524">还可以将呈现片段作为参数传递给[模板化组件](xref:blazor/components/templated-components)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-524">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

<span data-ttu-id="5d5d6-525">`Pages/RazorTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="5d5d6-525">`Pages/RazorTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/RazorTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/RazorTemplate.razor)]

::: moniker-end

<span data-ttu-id="5d5d6-526">以上代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-526">Rendered output of the preceding code:</span></span>

```html
<p>The time is 4/19/2021 8:54:46 AM.</p>
<p>Pet: Nutty Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="5d5d6-527">静态资产</span><span class="sxs-lookup"><span data-stu-id="5d5d6-527">Static assets</span></span>

<span data-ttu-id="5d5d6-528">Blazor 遵循 ASP.NET Core 应用对于静态资产的约定。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-528">Blazor follows the convention of ASP.NET Core apps for static assets.</span></span> <span data-ttu-id="5d5d6-529">静态资产位于项目的 [`web root` (`wwwroot`) 文件夹](xref:fundamentals/index#web-root)中或是 `wwwroot` 文件夹下的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-529">Static assets are located in the project's [`web root` (`wwwroot`) folder](xref:fundamentals/index#web-root) or folders under the `wwwroot` folder.</span></span>

<span data-ttu-id="5d5d6-530">使用基相对路径 (`/`) 来引用静态资产的 Web 根。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-530">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="5d5d6-531">在下面的示例中，`logo.png` 实际位于 `{PROJECT ROOT}/wwwroot/images` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-531">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder.</span></span> <span data-ttu-id="5d5d6-532">`{PROJECT ROOT}` 是应用的项目根。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-532">`{PROJECT ROOT}` is the app's project root.</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="5d5d6-533">组件不支持波浪符斜杠表示法 (`~/`)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-533">Components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="5d5d6-534">有关设置应用基路径的信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-534">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="5d5d6-535">组件中不支持标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="5d5d6-535">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="5d5d6-536">组件中不支持 [`Tag Helpers`](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-536">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in components.</span></span> <span data-ttu-id="5d5d6-537">若要在 Blazor 中提供类似标记帮助程序的功能，请创建一个具有与标记帮助程序相同功能的组件，并改为使用该组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-537">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="5d5d6-538">可缩放的向量图形 (SVG) 图像</span><span class="sxs-lookup"><span data-stu-id="5d5d6-538">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="5d5d6-539">由于 Blazor 呈现 HTML，因此通过 `<img>` 标记支持浏览器支持的图像，包括可缩放的矢量图形 (SVG) 图像(`.svg`)：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-539">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="image.svg" />
```

<span data-ttu-id="5d5d6-540">同样，样式表文件 (`.css`) 的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-540">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.element-class {
    background-image: url("image.svg");
}
```

<span data-ttu-id="5d5d6-541">但是，并非在所有情况下都支持内联的 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-541">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="5d5d6-542">如果将 `<svg>` 标记直接放入 Razor 文件 (`.razor`)，则支持基本图像呈现，但很多高级场景尚不受支持。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-542">If you place an `<svg>` tag directly into a Razor file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="5d5d6-543">例如，当前未遵循 `<use>` 标记，并且 [`@bind`][10] 不能与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-543">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="5d5d6-544">有关详细信息，请参阅 [Blazor (dotnet/aspnetcore #18271) 中的 SVG 支持](https://github.com/dotnet/aspnetcore/issues/18271)。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-544">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="5d5d6-545">空白的呈现行为</span><span class="sxs-lookup"><span data-stu-id="5d5d6-545">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5d5d6-546">除非将 [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) 指令与值 `true` 一起使用，否则在以下情况下默认删除额外的空白：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-546">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="5d5d6-547">元素中的前导或尾随空白。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-547">Leading or trailing within an element.</span></span>
* <span data-ttu-id="5d5d6-548"><xref:Microsoft.AspNetCore.Components.RenderFragment>/<xref:Microsoft.AspNetCore.Components.RenderFragment%601> 参数中的前导或尾随（例如，传递到另一个组件的子内容）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-548">Leading or trailing within a <xref:Microsoft.AspNetCore.Components.RenderFragment>/<xref:Microsoft.AspNetCore.Components.RenderFragment%601> parameter (for example, child content passed to another component).</span></span>
* <span data-ttu-id="5d5d6-549">在 C# 代码块（例如 `@if` 和 `@foreach`）之前或之后。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-549">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="5d5d6-550">但是，在使用 CSS 规则（例如 `white-space: pre`）时，删除空白可能会影响呈现输出。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-550">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="5d5d6-551">若要禁用此性能优化并保留空白，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-551">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="5d5d6-552">将 `@preservewhitespace true` 指令添加到 Razor 文件 (`.razor`) 的顶部，从而将首选项应用于特定组件。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-552">Add the `@preservewhitespace true` directive at the top of the Razor file (`.razor`) to apply the preference to a specific component.</span></span>
* <span data-ttu-id="5d5d6-553">将 `@preservewhitespace true` 指令添加到 `_Imports.razor` 文件中，从而将首选项应用于子目录或整个项目。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-553">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to a subdirectory or to the entire project.</span></span>

<span data-ttu-id="5d5d6-554">在大多数情况下，不需要执行任何操作，因为应用程序通常会继续正常运行（但速度会更快）。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-554">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="5d5d6-555">如果去除空白会导致特定组件出现呈现问题，请在该组件中使用 `@preservewhitespace true` 来禁用此优化。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-555">If stripping whitespace causes a rendering problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5d5d6-556">空白将保留在组件的源标记中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-556">Whitespace is retained in a component's source markup.</span></span> <span data-ttu-id="5d5d6-557">即使在没有视觉效果的情况下，只有空白的文本也会呈现在浏览器的 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-557">Whitespace-only text renders in the browser's DOM even when there's no visual effect.</span></span>

<span data-ttu-id="5d5d6-558">请考虑以下组件标记：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-558">Consider the following component markup:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="5d5d6-559">前面的示例呈现以下不必要的空白：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-559">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="5d5d6-560">在 `@foreach` 代码块外。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-560">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="5d5d6-561">围绕 `<li>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-561">Around the `<li>` element.</span></span>
* <span data-ttu-id="5d5d6-562">围绕 `@item.Text` 输出。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-562">Around the `@item.Text` output.</span></span>

<span data-ttu-id="5d5d6-563">100 项的列表会导致超过 400 个空白区域。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-563">A list of 100 items results in over 400 areas of whitespace.</span></span> <span data-ttu-id="5d5d6-564">任何额外空白都不会在视觉上影响呈现的输出。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-564">None of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="5d5d6-565">呈现组件的静态 HTML 时，不会保留标记中的空白。</span><span class="sxs-lookup"><span data-stu-id="5d5d6-565">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="5d5d6-566">例如，查看组件 Razor 文件 (`.razor`) 中以下 `<img>` 标记的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-566">For example, view the rendered output of the following `<img>` tag in a component Razor file (`.razor`):</span></span>

```razor
<img     alt="Example image"   src="img.png"     />
```

<span data-ttu-id="5d5d6-567">不会保留前面标记中的空白：</span><span class="sxs-lookup"><span data-stu-id="5d5d6-567">Whitespace isn't preserved from the preceding markup:</span></span>

```razor
<img alt="Example image" src="img.png" />
```

::: moniker-end

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
