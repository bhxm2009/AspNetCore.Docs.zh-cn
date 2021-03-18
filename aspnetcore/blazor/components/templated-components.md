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

## <a name="generic-type-constraints"></a>泛型类型约束

> [!NOTE]
> 未来版本将支持泛型类型约束。 有关详细信息，请参阅[允许泛型类型约束 (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
