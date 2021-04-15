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
嵌套组件通常使用链式绑定来绑定数据，如 <xref:blazor/components/data-binding> 中所述。 嵌套组件和非嵌套组件可以使用已注册的内存中状态容器来共享对数据的访问。 自定义状态容器类可以使用可分配的 <xref:System.Action>，来向应用不同部分中的组件通知状态更改。 如下示例中：

* 一对组件使用状态容器来跟踪属性。
* 以下示例中的一个组件嵌套在另一个组件中，但此方法不需要嵌套就能工作。

`StateContainer.cs`:

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

在 `Program.Main` (Blazor WebAssembly) 中：

```csharp
builder.Services.AddSingleton<StateContainer>();
```

在 `Startup.ConfigureServices` (Blazor Server) 中：

```csharp
services.AddScoped<StateContainer>();
```

`Shared/Nested.razor`:

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

`Pages/StateContainer.razor`:

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

前面的组件实现 <xref:System.IDisposable>，并且 `OnChange` 委托在 `Dispose` 方法中取消订阅，这些方法是在释放组件时由框架调用的。 有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。
