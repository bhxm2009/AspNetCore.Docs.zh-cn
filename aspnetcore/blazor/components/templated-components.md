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
ms.openlocfilehash: 6c94218f3808baca18f23a53688bafdd6354e760
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589473"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="554d1-103">ASP.NET Core Blazor 模板化组件</span><span class="sxs-lookup"><span data-stu-id="554d1-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="554d1-104">模板化组件是接受一个或多个 UI 模板作为参数的组件，然后可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="554d1-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="554d1-105">通过模板化组件，可以创作适用面更广、比常规组件更便于重复使用的组件。</span><span class="sxs-lookup"><span data-stu-id="554d1-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="554d1-106">下面是一些示例：</span><span class="sxs-lookup"><span data-stu-id="554d1-106">A couple of examples include:</span></span>

* <span data-ttu-id="554d1-107">表组件，用户可通过它指定表的标题、行和页脚的模板。</span><span class="sxs-lookup"><span data-stu-id="554d1-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="554d1-108">列表组件，用户可通过它指定用于呈现列表中项的模板。</span><span class="sxs-lookup"><span data-stu-id="554d1-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="554d1-109">通过指定一个或多个 <xref:Microsoft.AspNetCore.Components.RenderFragment> 或 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 类型的组件参数来定义模板化组件。</span><span class="sxs-lookup"><span data-stu-id="554d1-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="554d1-110">呈现片段，表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="554d1-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="554d1-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="554d1-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="554d1-112">通常，模板化组件是有类型的组件，如以下 `TableTemplate` 组件所示。</span><span class="sxs-lookup"><span data-stu-id="554d1-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="554d1-113">该示例中的泛型类型 `<T>` 用于呈现 `IReadOnlyList<T>` 值，在本例中，这是显示一个宠物表格的组件中的一系列宠物行。</span><span class="sxs-lookup"><span data-stu-id="554d1-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="554d1-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="554d1-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="554d1-115">在使用模板化组件时，可以使用与参数名称匹配的子元素来指定模板参数。</span><span class="sxs-lookup"><span data-stu-id="554d1-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="554d1-116">在下面的示例中，`<TableHeader>...</TableHeader>` 和 `<RowTemplate>...<RowTemplate>` 为 `TableTemplate` 组件的 `TableHeader` 和 `RowTemplate` 提供 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 模板。</span><span class="sxs-lookup"><span data-stu-id="554d1-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="554d1-117">如果要为隐式子内容指定内容参数名称（不包含任何包装子元素），请在组件元素上指定 `Context` 属性。</span><span class="sxs-lookup"><span data-stu-id="554d1-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="554d1-118">在下面的示例中，`Context` 属性显示在 `TableTemplate` 元素上，并应用于所有 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 模板参数。</span><span class="sxs-lookup"><span data-stu-id="554d1-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="554d1-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="554d1-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="554d1-120">或者，您可以使用 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 子元素上的 `Context` 特性更改参数名称。</span><span class="sxs-lookup"><span data-stu-id="554d1-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="554d1-121">在下面的示例中，`Context` 是在 `RowTemplate` 上设置的，而不是 `TableTemplate`：</span><span class="sxs-lookup"><span data-stu-id="554d1-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="554d1-122">类型为 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 的组件参数具有一个名为 `context` 的隐式参数，可使用该参数。</span><span class="sxs-lookup"><span data-stu-id="554d1-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="554d1-123">在以下示例中，没有设置 `Context`。</span><span class="sxs-lookup"><span data-stu-id="554d1-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="554d1-124">`@context.{PROPERTY}` 向模板提供宠物值，其中 `{PROPERTY}` 是一个 `Pet` 属性：</span><span class="sxs-lookup"><span data-stu-id="554d1-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="554d1-125">使用泛型类型组件时，将在可能的情况下推断类型参数。</span><span class="sxs-lookup"><span data-stu-id="554d1-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="554d1-126">但是，可以使用名称与类型参数匹配的属性（上文示例中的 `TItem`）显式指定类型：</span><span class="sxs-lookup"><span data-stu-id="554d1-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a><span data-ttu-id="554d1-127">泛型类型约束</span><span class="sxs-lookup"><span data-stu-id="554d1-127">Generic type constraints</span></span>

> [!NOTE]
> <span data-ttu-id="554d1-128">未来版本将支持泛型类型约束。</span><span class="sxs-lookup"><span data-stu-id="554d1-128">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="554d1-129">有关详细信息，请参阅[允许泛型类型约束 (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)。</span><span class="sxs-lookup"><span data-stu-id="554d1-129">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="554d1-130">其他资源</span><span class="sxs-lookup"><span data-stu-id="554d1-130">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
