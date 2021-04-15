---
title: ASP.NET Core Blazor 模板化组件
author: guardrex
description: 了解模板化组件如何接受一个或多个 UI 模板作为参数，然后将其用作组件呈现逻辑的一部分。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2021
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
uid: blazor/components/templated-components
ms.openlocfilehash: d58bd87ba2b39ad7e6d57560a200c69e7937ec0d
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957725"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="0bf89-103">ASP.NET Core Blazor 模板化组件</span><span class="sxs-lookup"><span data-stu-id="0bf89-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="0bf89-104">模板化组件是接受一个或多个 UI 模板作为参数的组件，然后可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="0bf89-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="0bf89-105">通过模板化组件，可以创作适用面更广、比常规组件更便于重复使用的组件。</span><span class="sxs-lookup"><span data-stu-id="0bf89-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="0bf89-106">下面是一些示例：</span><span class="sxs-lookup"><span data-stu-id="0bf89-106">A couple of examples include:</span></span>

* <span data-ttu-id="0bf89-107">表组件，用户可通过它指定表的标题、行和页脚的模板。</span><span class="sxs-lookup"><span data-stu-id="0bf89-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="0bf89-108">列表组件，用户可通过它指定用于呈现列表中项的模板。</span><span class="sxs-lookup"><span data-stu-id="0bf89-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="0bf89-109">通过指定一个或多个 <xref:Microsoft.AspNetCore.Components.RenderFragment> 或 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 类型的组件参数来定义模板化组件。</span><span class="sxs-lookup"><span data-stu-id="0bf89-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="0bf89-110">呈现片段，表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="0bf89-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="0bf89-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="0bf89-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="0bf89-112">通常，模板化组件是有类型的组件，如以下 `TableTemplate` 组件所示。</span><span class="sxs-lookup"><span data-stu-id="0bf89-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="0bf89-113">该示例中的泛型类型 `<T>` 用于呈现 `IReadOnlyList<T>` 值，在本例中，这是显示一个宠物表格的组件中的一系列宠物行。</span><span class="sxs-lookup"><span data-stu-id="0bf89-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="0bf89-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="0bf89-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="0bf89-115">在使用模板化组件时，可以使用与参数名称匹配的子元素来指定模板参数。</span><span class="sxs-lookup"><span data-stu-id="0bf89-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="0bf89-116">在下面的示例中，`<TableHeader>...</TableHeader>` 和 `<RowTemplate>...<RowTemplate>` 为 `TableTemplate` 组件的 `TableHeader` 和 `RowTemplate` 提供 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 模板。</span><span class="sxs-lookup"><span data-stu-id="0bf89-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="0bf89-117">如果要为隐式子内容指定内容参数名称（不包含任何包装子元素），请在组件元素上指定 `Context` 属性。</span><span class="sxs-lookup"><span data-stu-id="0bf89-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="0bf89-118">在下面的示例中，`Context` 属性显示在 `TableTemplate` 元素上，并应用于所有 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 模板参数。</span><span class="sxs-lookup"><span data-stu-id="0bf89-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="0bf89-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="0bf89-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="0bf89-120">或者，您可以使用 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 子元素上的 `Context` 特性更改参数名称。</span><span class="sxs-lookup"><span data-stu-id="0bf89-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="0bf89-121">在下面的示例中，`Context` 是在 `RowTemplate` 上设置的，而不是 `TableTemplate`：</span><span class="sxs-lookup"><span data-stu-id="0bf89-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="0bf89-122">类型为 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 的组件参数具有一个名为 `context` 的隐式参数，可使用该参数。</span><span class="sxs-lookup"><span data-stu-id="0bf89-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="0bf89-123">在以下示例中，没有设置 `Context`。</span><span class="sxs-lookup"><span data-stu-id="0bf89-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="0bf89-124">`@context.{PROPERTY}` 向模板提供宠物值，其中 `{PROPERTY}` 是一个 `Pet` 属性：</span><span class="sxs-lookup"><span data-stu-id="0bf89-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="0bf89-125">使用泛型类型组件时，将在可能的情况下推断类型参数。</span><span class="sxs-lookup"><span data-stu-id="0bf89-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="0bf89-126">但是，可以使用名称与类型参数匹配的属性（上文示例中的 `TItem`）显式指定类型：</span><span class="sxs-lookup"><span data-stu-id="0bf89-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="infer-generic-types-based-on-ancestor-components"></a><span data-ttu-id="0bf89-127">基于上级组件推断泛型类型</span><span class="sxs-lookup"><span data-stu-id="0bf89-127">Infer generic types based on ancestor components</span></span>

::: moniker range=">= aspnetcore-6.0"

<span data-ttu-id="0bf89-128">上级组件可以使用 `CascadingTypeParameter` 特性将类型参数按名称级联到下级。</span><span class="sxs-lookup"><span data-stu-id="0bf89-128">An ancestor component can cascade a type parameter by name to descendants using the `CascadingTypeParameter` attribute.</span></span> <span data-ttu-id="0bf89-129">此特性允许泛型类型推理自动使用指定的类型参数以及具有相同名称的类型参数的下级。</span><span class="sxs-lookup"><span data-stu-id="0bf89-129">This attribute allows a generic type inference to use the specified type parameter automatically with descendants that have a type parameter with the same name.</span></span>

<span data-ttu-id="0bf89-130">例如，以下 `Chart` 组件接收股票价格数据，并将名为 `TLineData` 的泛型类型参数级联到其下级组件。</span><span class="sxs-lookup"><span data-stu-id="0bf89-130">For example, the following `Chart` component receives stock price data and cascades a generic type parameter named `TLineData` to its descendent components.</span></span>

<span data-ttu-id="0bf89-131">`Shared/Chart.razor`:</span><span class="sxs-lookup"><span data-stu-id="0bf89-131">`Shared/Chart.razor`:</span></span>

```razor
@typeparam TLineData
@attribute [CascadingTypeParameter(nameof(TLineData))]

...

@code {
    [Parameter]
    public IEnumerable<TLineData> Data { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }
}
```

<span data-ttu-id="0bf89-132">`Shared/Line.razor`:</span><span class="sxs-lookup"><span data-stu-id="0bf89-132">`Shared/Line.razor`:</span></span>

```razor
@typeparam TLineData

...

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public decimal Value { get; set; }

    [Parameter]
    public IEnumerable<TLineData> Data { get; set; }
}
```

<span data-ttu-id="0bf89-133">使用 `Chart` 组件时，不会为图表的每个 `Line` 组件指定 `TLineData`。</span><span class="sxs-lookup"><span data-stu-id="0bf89-133">When the `Chart` component is used, `TLineData` isn't specified for each `Line` component of the chart.</span></span>

<span data-ttu-id="0bf89-134">`Pages/StockPriceHistory.razor`:</span><span class="sxs-lookup"><span data-stu-id="0bf89-134">`Pages/StockPriceHistory.razor`:</span></span>

```razor
@page "/stock-price-history"

<Chart Data="stockPriceHistory.GroupBy(x => x.Date)">
    <Line Title="Open" Value="day => day.Values.First()" />
    <Line Title="High" Value="day => day.Values.Max()" />
    <Line Title="Low" Value="day => day.Values.Min()" />
    <Line Title="Close" Value="day => day.Values.Last()" />
</Chart>
```

> [!NOTE]
> <span data-ttu-id="0bf89-135">Visual Studio Code 中的 Razor 支持尚未更新为支持此功能，因此即使项目正确生成，你也可能会收到“不正确”错误。</span><span class="sxs-lookup"><span data-stu-id="0bf89-135">The Razor support in Visual Studio Code hasn't been updated to support this feature, so you may receive incorrect errors even though the project correctly builds.</span></span> <span data-ttu-id="0bf89-136">即将发布的工具版本中将解决此问题。</span><span class="sxs-lookup"><span data-stu-id="0bf89-136">This will be addressed in an upcoming tooling release.</span></span>

<span data-ttu-id="0bf89-137">通过将 `@attribute [CascadingTypeParameter(...)]` 添加到组件中，符合以下条件的下级会自动使用指定的泛型类型参数：</span><span class="sxs-lookup"><span data-stu-id="0bf89-137">By adding `@attribute [CascadingTypeParameter(...)]` to a component, the specified generic type argument is automatically used by descendants that:</span></span>

* <span data-ttu-id="0bf89-138">在同一个 `.razor` 文档中嵌套为组件的子内容。</span><span class="sxs-lookup"><span data-stu-id="0bf89-138">Are nested as child content for the component in the same `.razor` document.</span></span>
* <span data-ttu-id="0bf89-139">同时还使用完全相同的名称声明了 [`@typeparam`](xref:mvc/views/razor#typeparam)。</span><span class="sxs-lookup"><span data-stu-id="0bf89-139">Also declare a [`@typeparam`](xref:mvc/views/razor#typeparam) with the exact same name.</span></span>
* <span data-ttu-id="0bf89-140">没有为类型参数提供其他值，也没有为其推断。</span><span class="sxs-lookup"><span data-stu-id="0bf89-140">Don't have another value supplied or inferred for the type parameter.</span></span> <span data-ttu-id="0bf89-141">如果提供或推断了其他值，则其优先于级联泛型类型。</span><span class="sxs-lookup"><span data-stu-id="0bf89-141">If another value is supplied or inferred, it takes precedence over the cascaded generic type.</span></span>

<span data-ttu-id="0bf89-142">接收级联类型参数时，组件从具有 `CascadingTypeParameter` 和一个匹配名称的最接近的上级中获取参数值。</span><span class="sxs-lookup"><span data-stu-id="0bf89-142">When receiving a cascaded type parameter, components obtain the parameter value from the closest ancestor that has a `CascadingTypeParameter` with a matching name.</span></span> <span data-ttu-id="0bf89-143">级联泛型类型参数在特定子树内被重写。</span><span class="sxs-lookup"><span data-stu-id="0bf89-143">Cascaded generic type parameters are overridden within a particular subtree.</span></span>

<span data-ttu-id="0bf89-144">匹配只按名称进行。</span><span class="sxs-lookup"><span data-stu-id="0bf89-144">Matching is only performed by name.</span></span> <span data-ttu-id="0bf89-145">因此，建议使用通用名称（例如 `T` 或 `TItem`）来避免使用级联泛型类型参数。</span><span class="sxs-lookup"><span data-stu-id="0bf89-145">Therefore, we recommend avoiding a cascaded generic type parameter with a generic name, for example `T` or `TItem`.</span></span> <span data-ttu-id="0bf89-146">如果开发人员选择级联一个类型参数，则会隐式承诺其名称是唯一的，不会与来自无关组件的其他级联类型参数冲突。</span><span class="sxs-lookup"><span data-stu-id="0bf89-146">If a developer opts into cascading a type parameter, they're implicitly promising that its name is unique enough not to clash with other cascaded type parameters from unrelated components.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-6.0"

> [!NOTE]
> <span data-ttu-id="0bf89-147">ASP.NET Core 6.0 或更高版本中支持推断泛型类型。</span><span class="sxs-lookup"><span data-stu-id="0bf89-147">Inferred generic types are supported in ASP.NET Core 6.0 or later.</span></span> <span data-ttu-id="0bf89-148">有关详细信息，请参阅本文中介绍的 ASP.NET Core 5.0 以上版本的内容。</span><span class="sxs-lookup"><span data-stu-id="0bf89-148">For more information, see a version of this article later than ASP.NET Core 5.0.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0bf89-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="0bf89-149">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
