---
title: ASP.NET Core Blazor 数据绑定
author: guardrex
description: 了解 Blazor 应用中组件和数据对象模型 (DOM) 元素的数据绑定功能。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/15/2021
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
uid: blazor/components/data-binding
ms.openlocfilehash: 6e2d295289d268ef306cae53a6fbdbaac309cb87
ms.sourcegitcommit: 7923a9ec594690f01e0c9c6df3416c239e6745fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081541"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor 数据绑定

Razor 组件使用 [`@bind`](xref:mvc/views/razor#bind) Razor 指令特性提供与字段、属性或 Razor 表达式值的数据绑定功能。

下面的示例执行以下绑定：

* 将 `<input>` 元素值绑定到 C# `inputValue` 字段。
* 将第二个 `<input>` 元素值绑定到 C# `InputValue` 属性。

当一个 `<input>` 元素失去焦点时，将更新其绑定字段或属性。

`Pages/Bind.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

仅当呈现组件时（而不是响应字段或属性值更改），才会在 UI 中更新文本框。 由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会在 UI 中反映字段和属性更新。

下面的示例将 `InputValue` 属性绑定到第二个 `<input>` 元素的 `value` 和 [`onchange`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange) 特性，演示了如何使用 HTML 撰写数据绑定。 下面示例中的第二个 `<input>` 元素旨在演示概念，并非用于建议在 Razor 中绑定数据的方式。

`Pages/BindTheory.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

`BindTheory` 组件呈现时，HTML 演示 `<input>` 元素的 `value` 来自 `InputValue` 属性。 用户在文本框中输入值并更改元素焦点时，会触发 `onchange` 事件并将 `InputValue` 属性设置为更改的值。 实际上，代码执行更加复杂，因为 [`@bind`](xref:mvc/views/razor#bind) 会处理执行类型转换的情况。 通常，[`@bind`](xref:mvc/views/razor#bind) 将表达式的当前值与 `value` 特性关联，并使用注册的处理程序处理更改。

通过包括 `@bind:event="{EVENT}"` 特性（用 DOM 事件表示 `{EVENT}` 占位符），可在其他[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件发生时绑定属性或字段。 下面的示例在 `<input>` 元素的 [`oninput` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput)触发时将 `InputValue` 属性绑定到该元素的值。 与在元素失去焦点时触发的 [`onchange` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange)不同，[`oninput`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) 在文本框的值发生更改时触发。

`Page/BindEvent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

Razor 特性绑定区分大小写：

* `@bind` 和 `@bind:event` 有效。
* `@Bind`/`@Bind:Event`（`B` 和 `E` 是大写字母）或 `@BIND`/`@BIND:EVENT`（全部是大写字母）无效。

## <a name="unparsable-values"></a>无法分析的值

如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。

以下面的组件为例，其中 `<input>` 元素绑定到初始值为 `123` 的 `int` 类型。

`Pages/UnparsableValues.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

默认情况下，绑定适用于元素的 `onchange` 事件。 如果用户将文本框输入的值更新为 `123.45` 并更改焦点，则元素的值将在 `onchange` 触发时还原为 `123`。 如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。

对于 `oninput` 事件 (`@bind:event="oninput"`)，将在执行生成无法分析的值的击键操作后还原值。 当使用 `int` 绑定类型以 `oninput` 事件为目标时，会阻止用户键入小数点 (`.`) 字符。 小数点 (`.`) 字符会立即被删除，因此用户会收到仅允许整数的即时反馈。 在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。 替代方案包括：

* 不使用 `oninput` 事件。 使用默认 `onchange` 事件，其中无效值在元素失去焦点之前不会还原。
* 绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供[自定义 `get` 和 `set` 访问器逻辑](#custom-binding-formats)来处理无效输入。
* 使用[窗体验证组件](xref:blazor/forms-validation)，如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>。 窗体验证组件提供用于管理无效输入的内置支持。 窗体验证组件：
  * 允许用户提供无效输入并在关联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 上接收验证错误。
  * 在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。

## <a name="format-strings"></a>格式字符串

数据绑定通过 `@bind:format="{FORMAT STRING}"` 使用单个 <xref:System.DateTime> 格式字符串，其中 `{FORMAT STRING}` 占位符是格式字符串。 其他格式表达式（如货币或数字格式）目前不可用，但可能会在将来的版本中添加。

`Pages/DateBinding.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

在前面的代码中，`<input>` 元素的字段类型（`type` 特性）默认为 `text`。

支持可为 null 的 <xref:System.DateTime?displayProperty=fullName> 和 <xref:System.DateTimeOffset?displayProperty=fullName>：

```csharp
private DateTime? date;
private DateTimeOffset? dateOffset;
```

不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。 尽管提出了建议，但如果使用 `date` 字段类型提供格式，则只有使用 `yyyy-MM-dd` 日期格式才能使绑定正常工作：

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="custom-binding-formats"></a>自定义绑定格式

[C# `get` 和 `set` 访问器](/dotnet/csharp/programming-guide/classes-and-structs/using-properties)可用于创建自定义绑定格式行为，如下面的 `DecimalBinding` 组件所示。 此组件通过 `string` 属性 (`DecimalValue`) 将最多具有三个小数位的正或负小数伪绑定到 `<input>` 元素。

`Pages/DecimalBinding.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

## <a name="binding-with-component-parameters"></a>与组件参数绑定

常见方案是将子组件中的属性绑定到其父组件中的属性。 此方案称为链接绑定，因为多个级别的绑定会同时进行。

[组件参数](xref:blazor/components/index#component-parameters)允许使用 `@bind-{PROPERTY}` 语法绑定父组件的属性，其中 `{PROPERTY}` 占位符是要绑定的属性。

不能在子组件中使用 [`@bind`](xref:mvc/views/razor#bind) 语法来实现链接绑定。 必须单独指定事件处理程序和值，以支持从子组件更新父组件中的属性。

父组件仍利用 [`@bind`](xref:mvc/views/razor#bind) 语法来设置与子组件的数据绑定。

以下 `ChildBind` 组件具有 `Year` 组件参数和 <xref:Microsoft.AspNetCore.Components.EventCallback%601>。 按照约定，此参数的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 必须以组件参数名称命名，并带有“`Changed`”后缀。 命名语法为 `{PARAMETER NAME}Changed`，其中 `{PARAMETER NAME}` 占位符是参数名称。 在以下示例中，将 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 命名为 `YearChanged`。

<xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 调用与提供的参数进行绑定相关联的委托，并为已更改的属性调度事件通知。

`Shared/ChildBind.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

有关事件和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 的详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback> 一文的“EventCallback”部分。

在下面的 `Parent` 组件中，`Year` 字段绑定到子组件的 `year` 参数。 `Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。

`Pages/Parent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

按照约定，可通过包含分配到处理程序的 `@bind-{PROPERTY}:event` 特性，将属性绑定到对应的事件处理程序，其中 `{PROPERTY}` 占位符是属性。 `<ChildBind @bind-Year="year" />` 等效于此写入：

```razor
<ChildBind @bind-Year="year" @bind-Year:event="YearChanged" />
```

在更复杂和实际的示例中，以下 `Password` 组件：

* 将 `<input>` 元素的值设置为 `password` 字段。
* 将 `Password` 属性的更改公开给父组件，其中 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) 以子级 `password` 字段的当前值作为参数传递。
* 使用 `onclick` 事件触发 `ToggleShowPassword` 方法。 有关详细信息，请参阅 <xref:blazor/components/event-handling>。

`Shared/PasswordEntry.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

`PasswordEntry` 组件用于另一个组件中，例如下面的 `PasswordBinding` 组件示例。

`Pages/PasswordBinding.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

在调用绑定的委托的方法中执行检查或捕获错误。 经过修改的以下 `PasswordEntry` 组件会在密码的值中使用空格时向用户提供即时反馈。

`Shared/PasswordEntry.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

## <a name="bind-across-more-than-two-components"></a>绑定到两个以上的组件

可以将参数绑定到任意数量的嵌套组件，但必须采用单向数据流：

* 更改通知沿层次结构向上传递。
* 新参数值按层次结构向下传递。

推荐的常见方法是仅将基础数据存储在父组件中，以避免对必须更新的状态产生任何混淆，如下面示例所示。

`Pages/Parent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

`Shared/NestedChild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

`Shared/NestedGrandchild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

有关适用于在内存中和跨不必嵌套的组件共享数据的可选方法，请参阅 <xref:blazor/state-management>。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/forms-validation>
* [绑定到窗体中的单选按钮](xref:blazor/forms-validation#radio-buttons)
* [将 `<select>` 元素选项绑定到窗体中的 C# 对象 `null` 值](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
* [ASP.NET Core Blazor 事件处理：`EventCallback` 部分](xref:blazor/components/event-handling#eventcallback)
