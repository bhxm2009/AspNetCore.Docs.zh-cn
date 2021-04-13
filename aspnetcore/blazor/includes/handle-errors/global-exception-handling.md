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
ms.openlocfilehash: 0ce606cc4800bcf5855b70a26272fceb3ad8e944
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554872"
---
Blazor 是一个单页应用程序 (SPA) 客户端框架。 浏览器充当应用的主机，因此也基于导航和静态资产的 URI 请求，充当独立 Razor 组件的处理管道。 与在设有中间件处理管道的服务器上运行的 ASP.NET Core 应用不同的是，这里没有负责为 Razor 组件处理请求的中间件管道可以用来处理全局错误。 但是，应用可以使用错误处理组件作为级联值来集中处理错误。

以下 `Error` 组件将自身作为 [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) 传递给子组件。 下面的示例仅记录错误，但组件的方法可以按照应用要求的任何方式处理错误，包括通过使用多种错误处理方法。 与使用[注入式服务](xref:blazor/fundamentals/dependency-injection)或自定义记录器实现相比，使用组件的一个优点是，在发生错误时，级联组件可以呈现内容并应用 CSS 样式。

`Shared/Error.razor`:

```razor
@using Microsoft.Extensions.Logging
@inject ILogger<Error> Logger

<CascadingValue Value=this>
    @ChildContent
</CascadingValue>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public void ProcessError(Exception ex)
    {
        Logger.LogError("Error:ProcessError - Type: {Type} Message: {Message}", 
            ex.GetType(), ex.Message);
    }
}
```

在 `App` 组件中，用 `Error` 组件将 `Router` 组件包装起来。 这允许 `Error` 组件向下级联到应用中将 `Error` 组件作为 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) 接收的任何组件。

`App.razor`:

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

要在组件中处理错误：

* 将 `Error` 组件指定为 [`@code`](xref:mvc/views/razor#code) 块中的 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute)：

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* 使用适当的异常类型在任何 `catch` 块中调用错误处理方法。 该示例 `Error` 组件只提供了一个 `ProcessError` 方法，但错误处理组件可以提供任意数量的错误处理方法来解决整个应用中的其他错误处理要求。

  ```csharp
  try
  {
      ...
  }
  catch (Exception ex)
  {
      Error.ProcessError(ex);
  }
  ```

使用前面的示例 `Error` 组件和 `ProcessError` 方法，浏览器的开发人员工具控制台会指示捕获并记录的错误：

> fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException Message: Object reference not set to an instance of an object.

如果 `ProcessError` 方法直接参与呈现，例如，显示自定义错误消息栏或更改所呈现元素的 CSS 样式，请在 `ProcessErrors` 方法末尾调用 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) 来重新呈现 UI。
