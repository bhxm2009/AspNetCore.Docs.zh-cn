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
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core Blazor 模板化组件

模板化组件是接受一个或多个 UI 模板作为参数的组件，然后可将其用作组件呈现逻辑的一部分。 通过模板化组件，可以创作适用面更广、比常规组件更便于重复使用的组件。 下面是一些示例：

* 表组件，用户可通过它指定表的标题、行和页脚的模板。
* 列表组件，用户可通过它指定用于呈现列表中项的模板。

通过指定一个或多个 <xref:Microsoft.AspNetCore.Components.RenderFragment> 或 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 类型的组件参数来定义模板化组件。 呈现片段，表示要呈现的 UI 段。 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 采用可在调用呈现片段时指定的类型参数。

通常，模板化组件是有类型的组件，如以下 `TableTemplate` 组件所示。 该示例中的泛型类型 `<T>` 用于呈现 `IReadOnlyList<T>` 值，在本例中，这是显示一个宠物表格的组件中的一系列宠物行。

`Shared/TableTemplate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

在使用模板化组件时，可以使用与参数名称匹配的子元素来指定模板参数。 在下面的示例中，`<TableHeader>...</TableHeader>` 和 `<RowTemplate>...<RowTemplate>` 为 `TableTemplate` 组件的 `TableHeader` 和 `RowTemplate` 提供 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 模板。

如果要为隐式子内容指定内容参数名称（不包含任何包装子元素），请在组件元素上指定 `Context` 属性。 在下面的示例中，`Context` 属性显示在 `TableTemplate` 元素上，并应用于所有 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 模板参数。

`Pages/Pets.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

或者，您可以使用 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 子元素上的 `Context` 特性更改参数名称。 在下面的示例中，`Context` 是在 `RowTemplate` 上设置的，而不是 `TableTemplate`：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

类型为 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 的组件参数具有一个名为 `context` 的隐式参数，可使用该参数。 在以下示例中，没有设置 `Context`。 `@context.{PROPERTY}` 向模板提供宠物值，其中 `{PROPERTY}` 是一个 `Pet` 属性：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

使用泛型类型组件时，将在可能的情况下推断类型参数。 但是，可以使用名称与类型参数匹配的属性（上文示例中的 `TItem`）显式指定类型：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="infer-generic-types-based-on-ancestor-components"></a>基于上级组件推断泛型类型

::: moniker range=">= aspnetcore-6.0"

上级组件可以使用 `CascadingTypeParameter` 特性将类型参数按名称级联到下级。 此特性允许泛型类型推理自动使用指定的类型参数以及具有相同名称的类型参数的下级。

例如，以下 `Chart` 组件接收股票价格数据，并将名为 `TLineData` 的泛型类型参数级联到其下级组件。

`Shared/Chart.razor`:

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

`Shared/Line.razor`:

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

使用 `Chart` 组件时，不会为图表的每个 `Line` 组件指定 `TLineData`。

`Pages/StockPriceHistory.razor`:

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
> Visual Studio Code 中的 Razor 支持尚未更新为支持此功能，因此即使项目正确生成，你也可能会收到“不正确”错误。 即将发布的工具版本中将解决此问题。

通过将 `@attribute [CascadingTypeParameter(...)]` 添加到组件中，符合以下条件的下级会自动使用指定的泛型类型参数：

* 在同一个 `.razor` 文档中嵌套为组件的子内容。
* 同时还使用完全相同的名称声明了 [`@typeparam`](xref:mvc/views/razor#typeparam)。
* 没有为类型参数提供其他值，也没有为其推断。 如果提供或推断了其他值，则其优先于级联泛型类型。

接收级联类型参数时，组件从具有 `CascadingTypeParameter` 和一个匹配名称的最接近的上级中获取参数值。 级联泛型类型参数在特定子树内被重写。

匹配只按名称进行。 因此，建议使用通用名称（例如 `T` 或 `TItem`）来避免使用级联泛型类型参数。 如果开发人员选择级联一个类型参数，则会隐式承诺其名称是唯一的，不会与来自无关组件的其他级联类型参数冲突。

::: moniker-end

::: moniker range="< aspnetcore-6.0"

> [!NOTE]
> ASP.NET Core 6.0 或更高版本中支持推断泛型类型。 有关详细信息，请参阅本文中介绍的 ASP.NET Core 5.0 以上版本的内容。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
