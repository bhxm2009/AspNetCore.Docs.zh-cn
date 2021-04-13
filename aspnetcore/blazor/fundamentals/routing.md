---
title: ASP.NET Core Blazor 路由
author: guardrex
description: 了解如何管理应用中的请求路由，以及如何在 Blazor 应用中使用 NavLink 组件进行导航。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: cfc540e00593c9a886b342cfe5998498b6500c31
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081411"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="3faad-103">ASP.NET Core Blazor 路由</span><span class="sxs-lookup"><span data-stu-id="3faad-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="3faad-104">在本文中，学习如何管理请求路由以及如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件在 Blazor 应用中创建导航链接。</span><span class="sxs-lookup"><span data-stu-id="3faad-104">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="3faad-105">路由模板</span><span class="sxs-lookup"><span data-stu-id="3faad-105">Route templates</span></span>

<span data-ttu-id="3faad-106">通过 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件可在 Blazor 应用中路由到 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="3faad-106">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="3faad-107"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件在 Blazor 应用的 `App` 组件中使用。</span><span class="sxs-lookup"><span data-stu-id="3faad-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="3faad-108">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-108">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

<span data-ttu-id="3faad-109">编译带有 [`@page` 指令](xref:mvc/views/razor#page)的 Razor 组件 (`.razor`) 时，将为生成的组件类提供一个 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 来指定组件的路由模板。</span><span class="sxs-lookup"><span data-stu-id="3faad-109">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="3faad-110">当应用启动时，将扫描指定为 路由器的 `AppAssembly` 的程序集，来收集具有 <xref:Microsoft.AspNetCore.Components.RouteAttribute> 的应用组件的路由信息。</span><span class="sxs-lookup"><span data-stu-id="3faad-110">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="3faad-111">在运行时，<xref:Microsoft.AspNetCore.Components.RouteView> 组件：</span><span class="sxs-lookup"><span data-stu-id="3faad-111">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="3faad-112">从 <xref:Microsoft.AspNetCore.Components.Routing.Router> 接收 <xref:Microsoft.AspNetCore.Components.RouteData> 以及所有路由参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-112">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="3faad-113">使用指定的组件的[布局](xref:blazor/layouts)来呈现该组件，包括任何后续嵌套布局。</span><span class="sxs-lookup"><span data-stu-id="3faad-113">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="3faad-114">对于没有使用 [`@layout` 指令](xref:blazor/layouts#apply-a-layout-to-a-component)指定布局的组件，可选择使用布局类指定一个 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-114">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#apply-a-layout-to-a-component).</span></span> <span data-ttu-id="3faad-115">框架的 [Blazor 项目模板](xref:blazor/project-structure)会指定 `MainLayout` 组件 (`Shared/MainLayout.razor`) 作为应用的默认布局。</span><span class="sxs-lookup"><span data-stu-id="3faad-115">The framework's [Blazor project templates](xref:blazor/project-structure) specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="3faad-116">有关布局的详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="3faad-116">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="3faad-117">组件支持使用多个 [`@page` 指令](xref:mvc/views/razor#page)的多个路由模板。</span><span class="sxs-lookup"><span data-stu-id="3faad-117">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="3faad-118">以下示例组件会对 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求进行加载。</span><span class="sxs-lookup"><span data-stu-id="3faad-118">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="3faad-119">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-119">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="3faad-120">若要正确解析 URL，应用必须在其 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中加入 `<base>` 标记，并在 `href` 属性中指定应用基路径。</span><span class="sxs-lookup"><span data-stu-id="3faad-120">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="3faad-121">有关详细信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="3faad-121">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="3faad-122">在找不到内容时提供自定义内容</span><span class="sxs-lookup"><span data-stu-id="3faad-122">Provide custom content when content isn't found</span></span>

<span data-ttu-id="3faad-123">如果找不到所请求路由的内容，则 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件允许应用指定自定义内容。</span><span class="sxs-lookup"><span data-stu-id="3faad-123">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="3faad-124">在 `App` 组件中，在 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件的 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 模板中设置自定义内容。</span><span class="sxs-lookup"><span data-stu-id="3faad-124">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="3faad-125">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-125">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="3faad-126">任意项都可用作 `<NotFound>` 标记的内容，例如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="3faad-126">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="3faad-127">若要将默认布局应用于 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 内容，请参阅 <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component>。</span><span class="sxs-lookup"><span data-stu-id="3faad-127">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#apply-a-layout-to-arbitrary-content-layoutview-component>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="3faad-128">从多个程序集路由到组件</span><span class="sxs-lookup"><span data-stu-id="3faad-128">Route to components from multiple assemblies</span></span>

<span data-ttu-id="3faad-129">使用 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 参数为 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件指定搜索可路由组件时要考虑的其他程序集。</span><span class="sxs-lookup"><span data-stu-id="3faad-129">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="3faad-130">除了指定至 `AppAssembly` 的程序集外，还会扫描其他程序集。</span><span class="sxs-lookup"><span data-stu-id="3faad-130">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="3faad-131">在以下示例中，`Component1` 是在引用的[组件类库](xref:blazor/components/class-libraries)中定义的可路由组件。</span><span class="sxs-lookup"><span data-stu-id="3faad-131">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="3faad-132">以下 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 示例为 `Component1` 提供路由支持。</span><span class="sxs-lookup"><span data-stu-id="3faad-132">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="3faad-133">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-133">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="3faad-134">路由参数</span><span class="sxs-lookup"><span data-stu-id="3faad-134">Route parameters</span></span>

<span data-ttu-id="3faad-135">路由器使用路由参数以相同的名称填充相应的[组件参数](xref:blazor/components/index#component-parameters)。</span><span class="sxs-lookup"><span data-stu-id="3faad-135">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="3faad-136">路由参数名不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3faad-136">Route parameter names are case insensitive.</span></span> <span data-ttu-id="3faad-137">在下面的示例中，`text` 参数将路由段的值赋给组件的 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="3faad-137">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="3faad-138">对 `/RouteParameter/amazing`发出请求时，`<h1>` 标记内容呈现为 `Blazor is amazing!`。</span><span class="sxs-lookup"><span data-stu-id="3faad-138">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="3faad-139">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-139">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3faad-140">支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-140">Optional parameters are supported.</span></span> <span data-ttu-id="3faad-141">在下面的示例中，`text` 可选参数将 route 段的值赋给组件的 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="3faad-141">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="3faad-142">如果该段不存在，则将 `Text` 的值设置为 `fantastic`。</span><span class="sxs-lookup"><span data-stu-id="3faad-142">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="3faad-143">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-143">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3faad-144">不支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-144">Optional parameters aren't supported.</span></span> <span data-ttu-id="3faad-145">在下述示例中，应用了两个 [`@page` 指令](xref:mvc/views/razor#page)。</span><span class="sxs-lookup"><span data-stu-id="3faad-145">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="3faad-146">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="3faad-146">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="3faad-147">第二个指令将 `{text}` 路由参数分配给组件的 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="3faad-147">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="3faad-148">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-148">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="3faad-149">使用 [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set-onparameterssetasync) 而不是 [`OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)，以允许应用使用不同的可选参数值导航到同一组件。</span><span class="sxs-lookup"><span data-stu-id="3faad-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set-onparameterssetasync) instead of [`OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-oninitializedasync) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="3faad-150">根据上述示例，当用户应该能够从 `/RouteParameter` 导航到 `/RouteParameter/amazing` 或从 `/RouteParameter/amazing` 导航到 `/RouteParameter` 时使用 `OnParametersSet`：</span><span class="sxs-lookup"><span data-stu-id="3faad-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="3faad-151">路由约束</span><span class="sxs-lookup"><span data-stu-id="3faad-151">Route constraints</span></span>

<span data-ttu-id="3faad-152">路由约束强制在路由段和组件之间进行类型匹配。</span><span class="sxs-lookup"><span data-stu-id="3faad-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="3faad-153">在以下示例中，到 `User` 组件的路由仅在以下情况下匹配：</span><span class="sxs-lookup"><span data-stu-id="3faad-153">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="3faad-154">请求 URL 中存在 `Id` 路由段。</span><span class="sxs-lookup"><span data-stu-id="3faad-154">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="3faad-155">`Id` 段是一个整数 (`int`) 类型。</span><span class="sxs-lookup"><span data-stu-id="3faad-155">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="3faad-156">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-156">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="3faad-157">下表中显示的路由约束可用。</span><span class="sxs-lookup"><span data-stu-id="3faad-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="3faad-158">有关与固定区域性匹配的路由约束，请参阅表下方的警告了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="3faad-158">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="3faad-159">约束</span><span class="sxs-lookup"><span data-stu-id="3faad-159">Constraint</span></span> | <span data-ttu-id="3faad-160">示例</span><span class="sxs-lookup"><span data-stu-id="3faad-160">Example</span></span>           | <span data-ttu-id="3faad-161">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="3faad-161">Example Matches</span></span>                                                                  | <span data-ttu-id="3faad-162">固定条件</span><span class="sxs-lookup"><span data-stu-id="3faad-162">Invariant</span></span><br><span data-ttu-id="3faad-163">区域性</span><span class="sxs-lookup"><span data-stu-id="3faad-163">culture</span></span><br><span data-ttu-id="3faad-164">匹配</span><span class="sxs-lookup"><span data-stu-id="3faad-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="3faad-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="3faad-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="3faad-166">否</span><span class="sxs-lookup"><span data-stu-id="3faad-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="3faad-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="3faad-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="3faad-168">是</span><span class="sxs-lookup"><span data-stu-id="3faad-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="3faad-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="3faad-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="3faad-170">是</span><span class="sxs-lookup"><span data-stu-id="3faad-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="3faad-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="3faad-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="3faad-172">是</span><span class="sxs-lookup"><span data-stu-id="3faad-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="3faad-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="3faad-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="3faad-174">是</span><span class="sxs-lookup"><span data-stu-id="3faad-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="3faad-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="3faad-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="3faad-176">否</span><span class="sxs-lookup"><span data-stu-id="3faad-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="3faad-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="3faad-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="3faad-178">是</span><span class="sxs-lookup"><span data-stu-id="3faad-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="3faad-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="3faad-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="3faad-180">是</span><span class="sxs-lookup"><span data-stu-id="3faad-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="3faad-181">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 <xref:System.DateTime>）。</span><span class="sxs-lookup"><span data-stu-id="3faad-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="3faad-182">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="3faad-182">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="3faad-183">使用包含点的 URL 进行路由</span><span class="sxs-lookup"><span data-stu-id="3faad-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="3faad-184">对于托管的 Blazor WebAssembly 和 Blazor Server应用，服务器端默认路由模板假定如果请求 URL 的最后一段包含一个点 (`.`)，则请求一个文件。</span><span class="sxs-lookup"><span data-stu-id="3faad-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="3faad-185">例如，URL `https://localhost.com:5001/example/some.thing` 由路由器解释为名为 `some.thing` 的文件的请求。</span><span class="sxs-lookup"><span data-stu-id="3faad-185">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="3faad-186">在没有额外配置的情况下，如果 `some.thing` 是指通过 [`@page` 指令](xref:mvc/views/razor#page)路由到一个组件，且 `some.thing` 是一个路由参数值，那么应用将返回“404 - 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="3faad-186">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="3faad-187">若要使用具有包含点的一个或多个参数的路由，则应用必须使用自定义模板配置该路由。</span><span class="sxs-lookup"><span data-stu-id="3faad-187">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="3faad-188">请考虑下面的 `Example` 组件，它可以从 URL 的最后一段接收路由参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-188">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="3faad-189">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-189">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="3faad-190">若要允许托管的 Blazor WebAssembly 解决方案的 `Server` 应用在 `param` 路由参数中使用一个点来路由请求，请添加一个回退文件路由模板，在该模板的 `Startup.Configure` 中包含该可选参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-190">To permit the **`Server`** app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="3faad-191">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="3faad-191">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="3faad-192">若要配置 Blazor Server应用，使其在 `param` 路由参数中使用一个点来路由请求，请添加一个回退页面路由模板，该模板具有`Startup.Configure` 中的可选参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-192">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="3faad-193">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="3faad-193">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="3faad-194">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="3faad-194">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="3faad-195">catch-all 路由参数</span><span class="sxs-lookup"><span data-stu-id="3faad-195">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3faad-196">组件支持可跨多个文件夹边界捕获路径的 catch-all 路由参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-196">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="3faad-197">Catch-all 路由参数是：</span><span class="sxs-lookup"><span data-stu-id="3faad-197">Catch-all route parameters are:</span></span>

* <span data-ttu-id="3faad-198">以与路由段名称匹配的方式命名。</span><span class="sxs-lookup"><span data-stu-id="3faad-198">Named to match the route segment name.</span></span> <span data-ttu-id="3faad-199">命名不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3faad-199">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="3faad-200">`string` 类型。</span><span class="sxs-lookup"><span data-stu-id="3faad-200">A `string` type.</span></span> <span data-ttu-id="3faad-201">框架不提供自动强制转换。</span><span class="sxs-lookup"><span data-stu-id="3faad-201">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="3faad-202">位于 URL 的末尾。</span><span class="sxs-lookup"><span data-stu-id="3faad-202">At the end of the URL.</span></span>

<span data-ttu-id="3faad-203">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-203">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

<span data-ttu-id="3faad-204">对于具有 `/catch-all/{*pageRoute}` 路由模板的 URL `/catch-all/this/is/a/test`，`PageRoute` 的值设置为 `this/is/a/test`。</span><span class="sxs-lookup"><span data-stu-id="3faad-204">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="3faad-205">对捕获路径的斜杠和段进行解码。</span><span class="sxs-lookup"><span data-stu-id="3faad-205">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="3faad-206">对于 `/catch-all/{*pageRoute}` 的路由模板，URL `/catch-all/this/is/a%2Ftest%2A` 会生成 `this/is/a/test*`。</span><span class="sxs-lookup"><span data-stu-id="3faad-206">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3faad-207">ASP.NET Core 5.0 或更高版本中支持 catch-all 路由参数。</span><span class="sxs-lookup"><span data-stu-id="3faad-207">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="3faad-208">有关详细信息，请选择本文的 5.0 版本。</span><span class="sxs-lookup"><span data-stu-id="3faad-208">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="3faad-209">URI 和导航状态帮助程序</span><span class="sxs-lookup"><span data-stu-id="3faad-209">URI and navigation state helpers</span></span>

<span data-ttu-id="3faad-210">在 C# 代码中使用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 来来管理 URI 和导航。</span><span class="sxs-lookup"><span data-stu-id="3faad-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="3faad-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> 提供下表所示的事件和方法。</span><span class="sxs-lookup"><span data-stu-id="3faad-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="3faad-212">成员</span><span class="sxs-lookup"><span data-stu-id="3faad-212">Member</span></span> | <span data-ttu-id="3faad-213">描述</span><span class="sxs-lookup"><span data-stu-id="3faad-213">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="3faad-214">获取当前绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="3faad-214">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="3faad-215">获取可在相对 URI 路径之前添加用于生成绝对 URI 的基 URI（带有尾部反斜杠）。</span><span class="sxs-lookup"><span data-stu-id="3faad-215">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="3faad-216">通常，<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 对应于 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 中文档的 `<base>` 元素上的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="3faad-216">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="3faad-217">导航到指定 URI。</span><span class="sxs-lookup"><span data-stu-id="3faad-217">Navigates to the specified URI.</span></span> <span data-ttu-id="3faad-218">如果 `forceLoad` 为 `true`，则：</span><span class="sxs-lookup"><span data-stu-id="3faad-218">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="3faad-219">客户端路由会被绕过。</span><span class="sxs-lookup"><span data-stu-id="3faad-219">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="3faad-220">无论 URI 是否通常由客户端路由器处理，浏览器都必须从服务器加载新页面。</span><span class="sxs-lookup"><span data-stu-id="3faad-220">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="3faad-221">导航位置更改时触发的事件。</span><span class="sxs-lookup"><span data-stu-id="3faad-221">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="3faad-222">将相对 URI 转换为绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="3faad-222">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="3faad-223">给定基 URI（例如，之前由 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> 返回的 URI），将绝对 URI 转换为相对于基 URI 前缀的 URI。</span><span class="sxs-lookup"><span data-stu-id="3faad-223">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="3faad-224">对于 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> 事件，<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> 提供了下述导航事件信息：</span><span class="sxs-lookup"><span data-stu-id="3faad-224">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="3faad-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>：新位置的 URL。</span><span class="sxs-lookup"><span data-stu-id="3faad-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="3faad-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>：如果为 `true`，则 Blazor 拦截了浏览器中的导航。</span><span class="sxs-lookup"><span data-stu-id="3faad-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="3faad-227">如果为 `false`，则 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 导致了导航发生。</span><span class="sxs-lookup"><span data-stu-id="3faad-227">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="3faad-228">以下组件：</span><span class="sxs-lookup"><span data-stu-id="3faad-228">The following component:</span></span>

* <span data-ttu-id="3faad-229">使用 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> 选择按钮后，导航到应用的 `Counter` 组件 (`Pages/Counter.razor`)。</span><span class="sxs-lookup"><span data-stu-id="3faad-229">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="3faad-230">通过订阅 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> 来处理位置更改事件。</span><span class="sxs-lookup"><span data-stu-id="3faad-230">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="3faad-231">在框架调用 `Dispose` 时，解除挂接 `HandleLocationChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="3faad-231">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="3faad-232">解除挂接该方法可允许组件进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="3faad-232">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="3faad-233">选择该按钮时，记录器实现会记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="3faad-233">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="3faad-234">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="3faad-234">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

<span data-ttu-id="3faad-235">要详细了解组件处置，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="3faad-235">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="3faad-236">查询字符串和分析参数</span><span class="sxs-lookup"><span data-stu-id="3faad-236">Query string and parse parameters</span></span>

<span data-ttu-id="3faad-237">可从 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> 属性中获取请求的查询字符串：</span><span class="sxs-lookup"><span data-stu-id="3faad-237">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="3faad-238">若要分析查询字符串的参数，一种方法是结合使用 [`URLSearchParams`](https://developer.mozilla.org/docs/Web/API/URLSearchParams) 和 [JavaScript (JS) 互操作](xref:blazor/call-javascript-from-dotnet)：</span><span class="sxs-lookup"><span data-stu-id="3faad-238">To parse a query string's parameters, one approach is to use [`URLSearchParams`](https://developer.mozilla.org/docs/Web/API/URLSearchParams) with [JavaScript (JS) interop](xref:blazor/call-javascript-from-dotnet):</span></span>

```javascript
export createQueryString = (string queryString) => new URLSearchParams(queryString);
```

<span data-ttu-id="3faad-239">有关详细信息，请参阅 [Blazor JavaScript 隔离和对象引用](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="3faad-239">For more information, see [Blazor JavaScript isolation and object references](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references).</span></span>

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="3faad-240">`NavLink` 和 `NavMenu` 组件</span><span class="sxs-lookup"><span data-stu-id="3faad-240">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="3faad-241">创建导航链接时，请使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件代替 HTML 超链接元素 (`<a>`)。</span><span class="sxs-lookup"><span data-stu-id="3faad-241">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="3faad-242"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件的行为方式类似于 `<a>` 元素，但它根据其 `href` 是否与当前 URL 匹配来切换 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="3faad-242">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="3faad-243">`active` 类可帮助用户了解所显示导航链接中的哪个页面是活动页面。</span><span class="sxs-lookup"><span data-stu-id="3faad-243">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="3faad-244">也可以选择将 CSS 类名分配到 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>，以便在当前路由与 `href` 匹配时将自定义 CSS 类应用到呈现的链接。</span><span class="sxs-lookup"><span data-stu-id="3faad-244">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="3faad-245">以下 `NavMenu` 组件创建 [`Bootstrap`](https://getbootstrap.com/docs/) 导航栏，该导航栏演示如何使用 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件：</span><span class="sxs-lookup"><span data-stu-id="3faad-245">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3faad-246">`NavMenu` 组件 (`NavMenu.razor`) 将在 [Blazor 项目模板](xref:blazor/project-structure)生成的应用的 `Shared` 文件夹中提供。</span><span class="sxs-lookup"><span data-stu-id="3faad-246">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="3faad-247">有两个 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 选项可分配给 `<NavLink>` 元素的 `Match` 属性：</span><span class="sxs-lookup"><span data-stu-id="3faad-247">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="3faad-248"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前整个 URL 匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="3faad-248"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="3faad-249"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>（默认）：<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 在与当前 URL 的任何前缀匹配的情况下处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="3faad-249"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="3faad-250">在前面的示例中，主页 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` 与主页 URL 匹配，并且仅在应用的默认基路径 URL（例如，`https://localhost:5001/`）处接收 `active` CSS 类。</span><span class="sxs-lookup"><span data-stu-id="3faad-250">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="3faad-251">当用户访问带有 `component` 前缀的任何 URL（例如，`https://localhost:5001/component` 和 `https://localhost:5001/component/another-segment`）时，第二个 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 接收 `active` 类。</span><span class="sxs-lookup"><span data-stu-id="3faad-251">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="3faad-252">其他 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件属性会传递到呈现的定位标记。</span><span class="sxs-lookup"><span data-stu-id="3faad-252">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="3faad-253">在以下示例中，<xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件包括 `target` 属性：</span><span class="sxs-lookup"><span data-stu-id="3faad-253">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="3faad-254">呈现以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="3faad-254">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="3faad-255">由于 Blazor 呈现子内容的方式，如果在 `NavLink`（子）组件的内容中使用递增循环变量，则在 `for` 循环内呈现 `NavLink` 组件需要本地索引变量：</span><span class="sxs-lookup"><span data-stu-id="3faad-255">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="3faad-256">在[子内容](xref:blazor/components/index#child-content)中使用循环变量的任何子组件（而不仅仅是 `NavLink` 组件）都要求必须在此方案中使用索引变量。</span><span class="sxs-lookup"><span data-stu-id="3faad-256">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="3faad-257">或者，将 `foreach` 循环用于 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="3faad-257">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="3faad-258">ASP.NET Core 终结点路由集成</span><span class="sxs-lookup"><span data-stu-id="3faad-258">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="3faad-259">*本部分仅适用于 Blazor Server应用。*</span><span class="sxs-lookup"><span data-stu-id="3faad-259">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="3faad-260">Blazor Server 已集成到 [ASP.NET Core 终结点路由](xref:fundamentals/routing)中。</span><span class="sxs-lookup"><span data-stu-id="3faad-260">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="3faad-261">ASP.NET Core 应用配置为接受 `Startup.Configure` 中带有 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 的交互式组件的传入连接。</span><span class="sxs-lookup"><span data-stu-id="3faad-261">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="3faad-262">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="3faad-262">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

<span data-ttu-id="3faad-263">典型的配置是将所有请求路由到 Razor 页面，该页面充当 Blazor Server应用的服务器端部分的主机。</span><span class="sxs-lookup"><span data-stu-id="3faad-263">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="3faad-264">按照约定，主机页面通常在应用的 `Pages` 文件夹中被命名为 `_Host.cshtml`。</span><span class="sxs-lookup"><span data-stu-id="3faad-264">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="3faad-265">主机文件中指定的路由称为 *回退路由*，因为它在路由匹配中以较低的优先级运行。</span><span class="sxs-lookup"><span data-stu-id="3faad-265">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="3faad-266">其他路由不匹配时，会使用回退路由。</span><span class="sxs-lookup"><span data-stu-id="3faad-266">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="3faad-267">这让应用能够使用其他控制器和页面，而不会干扰 Blazor Server应用中的组件路由。</span><span class="sxs-lookup"><span data-stu-id="3faad-267">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="3faad-268">若要了解如何为非根 URL 服务器托管配置 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A>，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="3faad-268">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
