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
ms.openlocfilehash: 5f1e9963a7b62b90ee492bebe9bfc357a8090caf
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105051031"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="8014b-103">ASP.NET Core Blazor 数据绑定</span><span class="sxs-lookup"><span data-stu-id="8014b-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="8014b-104">Razor 组件使用 [`@bind`](xref:mvc/views/razor#bind) Razor 指令特性提供与字段、属性或 Razor 表达式值的数据绑定功能。</span><span class="sxs-lookup"><span data-stu-id="8014b-104">Razor components provide data binding features with the [`@bind`](xref:mvc/views/razor#bind) Razor directive attribute with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="8014b-105">下面的示例执行以下绑定：</span><span class="sxs-lookup"><span data-stu-id="8014b-105">The following example binds:</span></span>

* <span data-ttu-id="8014b-106">将 `<input>` 元素值绑定到 C# `inputValue` 字段。</span><span class="sxs-lookup"><span data-stu-id="8014b-106">An `<input>` element value to the C# `inputValue` field.</span></span>
* <span data-ttu-id="8014b-107">将第二个 `<input>` 元素值绑定到 C# `InputValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-107">A second `<input>` element value to the C# `InputValue` property.</span></span>

<span data-ttu-id="8014b-108">当一个 `<input>` 元素失去焦点时，将更新其绑定字段或属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-108">When an `<input>` element loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="8014b-109">`Pages/Bind.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-109">`Pages/Bind.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Bind.razor?highlight=4,8)]

::: moniker-end

<span data-ttu-id="8014b-110">仅当呈现组件时（而不是响应字段或属性值更改），才会在 UI 中更新文本框。</span><span class="sxs-lookup"><span data-stu-id="8014b-110">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="8014b-111">由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会在 UI 中反映字段和属性更新。</span><span class="sxs-lookup"><span data-stu-id="8014b-111">Since components render themselves after event handler code executes, field and property updates are usually reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="8014b-112">下面的示例将 `InputValue` 属性绑定到第二个 `<input>` 元素的 `value` 和 [`onchange`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange) 特性，演示了如何使用 HTML 撰写数据绑定。</span><span class="sxs-lookup"><span data-stu-id="8014b-112">As a demonstration of how data binding composes in HTML, the following example binds the `InputValue` property to the second `<input>` element's `value` and [`onchange`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange) attributes.</span></span> <span data-ttu-id="8014b-113">下面示例中的第二个 `<input>` 元素旨在演示概念，并非用于建议在 Razor 中绑定数据的方式。</span><span class="sxs-lookup"><span data-stu-id="8014b-113">*The second `<input>` element in the following example is a concept demonstration and isn't meant to suggest how you should bind data in Razor components.*</span></span>

<span data-ttu-id="8014b-114">`Pages/BindTheory.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-114">`Pages/BindTheory.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindTheory.razor?highlight=12-14)]

::: moniker-end

<span data-ttu-id="8014b-115">`BindTheory` 组件呈现时，HTML 演示 `<input>` 元素的 `value` 来自 `InputValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-115">When the `BindTheory` component is rendered, the `value` of the HTML demonstration `<input>` element comes from the `InputValue` property.</span></span> <span data-ttu-id="8014b-116">用户在文本框中输入值并更改元素焦点时，会触发 `onchange` 事件并将 `InputValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="8014b-116">When the user enters a value in the text box and changes element focus, the `onchange` event is fired and the `InputValue` property is set to the changed value.</span></span> <span data-ttu-id="8014b-117">实际上，代码执行更加复杂，因为 [`@bind`](xref:mvc/views/razor#bind) 会处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="8014b-117">In reality, code execution is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="8014b-118">通常，[`@bind`](xref:mvc/views/razor#bind) 将表达式的当前值与 `value` 特性关联，并使用注册的处理程序处理更改。</span><span class="sxs-lookup"><span data-stu-id="8014b-118">In general, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="8014b-119">通过包括 `@bind:event="{EVENT}"` 特性（用 DOM 事件表示 `{EVENT}` 占位符），可在其他[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件发生时绑定属性或字段。</span><span class="sxs-lookup"><span data-stu-id="8014b-119">Bind a property or field on other [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events by including an `@bind:event="{EVENT}"` attribute with a DOM event for the `{EVENT}` placeholder.</span></span> <span data-ttu-id="8014b-120">下面的示例在 `<input>` 元素的 [`oninput` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput)触发时将 `InputValue` 属性绑定到该元素的值。</span><span class="sxs-lookup"><span data-stu-id="8014b-120">The following example binds the `InputValue` property to the `<input>` element's value when the element's [`oninput` event](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) is triggered.</span></span> <span data-ttu-id="8014b-121">与在元素失去焦点时触发的 [`onchange` 事件](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange)不同，[`oninput`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) 在文本框的值发生更改时触发。</span><span class="sxs-lookup"><span data-stu-id="8014b-121">Unlike the [`onchange` event](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onchange), which fires when the element loses focus, [`oninput`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/oninput) fires when the value of the text box changes.</span></span>

<span data-ttu-id="8014b-122">`Page/BindEvent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-122">`Page/BindEvent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/BindEvent.razor?highlight=4)]

::: moniker-end

<span data-ttu-id="8014b-123">Razor 特性绑定区分大小写：</span><span class="sxs-lookup"><span data-stu-id="8014b-123">Razor attribute binding is case sensitive:</span></span>

* <span data-ttu-id="8014b-124">`@bind` 和 `@bind:event` 有效。</span><span class="sxs-lookup"><span data-stu-id="8014b-124">`@bind` and `@bind:event` are valid.</span></span>
* <span data-ttu-id="8014b-125">`@Bind`/`@Bind:Event`（`B` 和 `E` 是大写字母）或 `@BIND`/`@BIND:EVENT`（全部是大写字母）无效。</span><span class="sxs-lookup"><span data-stu-id="8014b-125">`@Bind`/`@Bind:Event` (capital letters `B` and `E`) or `@BIND`/`@BIND:EVENT` (all capital letters) **are invalid**.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="8014b-126">无法分析的值</span><span class="sxs-lookup"><span data-stu-id="8014b-126">Unparsable values</span></span>

<span data-ttu-id="8014b-127">如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。</span><span class="sxs-lookup"><span data-stu-id="8014b-127">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="8014b-128">以下面的组件为例，其中 `<input>` 元素绑定到初始值为 `123` 的 `int` 类型。</span><span class="sxs-lookup"><span data-stu-id="8014b-128">Consider the following component, where an `<input>` element is bound to an `int` type with an initial value of `123`.</span></span>

<span data-ttu-id="8014b-129">`Pages/UnparsableValues.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-129">`Pages/UnparsableValues.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/UnparsableValues.razor?highlight=4,12)]

::: moniker-end

<span data-ttu-id="8014b-130">默认情况下，绑定适用于元素的 `onchange` 事件。</span><span class="sxs-lookup"><span data-stu-id="8014b-130">By default, binding applies to the element's `onchange` event.</span></span> <span data-ttu-id="8014b-131">如果用户将文本框输入的值更新为 `123.45` 并更改焦点，则元素的值将在 `onchange` 触发时还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="8014b-131">If the user updates the value of the text box's entry to `123.45` and changes the focus, the element's value is reverted to `123` when `onchange` fires.</span></span> <span data-ttu-id="8014b-132">如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="8014b-132">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="8014b-133">对于 `oninput` 事件 (`@bind:event="oninput"`)，将在执行生成无法分析的值的击键操作后还原值。</span><span class="sxs-lookup"><span data-stu-id="8014b-133">For the `oninput` event (`@bind:event="oninput"`), a value reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="8014b-134">当使用 `int` 绑定类型以 `oninput` 事件为目标时，会阻止用户键入小数点 (`.`) 字符。</span><span class="sxs-lookup"><span data-stu-id="8014b-134">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a dot (`.`) character.</span></span> <span data-ttu-id="8014b-135">小数点 (`.`) 字符会立即被删除，因此用户会收到仅允许整数的即时反馈。</span><span class="sxs-lookup"><span data-stu-id="8014b-135">A dot (`.`) character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="8014b-136">在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="8014b-136">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="8014b-137">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="8014b-137">Alternatives include:</span></span>

* <span data-ttu-id="8014b-138">不使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="8014b-138">Don't use the `oninput` event.</span></span> <span data-ttu-id="8014b-139">使用默认 `onchange` 事件，其中无效值在元素失去焦点之前不会还原。</span><span class="sxs-lookup"><span data-stu-id="8014b-139">Use the default `onchange` event, where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="8014b-140">绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供[自定义 `get` 和 `set` 访问器逻辑](#custom-binding-formats)来处理无效输入。</span><span class="sxs-lookup"><span data-stu-id="8014b-140">Bind to a nullable type, such as `int?` or `string` and provide [custom `get` and `set` accessor logic](#custom-binding-formats) to handle invalid entries.</span></span>
* <span data-ttu-id="8014b-141">使用[窗体验证组件](xref:blazor/forms-validation)，如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>。</span><span class="sxs-lookup"><span data-stu-id="8014b-141">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="8014b-142">窗体验证组件提供用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="8014b-142">Form validation components provide built-in support to manage invalid inputs.</span></span> <span data-ttu-id="8014b-143">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="8014b-143">Form validation components:</span></span>
  * <span data-ttu-id="8014b-144">允许用户提供无效输入并在关联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 上接收验证错误。</span><span class="sxs-lookup"><span data-stu-id="8014b-144">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="8014b-145">在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="8014b-145">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="8014b-146">格式字符串</span><span class="sxs-lookup"><span data-stu-id="8014b-146">Format strings</span></span>

<span data-ttu-id="8014b-147">数据绑定通过 `@bind:format="{FORMAT STRING}"` 使用单个 <xref:System.DateTime> 格式字符串，其中 `{FORMAT STRING}` 占位符是格式字符串。</span><span class="sxs-lookup"><span data-stu-id="8014b-147">Data binding works with a single <xref:System.DateTime> format string using `@bind:format="{FORMAT STRING}"`, where the `{FORMAT STRING}` placeholder is the format string.</span></span> <span data-ttu-id="8014b-148">其他格式表达式（如货币或数字格式）目前不可用，但可能会在将来的版本中添加。</span><span class="sxs-lookup"><span data-stu-id="8014b-148">Other format expressions, such as currency or number formats, aren't available at this time but might be added in a future release.</span></span>

<span data-ttu-id="8014b-149">`Pages/DateBinding.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-149">`Pages/DateBinding.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DateBinding.razor?highlight=6)]

::: moniker-end

<span data-ttu-id="8014b-150">在前面的代码中，`<input>` 元素的字段类型（`type` 特性）默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="8014b-150">In the preceding code, the `<input>` element's field type (`type` attribute) defaults to `text`.</span></span>

<span data-ttu-id="8014b-151">支持可为 null 的 <xref:System.DateTime?displayProperty=fullName> 和 <xref:System.DateTimeOffset?displayProperty=fullName>：</span><span class="sxs-lookup"><span data-stu-id="8014b-151">Nullable <xref:System.DateTime?displayProperty=fullName> and <xref:System.DateTimeOffset?displayProperty=fullName> are supported:</span></span>

```csharp
private DateTime? date;
private DateTimeOffset? dateOffset;
```

<span data-ttu-id="8014b-152">不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="8014b-152">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="8014b-153">尽管提出了建议，但如果使用 `date` 字段类型提供格式，则只有使用 `yyyy-MM-dd` 日期格式才能使绑定正常工作：</span><span class="sxs-lookup"><span data-stu-id="8014b-153">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="custom-binding-formats"></a><span data-ttu-id="8014b-154">自定义绑定格式</span><span class="sxs-lookup"><span data-stu-id="8014b-154">Custom binding formats</span></span>

<span data-ttu-id="8014b-155">[C# `get` 和 `set` 访问器](/dotnet/csharp/programming-guide/classes-and-structs/using-properties)可用于创建自定义绑定格式行为，如下面的 `DecimalBinding` 组件所示。</span><span class="sxs-lookup"><span data-stu-id="8014b-155">[C# `get` and `set` accessors](/dotnet/csharp/programming-guide/classes-and-structs/using-properties) can be used to create custom binding format behavior, as the following `DecimalBinding` component demonstrates.</span></span> <span data-ttu-id="8014b-156">此组件通过 `string` 属性 (`DecimalValue`) 将最多具有三个小数位的正或负小数伪绑定到 `<input>` 元素。</span><span class="sxs-lookup"><span data-stu-id="8014b-156">The component pesudo-binds a positive or negative decimal with up to three decimal places to an `<input>` element by way of a `string` property (`DecimalValue`).</span></span>

<span data-ttu-id="8014b-157">`Pages/DecimalBinding.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-157">`Pages/DecimalBinding.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/DecimalBinding.razor?highlight=7,21-31)]

::: moniker-end

## <a name="binding-with-component-parameters"></a><span data-ttu-id="8014b-158">与组件参数绑定</span><span class="sxs-lookup"><span data-stu-id="8014b-158">Binding with component parameters</span></span>

<span data-ttu-id="8014b-159">常见方案是将子组件中的属性绑定到其父组件中的属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-159">A common scenario is binding a property of a child component to a property in its parent component.</span></span> <span data-ttu-id="8014b-160">此方案称为链接绑定，因为多个级别的绑定会同时进行。</span><span class="sxs-lookup"><span data-stu-id="8014b-160">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="8014b-161">[组件参数](xref:blazor/components/index#component-parameters)允许使用 `@bind-{PROPERTY}` 语法绑定父组件的属性，其中 `{PROPERTY}` 占位符是要绑定的属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-161">[Component parameters](xref:blazor/components/index#component-parameters) permit binding properties of a parent component with `@bind-{PROPERTY}` syntax, where the `{PROPERTY}` placeholder is the property to bind.</span></span>

<span data-ttu-id="8014b-162">不能在子组件中使用 [`@bind`](xref:mvc/views/razor#bind) 语法来实现链接绑定。</span><span class="sxs-lookup"><span data-stu-id="8014b-162">You can't implement chained binds with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="8014b-163">必须单独指定事件处理程序和值，以支持从子组件更新父组件中的属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-163">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="8014b-164">父组件仍利用 [`@bind`](xref:mvc/views/razor#bind) 语法来设置与子组件的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="8014b-164">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the databinding with the child component.</span></span>

<span data-ttu-id="8014b-165">以下 `ChildBind` 组件具有 `Year` 组件参数和 <xref:Microsoft.AspNetCore.Components.EventCallback%601>。</span><span class="sxs-lookup"><span data-stu-id="8014b-165">The following `ChildBind` component has a `Year` component parameter and an <xref:Microsoft.AspNetCore.Components.EventCallback%601>.</span></span> <span data-ttu-id="8014b-166">按照约定，此参数的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 必须以组件参数名称命名，并带有“`Changed`”后缀。</span><span class="sxs-lookup"><span data-stu-id="8014b-166">By convention, the <xref:Microsoft.AspNetCore.Components.EventCallback%601> for the parameter must be named as the component parameter name with a "`Changed`" suffix.</span></span> <span data-ttu-id="8014b-167">命名语法为 `{PARAMETER NAME}Changed`，其中 `{PARAMETER NAME}` 占位符是参数名称。</span><span class="sxs-lookup"><span data-stu-id="8014b-167">The naming syntax is `{PARAMETER NAME}Changed`, where the `{PARAMETER NAME}` placeholder is the parameter name.</span></span> <span data-ttu-id="8014b-168">在以下示例中，将 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 命名为 `YearChanged`。</span><span class="sxs-lookup"><span data-stu-id="8014b-168">In the following example, the <xref:Microsoft.AspNetCore.Components.EventCallback%601> is named `YearChanged`.</span></span>

<span data-ttu-id="8014b-169"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 调用与提供的参数进行绑定相关联的委托，并为已更改的属性调度事件通知。</span><span class="sxs-lookup"><span data-stu-id="8014b-169"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="8014b-170">`Shared/ChildBind.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-170">`Shared/ChildBind.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/ChildBind.razor?highlight=14-15,17-18,22)]

::: moniker-end

<span data-ttu-id="8014b-171">有关事件和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 的详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback> 一文的“EventCallback”部分。</span><span class="sxs-lookup"><span data-stu-id="8014b-171">For more information on events and <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see the *EventCallback* section of the <xref:blazor/components/event-handling#eventcallback> article.</span></span>

<span data-ttu-id="8014b-172">在下面的 `Parent` 组件中，`Year` 字段绑定到子组件的 `year` 参数。</span><span class="sxs-lookup"><span data-stu-id="8014b-172">In the following `Parent` component, the `year` field is bound to the `Year` parameter of the child component.</span></span> <span data-ttu-id="8014b-173">`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。</span><span class="sxs-lookup"><span data-stu-id="8014b-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="8014b-174">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-174">`Pages/Parent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent1.razor?highlight=9)]

::: moniker-end

<span data-ttu-id="8014b-175">按照约定，可通过包含分配到处理程序的 `@bind-{PROPERTY}:event` 特性，将属性绑定到对应的事件处理程序，其中 `{PROPERTY}` 占位符是属性。</span><span class="sxs-lookup"><span data-stu-id="8014b-175">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler, where the `{PROPERTY}` placeholder is the property.</span></span> <span data-ttu-id="8014b-176">`<ChildBind @bind-Year="year" />` 等效于此写入：</span><span class="sxs-lookup"><span data-stu-id="8014b-176">`<ChildBind @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<ChildBind @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="8014b-177">在更复杂和实际的示例中，以下 `Password` 组件：</span><span class="sxs-lookup"><span data-stu-id="8014b-177">In a more sophisticated and real-world example, the following `Password` component:</span></span>

* <span data-ttu-id="8014b-178">将 `<input>` 元素的值设置为 `password` 字段。</span><span class="sxs-lookup"><span data-stu-id="8014b-178">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="8014b-179">将 `Password` 属性的更改公开给父组件，其中 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) 以子级 `password` 字段的当前值作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="8014b-179">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="8014b-180">使用 `onclick` 事件触发 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="8014b-180">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="8014b-181">有关详细信息，请参阅 <xref:blazor/components/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="8014b-181">For more information, see <xref:blazor/components/event-handling>.</span></span>

<span data-ttu-id="8014b-182">`Shared/PasswordEntry.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-182">`Shared/PasswordEntry.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry.razor?highlight=7-10,13,23-24,26-27,36-39)]

::: moniker-end

<span data-ttu-id="8014b-183">`PasswordEntry` 组件用于另一个组件中，例如下面的 `PasswordBinding` 组件示例。</span><span class="sxs-lookup"><span data-stu-id="8014b-183">The `PasswordEntry` component is used in another component, such as the following `PasswordBinding` component example.</span></span>

<span data-ttu-id="8014b-184">`Pages/PasswordBinding.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-184">`Pages/PasswordBinding.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/PasswordBinding.razor?highlight=5)]

::: moniker-end

<span data-ttu-id="8014b-185">在调用绑定的委托的方法中执行检查或捕获错误。</span><span class="sxs-lookup"><span data-stu-id="8014b-185">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="8014b-186">经过修改的以下 `PasswordEntry` 组件会在密码的值中使用空格时向用户提供即时反馈。</span><span class="sxs-lookup"><span data-stu-id="8014b-186">The following revised `PasswordEntry` component provides immediate feedback to the user if a space is used in the password's value.</span></span>

<span data-ttu-id="8014b-187">`Shared/PasswordEntry.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-187">`Shared/PasswordEntry.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/PasswordEntry2.razor?highlight=35-46)]

::: moniker-end

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="8014b-188">绑定到两个以上的组件</span><span class="sxs-lookup"><span data-stu-id="8014b-188">Bind across more than two components</span></span>

<span data-ttu-id="8014b-189">可以将参数绑定到任意数量的嵌套组件，但必须采用单向数据流：</span><span class="sxs-lookup"><span data-stu-id="8014b-189">You can bind parameters through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="8014b-190">更改通知沿层次结构向上传递。</span><span class="sxs-lookup"><span data-stu-id="8014b-190">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="8014b-191">新参数值按层次结构向下传递。</span><span class="sxs-lookup"><span data-stu-id="8014b-191">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="8014b-192">推荐的常见方法是仅将基础数据存储在父组件中，以避免对必须更新的状态产生任何混淆，如下面示例所示。</span><span class="sxs-lookup"><span data-stu-id="8014b-192">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated, as shown in the following example.</span></span>

<span data-ttu-id="8014b-193">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-193">`Pages/Parent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/data-binding/Parent2.razor)]

::: moniker-end

<span data-ttu-id="8014b-194">`Shared/NestedChild.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-194">`Shared/NestedChild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedChild.razor)]

::: moniker-end

<span data-ttu-id="8014b-195">`Shared/NestedGrandchild.razor`:</span><span class="sxs-lookup"><span data-stu-id="8014b-195">`Shared/NestedGrandchild.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/data-binding/NestedGrandchild.razor)]

::: moniker-end

<span data-ttu-id="8014b-196">有关适用于在内存中和跨不必嵌套的组件共享数据的可选方法，请参阅 <xref:blazor/state-management>。</span><span class="sxs-lookup"><span data-stu-id="8014b-196">For an alternative approach suited to sharing data in memory and across components that aren't necessarily nested, see <xref:blazor/state-management>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8014b-197">其他资源</span><span class="sxs-lookup"><span data-stu-id="8014b-197">Additional resources</span></span>

* <xref:blazor/forms-validation>
* [<span data-ttu-id="8014b-198">绑定到窗体中的单选按钮</span><span class="sxs-lookup"><span data-stu-id="8014b-198">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="8014b-199">将 `<select>` 元素选项绑定到窗体中的 C# 对象 `null` 值</span><span class="sxs-lookup"><span data-stu-id="8014b-199">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
* [<span data-ttu-id="8014b-200">ASP.NET Core Blazor 事件处理：`EventCallback` 部分</span><span class="sxs-lookup"><span data-stu-id="8014b-200">ASP.NET Core Blazor event handling: `EventCallback` section</span></span>](xref:blazor/components/event-handling#eventcallback)
