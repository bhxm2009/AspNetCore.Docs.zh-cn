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
<span data-ttu-id="8a59f-101">本部分适用于预呈现 Razor 组件的 Blazor Server 和托管 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="8a59f-101">*This section applies to Blazor Server and hosted Blazor WebAssembly apps that prerender Razor components.*</span></span>

<span data-ttu-id="8a59f-102">在应用进行预呈现时，无法执行调用 JavaScript 等特定操作。</span><span class="sxs-lookup"><span data-stu-id="8a59f-102">While an app is prerendering, certain actions, such as calling into JavaScript, aren't possible.</span></span> <span data-ttu-id="8a59f-103">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="8a59f-103">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="8a59f-104">若要将 JavaScript 互操作调用延迟到能够保证此类调用正常工作之后，请重写 [`OnAfterRender{Async}` 生命周期事件](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)。</span><span class="sxs-lookup"><span data-stu-id="8a59f-104">To delay JavaScript interop calls until a point where such calls are guaranteed to work, override the [`OnAfterRender{Async}` lifecycle event](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync).</span></span> <span data-ttu-id="8a59f-105">仅在完全呈现应用后，才会调用此事件。</span><span class="sxs-lookup"><span data-stu-id="8a59f-105">This event is only called after the app is fully rendered.</span></span>

<span data-ttu-id="8a59f-106">`Pages/PrerenderedInterop1.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a59f-106">`Pages/PrerenderedInterop1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

<span data-ttu-id="8a59f-107">对于上述示例代码，请在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供一个 `setElementText1` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="8a59f-107">For the preceding example code, provide a `setElementText1` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="8a59f-108">该函数通过 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 进行调用，不返回值：</span><span class="sxs-lookup"><span data-stu-id="8a59f-108">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText1 = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="8a59f-109">**上述示例直接修改文档对象模型 (DOM)，以便仅供演示所用。**</span><span class="sxs-lookup"><span data-stu-id="8a59f-109">**The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.**</span></span> <span data-ttu-id="8a59f-110">大多数情况下，不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="8a59f-110">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

> [!NOTE]
> <span data-ttu-id="8a59f-111">前面的示例使用全局方法来污染客户端。</span><span class="sxs-lookup"><span data-stu-id="8a59f-111">The preceding example pollutes the client with global methods.</span></span> <span data-ttu-id="8a59f-112">若要在生产应用中获取更好的方法，请参阅 [Blazor JavaScript 隔离和对象引用](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="8a59f-112">For a better approach in production apps, see [Blazor JavaScript isolation and object references](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references).</span></span>
>
> <span data-ttu-id="8a59f-113">示例：</span><span class="sxs-lookup"><span data-stu-id="8a59f-113">Example:</span></span>
>
> ```javascript
> export setElementText1 = (element, text) => element.innerText = text;
> ```

<span data-ttu-id="8a59f-114">以下组件展示了如何以一种与预呈现兼容的方式将 JavaScript 互操作用作组件初始化逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="8a59f-114">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="8a59f-115">该组件显示可从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 内部触发呈现更新。</span><span class="sxs-lookup"><span data-stu-id="8a59f-115">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="8a59f-116">开发人员必须小心避免在此场景中创建无限循环。</span><span class="sxs-lookup"><span data-stu-id="8a59f-116">The developer must be careful to avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="8a59f-117">如果调用 <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType>，则 `ElementRef` 仅在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 中使用，而不在任何更早的生命周期方法中使用，因为呈现组件后才会有 JavaScript 元素。</span><span class="sxs-lookup"><span data-stu-id="8a59f-117">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="8a59f-118">通过调用 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged)，可使用从 JavaScript 互操作调用中获取的新状态重新呈现组件（有关详细信息，请参阅 <xref:blazor/components/rendering>）。</span><span class="sxs-lookup"><span data-stu-id="8a59f-118">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) is called to rerender the component with the new state obtained from the JavaScript interop call (for more information, see <xref:blazor/components/rendering>).</span></span> <span data-ttu-id="8a59f-119">此代码不会创建无限循环，因为仅在 `infoFromJs` 为 `null` 时才调用 `StateHasChanged`。</span><span class="sxs-lookup"><span data-stu-id="8a59f-119">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

<span data-ttu-id="8a59f-120">`Pages/PrerenderedInterop2.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a59f-120">`Pages/PrerenderedInterop2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

<span data-ttu-id="8a59f-121">对于上述示例代码，请在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的 `<head>` 元素中，提供一个 `setElementText2` JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="8a59f-121">For the preceding example code, provide a `setElementText2` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="8a59f-122">该函数通过 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 进行调用，会返回值：</span><span class="sxs-lookup"><span data-stu-id="8a59f-122">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText2 = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="8a59f-123">**上述示例直接修改文档对象模型 (DOM)，以便仅供演示所用。**</span><span class="sxs-lookup"><span data-stu-id="8a59f-123">**The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.**</span></span> <span data-ttu-id="8a59f-124">大多数情况下，不建议使用 JavaScript 直接修改 DOM，因为 JavaScript 可能会干扰 Blazor 的更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="8a59f-124">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

> [!NOTE]
> <span data-ttu-id="8a59f-125">前面的示例使用全局方法来污染客户端。</span><span class="sxs-lookup"><span data-stu-id="8a59f-125">The preceding example pollutes the client with global methods.</span></span> <span data-ttu-id="8a59f-126">若要在生产应用中获取更好的方法，请参阅 [Blazor JavaScript 隔离和对象引用](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="8a59f-126">For a better approach in production apps, see [Blazor JavaScript isolation and object references](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references).</span></span>
>
> <span data-ttu-id="8a59f-127">示例：</span><span class="sxs-lookup"><span data-stu-id="8a59f-127">Example:</span></span>
>
> ```javascript
> export setElementText2 = (element, text) => {
>   element.innerText = text;
>   return text;
> };
> ```
