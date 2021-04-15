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
ms.openlocfilehash: 62d000082180163737ae95bf8fc829b9f026b8a8
ms.sourcegitcommit: 7354c2029164702d075fd3786d96a92c6d49bc6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178257"
---
<span data-ttu-id="c3c48-101">嵌套组件通常使用链式绑定来绑定数据，如 <xref:blazor/components/data-binding> 中所述。</span><span class="sxs-lookup"><span data-stu-id="c3c48-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="c3c48-102">嵌套组件和非嵌套组件可以使用已注册的内存中状态容器来共享对数据的访问。</span><span class="sxs-lookup"><span data-stu-id="c3c48-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="c3c48-103">自定义状态容器类可以使用可分配的 <xref:System.Action>，来向应用不同部分中的组件通知状态更改。</span><span class="sxs-lookup"><span data-stu-id="c3c48-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="c3c48-104">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="c3c48-104">In the following example:</span></span>

* <span data-ttu-id="c3c48-105">一对组件使用状态容器来跟踪属性。</span><span class="sxs-lookup"><span data-stu-id="c3c48-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="c3c48-106">以下示例中的一个组件嵌套在另一个组件中，但此方法不需要嵌套就能工作。</span><span class="sxs-lookup"><span data-stu-id="c3c48-106">One component in the following example is nested in the other component, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="c3c48-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="c3c48-107">`StateContainer.cs`:</span></span>

```csharp
using System;

public class StateContainer
{
    private string savedString;

    public string Property
    {
        get => savedString;
        set
        {
            savedString = value;
            NotifyStateChanged();
        }
    }

    public event Action OnChange;

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="c3c48-108">在 `Program.Main` (Blazor WebAssembly) 中：</span><span class="sxs-lookup"><span data-stu-id="c3c48-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="c3c48-109">在 `Startup.ConfigureServices` (Blazor Server) 中：</span><span class="sxs-lookup"><span data-stu-id="c3c48-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddScoped<StateContainer>();
```

<span data-ttu-id="c3c48-110">`Shared/Nested.razor`:</span><span class="sxs-lookup"><span data-stu-id="c3c48-110">`Shared/Nested.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Nested component</h2>

<p>Nested component Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">
        Change the Property from the Nested component
    </button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.Property = 
            $"New value set in the Nested component: {DateTime.Now}";
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="c3c48-111">`Pages/StateContainer.razor`:</span><span class="sxs-lookup"><span data-stu-id="c3c48-111">`Pages/StateContainer.razor`:</span></span>

```razor
@page "/state-container"
@inject StateContainer StateContainer
@implements IDisposable

<h1>State Container component</h1>

<p>State Container component Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">
        Change the Property from the State Container component
    </button>
</p>

<Nested />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.Property = 
            $"New value set in the State Container component: {DateTime.Now}";
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="c3c48-112">前面的组件实现 <xref:System.IDisposable>，并且 `OnChange` 委托在 `Dispose` 方法中取消订阅，这些方法是在释放组件时由框架调用的。</span><span class="sxs-lookup"><span data-stu-id="c3c48-112">The preceding components implement <xref:System.IDisposable>, and the `OnChange` delegates are unsubscribed in the `Dispose` methods, which are called by the framework when the components are disposed.</span></span> <span data-ttu-id="c3c48-113">有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="c3c48-113">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
