---
title: ASP.NET Core Blazor 事件处理
author: guardrex
description: 了解 Blazor 的事件处理特性，包括事件参数类型、事件回调和管理默认浏览器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
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
uid: blazor/components/event-handling
ms.openlocfilehash: a768934bf761632803b667b32afeab686ebed6f9
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711043"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor 事件处理

使用 [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor 语法在 Razor 组件标记中指定委托事件处理程序：

* `{DOM EVENT}` 占位符是[文档对象模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)（例如 `click`）。
* `{DELEGATE}` 占位符是 C# 委托事件处理程序。

对于事件处理：

* 支持返回 <xref:System.Threading.Tasks.Task> 的异步委托事件处理程序。
* 委托事件处理程序会自动触发 UI 呈现，因此无需手动调用 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)。
* 记录异常。

下面的代码：

* 在 UI 中选择该按钮时，调用 `UpdateHeading` 方法。
* UI 中的该复选框发生更改时，调用 `CheckChanged` 方法。

`Pages/EventHandlerExample1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

在以下示例中，`UpdateHeading`：

* 在选择该按钮时以异步方式调用。
* 在更新标题之前等待两秒。

`Pages/EventHandlerExample2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

## <a name="event-arguments"></a>事件参数

::: moniker range=">= aspnetcore-6.0"

### <a name="built-in-event-arguments"></a>内置事件参数

::: moniker-end

对于支持事件参数类型的事件，仅在方法使用事件类型时，才需要在事件方法定义中指定事件参数。 在下面的示例中，`ReportPointerLocation` 方法使用了 <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> 来设置消息文本，以便在用户选择 UI 中的按钮时报告鼠标坐标。

`Pages/EventHandlerExample3.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

支持的 <xref:System.EventArgs> 显示在下表中。

::: moniker range=">= aspnetcore-5.0"

| 事件            | 类  | [文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和说明 |
| ---------------- | ------ | --- |
| 剪贴板        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| 拖动             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。<br><br>使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)与 [HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)在 Blazor 应用中实现拖放。 |
| 错误            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| 事件            | <xref:System.EventArgs> | *常规*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*剪贴板*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*输入*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*介质*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。 |
| 焦点            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>不包含对 `relatedTarget` 的支持。 |
| 输入            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| 键盘         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| 鼠标            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 鼠标指针    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 鼠标滚轮      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| 进度         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 触控            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。 |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| 事件            | 类 | [文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和说明 |
| ---------------- | ----- | --- |
| 剪贴板        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| 拖动             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。<br><br>使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)与 [HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)在 Blazor 应用中实现拖放。 |
| 错误            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| 事件            | <xref:System.EventArgs> | *常规*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*剪贴板*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*输入*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*介质*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。 |
| 焦点            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>不包含对 `relatedTarget` 的支持。 |
| 输入            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| 键盘         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| 鼠标            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 鼠标指针    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 鼠标滚轮      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| 进度         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 触控            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。 |

::: moniker-end

有关更多信息，请参见以下资源：

* [ASP.NET Core 引用源（dotnet/aspnetcore `main` 分支）中的 `EventArgs` 类](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* [MDN Web 文档：GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含关于哪些 HTML 元素支持每个 DOM 事件的信息。

::: moniker range=">= aspnetcore-6.0"

### <a name="custom-event-arguments"></a>自定义事件参数

Blazor 支持自定义事件参数。借助这些参数，你可以将任意数据通过自定义事件传递给 .NET 事件处理程序。

#### <a name="general-configuration"></a>常规配置

通常，通过以下步骤启用具有自定义事件参数的自定义事件。

1. 在 JavaScript 中，定义一个函数，用于通过源事件生成自定义事件参数对象：

   ```javascript
   function eventArgsCreator(event) { 
     return {
       customProperty1: 'any value for property 1',
       customProperty2: event.srcElement.value
     };
   }
   ```

1. 在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 中（紧随 Blazor `<script>` 之后）使用前面的处理程序注册自定义事件：

   ```html
   <script>
       Blazor.registerCustomEventType('customevent', {
           createEventArgs: eventArgsCreator;
       });
   </script>
   ```

   > [!NOTE]
   > 每个事件仅在脚本中调用 `registerCustomEventType` 一次。

1. 定义事件参数的类：

   ```csharp
   public class CustomEventArgs : EventArgs
   {
       public string CustomProperty1 {get; set;}
       public string CustomProperty2 {get; set;}
   }
   ```

1. 通过为自定义事件添加 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 特性注释，连接自定义事件和事件参数。 此类不需要成员：

   ```csharp
   [EventHandler("oncustomevent", typeof(CustomEventArgs), enableStopPropagation: true, enablePreventDefault: true)]
   static class EventHandlers
   {
   }
   ```

1. 在一个或多个 HTML 元素上注册此事件处理程序。 在委托处理程序方法中访问从 Javascript 传入的数据：

   ```razor
   <button @oncustomevent="HandleCustomEvent">Handle</button>

   @code
   {
       void HandleCustomEvent(CustomEventArgs eventArgs)
       {
           // eventArgs.CustomProperty1
           // eventArgs.CustomProperty2
       }
   }
   ```

每当在 DOM 上触发此自定义事件时，都会使用从 Javascript 传入的数据调用此事件处理程序。

如果正在尝试触发自定义事件，则必须将 [`bubbles`](https://developer.mozilla.org/docs/Web/API/Event/bubbles) 的值设置为 `true` 以启用它。 否则，事件无法到达 Blazor 处理程序，进而无法受到处理并进入 C# 自定义 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 方法。 有关详细信息，请参阅 [MDN Web 文档：事件冒泡](https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events#event_bubbling)。

#### <a name="custom-clipboard-paste-event-example"></a>自定义剪贴板粘贴事件示例

下面的示例接收包含粘贴时间和用户所粘贴文本的自定义剪贴板粘贴事件。

声明事件的自定义名称 (`oncustompaste`)，并声明一个 .NET 类 (`CustomPasteEventArgs`) 以保存此事件的事件参数：

`CustomEvents.cs`:

```csharp
[EventHandler("oncustompaste", typeof(CustomPasteEventArgs), 
    enableStopPropagation: true, enablePreventDefault: true)]
public static class EventHandlers
{
}

public class CustomPasteEventArgs : EventArgs
{
    public DateTime EventTimestamp { get; set; }
    public string PastedData { get; set; }
}
```

添加 JavaScript 代码以为 <xref:System.EventArgs> 子类提供数据。 在 `wwwroot/index.html` 或 `Pages/_Host.cshtml` 文件中，紧随 Blazor 脚本之后添加以下 `<script>` 标记和内容。 下面的示例仅处理粘贴文本，但你可以使用任意 JavaScript API 来处理用户粘贴的其他类型的数据（如图像）。

紧随 Blazor 脚本之后的 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server)：

```html
<script>
    Blazor.registerCustomEventType('custompaste', {
        browserEventName: 'paste',
        createEventArgs: event => {
            return {
                eventTimestamp: new Date(),
                pastedData: event.clipboardData.getData('text')
            };
        }
    });
</script>
```

前面的代码会在本机 [`paste`](https://developer.mozilla.org/docs/Web/API/Element/paste_event) 事件发生时告诉浏览器：

* 引发 `custompaste` 事件。
* 使用所述的自定义逻辑提供事件参数数据：
  * 对于 `eventTimestamp`，请创建新日期。
  * 对于 `pastedData`，请获取剪贴板数据的文本形式。 有关详细信息，请参阅 [MDN Web 文档：ClipboardEvent. clipboardData](https://developer.mozilla.org/docs/Web/API/ClipboardEvent/clipboardData)。

.NET 和 JavaScript 的事件名称约定有所不同：

* 在 .NET 中，事件名称带有前缀“`on`”。
* 在 JavaScript 中，事件名称不带前缀。

在 Razor 组件中，将自定义处理程序附加到元素中。

`Pages/CustomPasteArguments.razor`:

```razor
@page "/custom-paste-arguments"

<label>
    Try pasting into the following text box:
    <input @oncustompaste="HandleCustomPaste" />
</label>

<p>
    @message
</p>

@code {
    private string message;

    private void HandleCustomPaste(CustomPasteEventArgs eventArgs)
    {
        message = $"At {eventArgs.EventTimestamp.ToShortTimeString()}, " +
            $"you pasted: {eventArgs.PastedData}";
    }
}
```

::: moniker-end

## <a name="lambda-expressions"></a>Lambda 表达式

支持 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)作为委托事件处理程序。

`Pages/EventHandlerExample4.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

使用 C# 方法参数关闭附加值通常很方便，例如在循环访问一组元素时。 下面的示例创建三个按钮，每个按钮都调用 `UpdateHeading` 并传递以下数据：

* `e` 中的事件参数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>)。
* `buttonNumber` 中的按钮编号。

`Pages/EventHandlerExample5.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

> [!NOTE]
> 不要直接在 Lambda 表达式中使用循环变量，如前面的 `for` 循环示例中的 `i`。 否则，所有 Lambda 表达式将使用相同的变量，这将导致在所有 Lambda 中使用相同的值。 始终在局部变量中捕获该变量的值，然后使用该值。 在上面的示例中：
>
> * 将循环变量 `i` 分配到 `buttonNumber`。
> * 将 `buttonNumber` 用于 lambda 表达式。

## <a name="eventcallback"></a>EventCallback

嵌套组件的一个常见方案：在子组件事件发生时执行父组件的方法。 子组件中发生的 `onclick` 事件是一个常见用例。 若要跨组件公开事件，请使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。 父组件可向子组件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 分配回调方法。

下面的 `Child` 组件演示如何设置按钮的 `onclick` 处理程序以从示例的 `ParentComponent` 接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 委托。 <xref:Microsoft.AspNetCore.Components.EventCallback> 是用 `MouseEventArgs` 键入的，这适用于来自外围设备的 `onclick` 事件。

`Shared/Child.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

`Parent` 组件将子级的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 设置为其 `ShowMessage` 方法。

`Pages/Parent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

在 `ChildComponent` 中选择该按钮时：

* 调用 `Parent` 组件的 `ShowMessage` 方法。 `message` 更新并显示在 `Parent` 组件中。
* 回调方法 (`ShowMessage`) 中不需要对 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) 的调用。 自动调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以重新呈现 `Parent` 组件，就像子事件触发组件重新呈现于在子级中执行的事件处理程序中一样。 有关详细信息，请参阅 <xref:blazor/components/rendering>。

<xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允许异步委托。 <xref:Microsoft.AspNetCore.Components.EventCallback> 是弱类型，允许将任何类型参数传入 `InvokeAsync(Object)`。 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 是强类型，需要将 `T` 参数传入可分配到 `TValue` 的 `InvokeAsync(T)` 中。

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

使用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> 调用 <xref:Microsoft.AspNetCore.Components.EventCallback> 或 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 并等待 <xref:System.Threading.Tasks.Task>：

```csharp
await OnClickCallback.InvokeAsync(arg);
```

使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 处理事件和绑定组件参数。

优先使用强类型 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 而非 <xref:Microsoft.AspNetCore.Components.EventCallback>。 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 向用户提供更好的组件错误反馈。 与其他 UI 事件处理程序类似，指定事件参数是可选操作。 当没有值传递给回调时，使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。

## <a name="prevent-default-actions"></a>阻止默认操作

使用 [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 指令特性防止事件的默认操作，其中 `{DOM EVENT}` 占位符是[文档对象模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。

在输入设备上选择某个键并且元素焦点位于某个文本框上时，浏览器通常在该文本框中显示该键的字符。 在下面的示例中，通过指定 `@onkeydown:preventDefault` 指令属性来阻止默认行为。 当焦点位于 `<input>` 元素上时，计数器随着按 Shift++<kbd></kbd><kbd></kbd> 按键顺序而递增。 不会将 `+` 字符分配到 `<input>` 元素的值。 有关 `keydown` 的详细信息，请参阅 [`MDN Web Docs: Document: keydown` 事件](https://developer.mozilla.org/docs/Web/API/Document/keydown_event)。

`Pages/EventHandlerExample6.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

指定没有值的 `@on{DOM EVENT}:preventDefault` 属性等同于 `@on{DOM EVENT}:preventDefault="true"`。

此特性也允许使用表达式值。 在下面的示例中，`shouldPreventDefault` 是设置为 `true` 或 `false` 的 `bool` 字段：

```razor
<input @onkeydown:preventDefault="shouldPreventDefault" />

...

@code {
    private bool shouldPreventDefault = true;
}
```

## <a name="stop-event-propagation"></a>停止事件传播

使用 [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 指令特性来停止事件传播，其中 `{DOM EVENT}` 占位符是[文档对象模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。

在下例中，选中复选框可阻止第二个子级 `<div>` 中的单击事件传播到父级 `<div>`。 由于传播的单击事件通常会触发 `OnSelectParentDiv` 方法，因此，如果未选中复选框，则选择第二个子级 `<div>` 会导致父级 div 消息出现。

`Pages/EventHandlerExample7.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>聚焦元素

在[元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上调用 <xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A> 来将代码中的元素作为焦点。 在下面的示例中，选择按钮可将 `<input>` 元素作为焦点。

`Pages/EventHandlerExample8.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample8.razor?highlight=16)]

::: moniker-end
