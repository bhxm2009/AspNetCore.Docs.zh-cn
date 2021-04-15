---
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
ms.openlocfilehash: f28a272c2e55e1fd6620ed4a23ef8cadcfad1ce9
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554703"
---
本部分适用于预呈现 Razor 组件的 Blazor Server 和托管 Blazor WebAssembly 应用。

在应用进行预呈现时，无法执行调用 JavaScript 等特定操作。 预呈现时，组件可能需要进行不同的呈现。

若要将 JavaScript 互操作调用延迟到能够保证此类调用正常工作之后，请重写 [`OnAfterRender{Async}` 生命周期事件](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)。 仅在完全呈现应用后，才会调用此事件。

`Pages/PrerenderedInterop1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

对于上述示例代码，请在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供一个 `setElementText1` JavaScript 函数。 该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：

```html
<script>
  window.setElementText1 = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> **上述示例直接修改文档对象模型 (DOM)，以便仅供演示所用。** 大多数情况下，不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。

> [!NOTE]
> 前面的示例使用全局方法来污染客户端。 若要在生产应用中获取更好的方法，请参阅 [Blazor JavaScript 隔离和对象引用](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。
>
> 示例：
>
> ```javascript
> export setElementText1 = (element, text) => element.innerText = text;
> ```

以下组件展示了如何以一种与预呈现兼容的方式将 JavaScript 互操作用作组件初始化逻辑的一部分。 该组件显示可从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 内部触发呈现更新。 开发人员必须小心避免在此场景中创建无限循环。

如果调用 <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType>，则 `ElementRef` 仅在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 中使用，而不在任何更早的生命周期方法中使用，因为呈现组件后才会有 JavaScript 元素。

通过调用 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged)，可使用从 JavaScript 互操作调用中获取的新状态重新呈现组件（有关详细信息，请参阅 <xref:blazor/components/rendering>）。 此代码不会创建无限循环，因为仅在 `infoFromJs` 为 `null` 时才调用 `StateHasChanged`。

`Pages/PrerenderedInterop2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

对于上述示例代码，请在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供一个 `setElementText2` JavaScript 函数。 该函数通过 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 进行调用，会返回值：

```html
<script>
  window.setElementText2 = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> **上述示例直接修改文档对象模型 (DOM)，以便仅供演示所用。** 大多数情况下，不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。

> [!NOTE]
> 前面的示例使用全局方法来污染客户端。 若要在生产应用中获取更好的方法，请参阅 [Blazor JavaScript 隔离和对象引用](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。
>
> 示例：
>
> ```javascript
> export setElementText2 = (element, text) => {
>   element.innerText = text;
>   return text;
> };
> ```
