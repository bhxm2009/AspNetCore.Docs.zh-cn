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
ms.openlocfilehash: c04ff3c2a8de6e8c9d0fa0653f09da2984dc528e
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105555040"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="78d08-103">ASP.NET Core Blazor 事件处理</span><span class="sxs-lookup"><span data-stu-id="78d08-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="78d08-104">使用 [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor 语法在 Razor 组件标记中指定委托事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="78d08-104">Specify delegate event handlers in Razor component markup with [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor syntax:</span></span>

* <span data-ttu-id="78d08-105">`{DOM EVENT}` 占位符是[文档对象模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)（例如 `click`）。</span><span class="sxs-lookup"><span data-stu-id="78d08-105">The `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events) (for example, `click`).</span></span>
* <span data-ttu-id="78d08-106">`{DELEGATE}` 占位符是 C# 委托事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="78d08-106">The `{DELEGATE}` placeholder is the C# delegate event handler.</span></span>

<span data-ttu-id="78d08-107">对于事件处理：</span><span class="sxs-lookup"><span data-stu-id="78d08-107">For event handling:</span></span>

* <span data-ttu-id="78d08-108">支持返回 <xref:System.Threading.Tasks.Task> 的异步委托事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="78d08-108">Asynchronous delegate event handlers that return a <xref:System.Threading.Tasks.Task> are supported.</span></span>
* <span data-ttu-id="78d08-109">委托事件处理程序会自动触发 UI 呈现，因此无需手动调用 [StateHasChanged](xref:blazor/components/lifecycle#state-changes-statehaschanged)。</span><span class="sxs-lookup"><span data-stu-id="78d08-109">Delegate event handlers automatically trigger a UI render, so there's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes-statehaschanged).</span></span>
* <span data-ttu-id="78d08-110">记录异常。</span><span class="sxs-lookup"><span data-stu-id="78d08-110">Exceptions are logged.</span></span>

<span data-ttu-id="78d08-111">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="78d08-111">The following code:</span></span>

* <span data-ttu-id="78d08-112">在 UI 中选择该按钮时，调用 `UpdateHeading` 方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-112">Calls the `UpdateHeading` method when the button is selected in the UI.</span></span>
* <span data-ttu-id="78d08-113">UI 中的该复选框发生更改时，调用 `CheckChanged` 方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-113">Calls the `CheckChanged` method when the check box is changed in the UI.</span></span>

<span data-ttu-id="78d08-114">`Pages/EventHandlerExample1.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-114">`Pages/EventHandlerExample1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

<span data-ttu-id="78d08-115">在以下示例中，`UpdateHeading`：</span><span class="sxs-lookup"><span data-stu-id="78d08-115">In the following example, `UpdateHeading`:</span></span>

* <span data-ttu-id="78d08-116">在选择该按钮时以异步方式调用。</span><span class="sxs-lookup"><span data-stu-id="78d08-116">Is called asynchronously when the button is selected.</span></span>
* <span data-ttu-id="78d08-117">在更新标题之前等待两秒。</span><span class="sxs-lookup"><span data-stu-id="78d08-117">Waits two seconds before updating the heading.</span></span>

<span data-ttu-id="78d08-118">`Pages/EventHandlerExample2.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-118">`Pages/EventHandlerExample2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

## <a name="event-arguments"></a><span data-ttu-id="78d08-119">事件参数</span><span class="sxs-lookup"><span data-stu-id="78d08-119">Event arguments</span></span>

::: moniker range=">= aspnetcore-6.0"

### <a name="built-in-event-arguments"></a><span data-ttu-id="78d08-120">内置事件参数</span><span class="sxs-lookup"><span data-stu-id="78d08-120">Built-in event arguments</span></span>

::: moniker-end

<span data-ttu-id="78d08-121">对于支持事件参数类型的事件，仅在方法使用事件类型时，才需要在事件方法定义中指定事件参数。</span><span class="sxs-lookup"><span data-stu-id="78d08-121">For events that support an event argument type, specifying an event parameter in the event method definition is only necessary if the event type is used in the method.</span></span> <span data-ttu-id="78d08-122">在下面的示例中，`ReportPointerLocation` 方法使用了 <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> 来设置消息文本，以便在用户选择 UI 中的按钮时报告鼠标坐标。</span><span class="sxs-lookup"><span data-stu-id="78d08-122">In the following example, <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> is used in the `ReportPointerLocation` method to set message text that reports the mouse coordinates when the user selects a button in the UI.</span></span>

<span data-ttu-id="78d08-123">`Pages/EventHandlerExample3.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-123">`Pages/EventHandlerExample3.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

<span data-ttu-id="78d08-124">支持的 <xref:System.EventArgs> 显示在下表中。</span><span class="sxs-lookup"><span data-stu-id="78d08-124">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="78d08-125">事件</span><span class="sxs-lookup"><span data-stu-id="78d08-125">Event</span></span>            | <span data-ttu-id="78d08-126">类</span><span class="sxs-lookup"><span data-stu-id="78d08-126">Class</span></span>  | <span data-ttu-id="78d08-127">[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和说明</span><span class="sxs-lookup"><span data-stu-id="78d08-127">[Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events and notes</span></span> |
| ---------------- | ------ | --- |
| <span data-ttu-id="78d08-128">剪贴板</span><span class="sxs-lookup"><span data-stu-id="78d08-128">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="78d08-129">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="78d08-129">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="78d08-130">拖动</span><span class="sxs-lookup"><span data-stu-id="78d08-130">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="78d08-131">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="78d08-131">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="78d08-132"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="78d08-132"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="78d08-133">使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)与 [HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)在 Blazor 应用中实现拖放。</span><span class="sxs-lookup"><span data-stu-id="78d08-133">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="78d08-134">错误</span><span class="sxs-lookup"><span data-stu-id="78d08-134">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="78d08-135">事件</span><span class="sxs-lookup"><span data-stu-id="78d08-135">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="78d08-136">*常规*</span><span class="sxs-lookup"><span data-stu-id="78d08-136">*General*</span></span><br><span data-ttu-id="78d08-137">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="78d08-137">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="78d08-138">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="78d08-138">*Clipboard*</span></span><br><span data-ttu-id="78d08-139">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="78d08-139">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="78d08-140">*输入*</span><span class="sxs-lookup"><span data-stu-id="78d08-140">*Input*</span></span><br><span data-ttu-id="78d08-141">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="78d08-141">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="78d08-142">*介质*</span><span class="sxs-lookup"><span data-stu-id="78d08-142">*Media*</span></span><br><span data-ttu-id="78d08-143">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="78d08-143">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="78d08-144"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="78d08-144"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="78d08-145">焦点</span><span class="sxs-lookup"><span data-stu-id="78d08-145">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="78d08-146">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="78d08-146">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="78d08-147">不包含对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="78d08-147">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="78d08-148">输入</span><span class="sxs-lookup"><span data-stu-id="78d08-148">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="78d08-149">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="78d08-149">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="78d08-150">键盘</span><span class="sxs-lookup"><span data-stu-id="78d08-150">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="78d08-151">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="78d08-151">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="78d08-152">鼠标</span><span class="sxs-lookup"><span data-stu-id="78d08-152">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="78d08-153">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="78d08-153">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="78d08-154">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="78d08-154">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="78d08-155">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="78d08-155">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="78d08-156">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="78d08-156">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="78d08-157">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="78d08-157">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="78d08-158">进度</span><span class="sxs-lookup"><span data-stu-id="78d08-158">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="78d08-159">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="78d08-159">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="78d08-160">触控</span><span class="sxs-lookup"><span data-stu-id="78d08-160">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="78d08-161">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="78d08-161">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="78d08-162"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。</span><span class="sxs-lookup"><span data-stu-id="78d08-162"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="78d08-163">事件</span><span class="sxs-lookup"><span data-stu-id="78d08-163">Event</span></span>            | <span data-ttu-id="78d08-164">类</span><span class="sxs-lookup"><span data-stu-id="78d08-164">Class</span></span> | <span data-ttu-id="78d08-165">[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和说明</span><span class="sxs-lookup"><span data-stu-id="78d08-165">[Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events and notes</span></span> |
| ---------------- | ----- | --- |
| <span data-ttu-id="78d08-166">剪贴板</span><span class="sxs-lookup"><span data-stu-id="78d08-166">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="78d08-167">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="78d08-167">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="78d08-168">拖动</span><span class="sxs-lookup"><span data-stu-id="78d08-168">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="78d08-169">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="78d08-169">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="78d08-170"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 和 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 保留拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="78d08-170"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="78d08-171">使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)与 [HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)在 Blazor 应用中实现拖放。</span><span class="sxs-lookup"><span data-stu-id="78d08-171">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="78d08-172">错误</span><span class="sxs-lookup"><span data-stu-id="78d08-172">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="78d08-173">事件</span><span class="sxs-lookup"><span data-stu-id="78d08-173">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="78d08-174">*常规*</span><span class="sxs-lookup"><span data-stu-id="78d08-174">*General*</span></span><br><span data-ttu-id="78d08-175">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="78d08-175">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="78d08-176">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="78d08-176">*Clipboard*</span></span><br><span data-ttu-id="78d08-177">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="78d08-177">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="78d08-178">*输入*</span><span class="sxs-lookup"><span data-stu-id="78d08-178">*Input*</span></span><br><span data-ttu-id="78d08-179">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="78d08-179">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="78d08-180">*介质*</span><span class="sxs-lookup"><span data-stu-id="78d08-180">*Media*</span></span><br><span data-ttu-id="78d08-181">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="78d08-181">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="78d08-182"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保留属性，以配置事件名称和事件参数类型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="78d08-182"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="78d08-183">焦点</span><span class="sxs-lookup"><span data-stu-id="78d08-183">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="78d08-184">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="78d08-184">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="78d08-185">不包含对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="78d08-185">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="78d08-186">输入</span><span class="sxs-lookup"><span data-stu-id="78d08-186">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="78d08-187">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="78d08-187">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="78d08-188">键盘</span><span class="sxs-lookup"><span data-stu-id="78d08-188">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="78d08-189">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="78d08-189">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="78d08-190">鼠标</span><span class="sxs-lookup"><span data-stu-id="78d08-190">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="78d08-191">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="78d08-191">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="78d08-192">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="78d08-192">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="78d08-193">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="78d08-193">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="78d08-194">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="78d08-194">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="78d08-195">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="78d08-195">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="78d08-196">进度</span><span class="sxs-lookup"><span data-stu-id="78d08-196">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="78d08-197">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="78d08-197">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="78d08-198">触控</span><span class="sxs-lookup"><span data-stu-id="78d08-198">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="78d08-199">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="78d08-199">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="78d08-200"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 表示触控敏感型设备上的单个接触点。</span><span class="sxs-lookup"><span data-stu-id="78d08-200"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="78d08-201">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="78d08-201">For more information, see the following resources:</span></span>

* [<span data-ttu-id="78d08-202">ASP.NET Core 引用源（dotnet/aspnetcore `main` 分支）中的 `EventArgs` 类</span><span class="sxs-lookup"><span data-stu-id="78d08-202">`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `main` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* <span data-ttu-id="78d08-203">[MDN Web 文档：GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含关于哪些 HTML 元素支持每个 DOM 事件的信息。</span><span class="sxs-lookup"><span data-stu-id="78d08-203">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

::: moniker range=">= aspnetcore-6.0"

### <a name="custom-event-arguments"></a><span data-ttu-id="78d08-204">自定义事件参数</span><span class="sxs-lookup"><span data-stu-id="78d08-204">Custom event arguments</span></span>

<span data-ttu-id="78d08-205">Blazor 支持自定义事件参数。借助这些参数，你可以将任意数据通过自定义事件传递给 .NET 事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="78d08-205">Blazor supports custom event arguments, which enable you to pass arbitrary data to .NET event handlers with custom events.</span></span>

#### <a name="general-configuration"></a><span data-ttu-id="78d08-206">常规配置</span><span class="sxs-lookup"><span data-stu-id="78d08-206">General configuration</span></span>

<span data-ttu-id="78d08-207">通常，通过以下步骤启用具有自定义事件参数的自定义事件。</span><span class="sxs-lookup"><span data-stu-id="78d08-207">Custom events with custom event arguments are generally enabled with the following steps.</span></span>

1. <span data-ttu-id="78d08-208">在 JavaScript 中，定义一个函数，用于通过源事件生成自定义事件参数对象：</span><span class="sxs-lookup"><span data-stu-id="78d08-208">In JavaScript, define a function for building the custom event argument object from the source event:</span></span>

   ```javascript
   function eventArgsCreator(event) { 
     return {
       customProperty1: 'any value for property 1',
       customProperty2: event.srcElement.value
     };
   }
   ```

1. <span data-ttu-id="78d08-209">在 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 中（紧随 Blazor `<script>` 之后）使用前面的处理程序注册自定义事件：</span><span class="sxs-lookup"><span data-stu-id="78d08-209">Register the custom event with the preceding handler in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server) immediately after the Blazor `<script>`:</span></span>

   ```html
   <script>
       Blazor.registerCustomEventType('customevent', {
           createEventArgs: eventArgsCreator;
       });
   </script>
   ```

   > [!NOTE]
   > <span data-ttu-id="78d08-210">每个事件仅在脚本中调用 `registerCustomEventType` 一次。</span><span class="sxs-lookup"><span data-stu-id="78d08-210">The call to `registerCustomEventType` is performed in a script only once per event.</span></span>

1. <span data-ttu-id="78d08-211">定义事件参数的类：</span><span class="sxs-lookup"><span data-stu-id="78d08-211">Define a class for the event arguments:</span></span>

   ```csharp
   public class CustomEventArgs : EventArgs
   {
       public string CustomProperty1 {get; set;}
       public string CustomProperty2 {get; set;}
   }
   ```

1. <span data-ttu-id="78d08-212">通过为自定义事件添加 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 特性注释，连接自定义事件和事件参数。</span><span class="sxs-lookup"><span data-stu-id="78d08-212">Wire up the custom event with the event arguments by adding an <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> attribute annotation for the custom event.</span></span> <span data-ttu-id="78d08-213">此类不需要成员：</span><span class="sxs-lookup"><span data-stu-id="78d08-213">The class doesn't require members:</span></span>

   ```csharp
   [EventHandler("oncustomevent", typeof(CustomEventArgs), enableStopPropagation: true, enablePreventDefault: true)]
   static class EventHandlers
   {
   }
   ```

1. <span data-ttu-id="78d08-214">在一个或多个 HTML 元素上注册此事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="78d08-214">Register the event handler on one or more HTML elements.</span></span> <span data-ttu-id="78d08-215">在委托处理程序方法中访问从 Javascript 传入的数据：</span><span class="sxs-lookup"><span data-stu-id="78d08-215">Access the data that was passed in from Javascript in the delegate handler method:</span></span>

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

<span data-ttu-id="78d08-216">每当在 DOM 上触发此自定义事件时，都会使用从 Javascript 传入的数据调用此事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="78d08-216">Whenever the custom event is fired on the DOM, the event handler is called with the data passed from the Javascript.</span></span>

<span data-ttu-id="78d08-217">如果正在尝试触发自定义事件，则必须将 [`bubbles`](https://developer.mozilla.org/docs/Web/API/Event/bubbles) 的值设置为 `true` 以启用它。</span><span class="sxs-lookup"><span data-stu-id="78d08-217">If you're attempting to fire a custom event, [`bubbles`](https://developer.mozilla.org/docs/Web/API/Event/bubbles) must be enabled by setting its value to `true`.</span></span> <span data-ttu-id="78d08-218">否则，事件无法到达 Blazor 处理程序，进而无法受到处理并进入 C# 自定义 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-218">Otherwise, the event doesn't reach the Blazor handler for processing into the C# custom <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> method.</span></span> <span data-ttu-id="78d08-219">有关详细信息，请参阅 [MDN Web 文档：事件冒泡](https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events#event_bubbling)。</span><span class="sxs-lookup"><span data-stu-id="78d08-219">For more information, see [MDN Web Docs: Event bubbling](https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events#event_bubbling).</span></span>

#### <a name="custom-clipboard-paste-event-example"></a><span data-ttu-id="78d08-220">自定义剪贴板粘贴事件示例</span><span class="sxs-lookup"><span data-stu-id="78d08-220">Custom clipboard paste event example</span></span>

<span data-ttu-id="78d08-221">下面的示例接收包含粘贴时间和用户所粘贴文本的自定义剪贴板粘贴事件。</span><span class="sxs-lookup"><span data-stu-id="78d08-221">The following example receives a custom clipboard paste event that includes the time of the paste and the user's pasted text.</span></span>

<span data-ttu-id="78d08-222">声明事件的自定义名称 (`oncustompaste`)，并声明一个 .NET 类 (`CustomPasteEventArgs`) 以保存此事件的事件参数：</span><span class="sxs-lookup"><span data-stu-id="78d08-222">Declare a custom name (`oncustompaste`) for the event and a .NET class (`CustomPasteEventArgs`) to hold the event arguments for this event:</span></span>

<span data-ttu-id="78d08-223">`CustomEvents.cs`:</span><span class="sxs-lookup"><span data-stu-id="78d08-223">`CustomEvents.cs`:</span></span>

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

<span data-ttu-id="78d08-224">添加 JavaScript 代码以为 <xref:System.EventArgs> 子类提供数据。</span><span class="sxs-lookup"><span data-stu-id="78d08-224">Add JavaScript code to supply data for the <xref:System.EventArgs> subclass.</span></span> <span data-ttu-id="78d08-225">在 `wwwroot/index.html` 或 `Pages/_Host.cshtml` 文件中，紧随 Blazor 脚本之后添加以下 `<script>` 标记和内容。</span><span class="sxs-lookup"><span data-stu-id="78d08-225">In the `wwwroot/index.html` or `Pages/_Host.cshtml` file, add the following `<script>` tag and content immediately after the Blazor script.</span></span> <span data-ttu-id="78d08-226">下面的示例仅处理粘贴文本，但你可以使用任意 JavaScript API 来处理用户粘贴的其他类型的数据（如图像）。</span><span class="sxs-lookup"><span data-stu-id="78d08-226">The following example only handles pasting text, but you could use arbitrary JavaScript APIs to deal with users pasting other types of data, such as images.</span></span>

<span data-ttu-id="78d08-227">紧随 Blazor 脚本之后的 `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server)：</span><span class="sxs-lookup"><span data-stu-id="78d08-227">`wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server) immediately after the Blazor script:</span></span>

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

<span data-ttu-id="78d08-228">前面的代码会在本机 [`paste`](https://developer.mozilla.org/docs/Web/API/Element/paste_event) 事件发生时告诉浏览器：</span><span class="sxs-lookup"><span data-stu-id="78d08-228">The preceding code tells the browser that when a native [`paste`](https://developer.mozilla.org/docs/Web/API/Element/paste_event) event occurs:</span></span>

* <span data-ttu-id="78d08-229">引发 `custompaste` 事件。</span><span class="sxs-lookup"><span data-stu-id="78d08-229">Raise a `custompaste` event.</span></span>
* <span data-ttu-id="78d08-230">使用所述的自定义逻辑提供事件参数数据：</span><span class="sxs-lookup"><span data-stu-id="78d08-230">Supply the event arguments data using the custom logic stated:</span></span>
  * <span data-ttu-id="78d08-231">对于 `eventTimestamp`，请创建新日期。</span><span class="sxs-lookup"><span data-stu-id="78d08-231">For the `eventTimestamp`, create a new date.</span></span>
  * <span data-ttu-id="78d08-232">对于 `pastedData`，请获取剪贴板数据的文本形式。</span><span class="sxs-lookup"><span data-stu-id="78d08-232">For the `pastedData`, get the clipboard data as text.</span></span> <span data-ttu-id="78d08-233">有关详细信息，请参阅 [MDN Web 文档：ClipboardEvent. clipboardData](https://developer.mozilla.org/docs/Web/API/ClipboardEvent/clipboardData)。</span><span class="sxs-lookup"><span data-stu-id="78d08-233">For more information, see [MDN Web Docs: ClipboardEvent.clipboardData](https://developer.mozilla.org/docs/Web/API/ClipboardEvent/clipboardData).</span></span>

<span data-ttu-id="78d08-234">.NET 和 JavaScript 的事件名称约定有所不同：</span><span class="sxs-lookup"><span data-stu-id="78d08-234">Event name conventions differ between .NET and JavaScript:</span></span>

* <span data-ttu-id="78d08-235">在 .NET 中，事件名称带有前缀“`on`”。</span><span class="sxs-lookup"><span data-stu-id="78d08-235">In .NET, event names are prefixed with "`on`".</span></span>
* <span data-ttu-id="78d08-236">在 JavaScript 中，事件名称不带前缀。</span><span class="sxs-lookup"><span data-stu-id="78d08-236">In JavaScript, event names don't have a prefix.</span></span>

<span data-ttu-id="78d08-237">在 Razor 组件中，将自定义处理程序附加到元素中。</span><span class="sxs-lookup"><span data-stu-id="78d08-237">In a Razor component, attach the custom handler to an element.</span></span>

<span data-ttu-id="78d08-238">`Pages/CustomPasteArguments.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-238">`Pages/CustomPasteArguments.razor`:</span></span>

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

## <a name="lambda-expressions"></a><span data-ttu-id="78d08-239">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="78d08-239">Lambda expressions</span></span>

<span data-ttu-id="78d08-240">支持 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)作为委托事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="78d08-240">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) are supported as the delegate event handler.</span></span>

<span data-ttu-id="78d08-241">`Pages/EventHandlerExample4.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-241">`Pages/EventHandlerExample4.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

<span data-ttu-id="78d08-242">使用 C# 方法参数关闭附加值通常很方便，例如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="78d08-242">It's often convenient to close over additional values using C# method parameters, such as when iterating over a set of elements.</span></span> <span data-ttu-id="78d08-243">下面的示例创建三个按钮，每个按钮都调用 `UpdateHeading` 并传递以下数据：</span><span class="sxs-lookup"><span data-stu-id="78d08-243">The following example creates three buttons, each of which calls `UpdateHeading` and passes the following data:</span></span>

* <span data-ttu-id="78d08-244">`e` 中的事件参数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>)。</span><span class="sxs-lookup"><span data-stu-id="78d08-244">An event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) in `e`.</span></span>
* <span data-ttu-id="78d08-245">`buttonNumber` 中的按钮编号。</span><span class="sxs-lookup"><span data-stu-id="78d08-245">The button number in `buttonNumber`.</span></span>

<span data-ttu-id="78d08-246">`Pages/EventHandlerExample5.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-246">`Pages/EventHandlerExample5.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="78d08-247">不要直接在 Lambda 表达式中使用循环变量，如前面的 `for` 循环示例中的 `i`。</span><span class="sxs-lookup"><span data-stu-id="78d08-247">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="78d08-248">否则，所有 Lambda 表达式将使用相同的变量，这将导致在所有 Lambda 中使用相同的值。</span><span class="sxs-lookup"><span data-stu-id="78d08-248">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="78d08-249">始终在局部变量中捕获该变量的值，然后使用该值。</span><span class="sxs-lookup"><span data-stu-id="78d08-249">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="78d08-250">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="78d08-250">In the preceding example:</span></span>
>
> * <span data-ttu-id="78d08-251">将循环变量 `i` 分配到 `buttonNumber`。</span><span class="sxs-lookup"><span data-stu-id="78d08-251">The loop variable `i` is assigned to `buttonNumber`.</span></span>
> * <span data-ttu-id="78d08-252">将 `buttonNumber` 用于 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="78d08-252">`buttonNumber` is used in the lambda expression.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="78d08-253">EventCallback</span><span class="sxs-lookup"><span data-stu-id="78d08-253">EventCallback</span></span>

<span data-ttu-id="78d08-254">嵌套组件的一个常见方案：在子组件事件发生时执行父组件的方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-254">A common scenario with nested components executes a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="78d08-255">子组件中发生的 `onclick` 事件是一个常见用例。</span><span class="sxs-lookup"><span data-stu-id="78d08-255">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="78d08-256">若要跨组件公开事件，请使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="78d08-256">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="78d08-257">父组件可向子组件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 分配回调方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-257">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="78d08-258">下面的 `Child` 组件演示如何设置按钮的 `onclick` 处理程序以从示例的 `ParentComponent` 接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 委托。</span><span class="sxs-lookup"><span data-stu-id="78d08-258">The following `Child` component demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="78d08-259"><xref:Microsoft.AspNetCore.Components.EventCallback> 是用 `MouseEventArgs` 键入的，这适用于来自外围设备的 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="78d08-259">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device.</span></span>

<span data-ttu-id="78d08-260">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-260">`Shared/Child.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

<span data-ttu-id="78d08-261">`Parent` 组件将子级的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 设置为其 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-261">The `Parent` component sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="78d08-262">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-262">`Pages/Parent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

<span data-ttu-id="78d08-263">在 `ChildComponent` 中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="78d08-263">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="78d08-264">调用 `Parent` 组件的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="78d08-264">The `Parent` component's `ShowMessage` method is called.</span></span> <span data-ttu-id="78d08-265">`message` 更新并显示在 `Parent` 组件中。</span><span class="sxs-lookup"><span data-stu-id="78d08-265">`message` is updated and displayed in the `Parent` component.</span></span>
* <span data-ttu-id="78d08-266">回调方法 (`ShowMessage`) 中不需要对 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) 的调用。</span><span class="sxs-lookup"><span data-stu-id="78d08-266">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="78d08-267">自动调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以重新呈现 `Parent` 组件，就像子事件触发组件重新呈现于在子级中执行的事件处理程序中一样。</span><span class="sxs-lookup"><span data-stu-id="78d08-267"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `Parent` component, just as child events trigger component rerendering in event handlers that execute within the child.</span></span> <span data-ttu-id="78d08-268">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="78d08-268">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="78d08-269"><xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="78d08-269"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="78d08-270"><xref:Microsoft.AspNetCore.Components.EventCallback> 是弱类型，允许将任何类型参数传入 `InvokeAsync(Object)`。</span><span class="sxs-lookup"><span data-stu-id="78d08-270"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="78d08-271"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 是强类型，需要将 `T` 参数传入可分配到 `TValue` 的 `InvokeAsync(T)` 中。</span><span class="sxs-lookup"><span data-stu-id="78d08-271"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="78d08-272">使用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> 调用 <xref:Microsoft.AspNetCore.Components.EventCallback> 或 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 并等待 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="78d08-272">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="78d08-273">使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 处理事件和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="78d08-273">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="78d08-274">优先使用强类型 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 而非 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="78d08-274">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="78d08-275"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 向用户提供更好的组件错误反馈。</span><span class="sxs-lookup"><span data-stu-id="78d08-275"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides enhanced error feedback to users of the component.</span></span> <span data-ttu-id="78d08-276">与其他 UI 事件处理程序类似，指定事件参数是可选操作。</span><span class="sxs-lookup"><span data-stu-id="78d08-276">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="78d08-277">当没有值传递给回调时，使用 <xref:Microsoft.AspNetCore.Components.EventCallback>。</span><span class="sxs-lookup"><span data-stu-id="78d08-277">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="78d08-278">阻止默认操作</span><span class="sxs-lookup"><span data-stu-id="78d08-278">Prevent default actions</span></span>

<span data-ttu-id="78d08-279">使用 [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 指令特性防止事件的默认操作，其中 `{DOM EVENT}` 占位符是[文档对象模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。</span><span class="sxs-lookup"><span data-stu-id="78d08-279">Use the [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event, where the `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events).</span></span>

<span data-ttu-id="78d08-280">在输入设备上选择某个键并且元素焦点位于某个文本框上时，浏览器通常在该文本框中显示该键的字符。</span><span class="sxs-lookup"><span data-stu-id="78d08-280">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="78d08-281">在下面的示例中，通过指定 `@onkeydown:preventDefault` 指令属性来阻止默认行为。</span><span class="sxs-lookup"><span data-stu-id="78d08-281">In the following example, the default behavior is prevented by specifying the `@onkeydown:preventDefault` directive attribute.</span></span> <span data-ttu-id="78d08-282">当焦点位于 `<input>` 元素上时，计数器随着按 Shift++<kbd></kbd><kbd></kbd> 按键顺序而递增。</span><span class="sxs-lookup"><span data-stu-id="78d08-282">When the focus is on the `<input>` element, the counter increments with the key sequence <kbd>Shift</kbd>+<kbd>+</kbd>.</span></span> <span data-ttu-id="78d08-283">不会将 `+` 字符分配到 `<input>` 元素的值。</span><span class="sxs-lookup"><span data-stu-id="78d08-283">The `+` character isn't assigned to the `<input>` element's value.</span></span> <span data-ttu-id="78d08-284">有关 `keydown` 的详细信息，请参阅 [`MDN Web Docs: Document: keydown` 事件](https://developer.mozilla.org/docs/Web/API/Document/keydown_event)。</span><span class="sxs-lookup"><span data-stu-id="78d08-284">For more information on `keydown`, see [`MDN Web Docs: Document: keydown` event](https://developer.mozilla.org/docs/Web/API/Document/keydown_event).</span></span>

<span data-ttu-id="78d08-285">`Pages/EventHandlerExample6.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-285">`Pages/EventHandlerExample6.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

<span data-ttu-id="78d08-286">指定没有值的 `@on{DOM EVENT}:preventDefault` 属性等同于 `@on{DOM EVENT}:preventDefault="true"`。</span><span class="sxs-lookup"><span data-stu-id="78d08-286">Specifying the `@on{DOM EVENT}:preventDefault` attribute without a value is equivalent to `@on{DOM EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="78d08-287">此特性也允许使用表达式值。</span><span class="sxs-lookup"><span data-stu-id="78d08-287">An expression is also a permitted value of the attribute.</span></span> <span data-ttu-id="78d08-288">在下面的示例中，`shouldPreventDefault` 是设置为 `true` 或 `false` 的 `bool` 字段：</span><span class="sxs-lookup"><span data-stu-id="78d08-288">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeydown:preventDefault="shouldPreventDefault" />

...

@code {
    private bool shouldPreventDefault = true;
}
```

## <a name="stop-event-propagation"></a><span data-ttu-id="78d08-289">停止事件传播</span><span class="sxs-lookup"><span data-stu-id="78d08-289">Stop event propagation</span></span>

<span data-ttu-id="78d08-290">使用 [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 指令特性来停止事件传播，其中 `{DOM EVENT}` 占位符是[文档对象模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。</span><span class="sxs-lookup"><span data-stu-id="78d08-290">Use the [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation, where the `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events).</span></span>

<span data-ttu-id="78d08-291">在下例中，选中复选框可阻止第二个子级 `<div>` 中的单击事件传播到父级 `<div>`。</span><span class="sxs-lookup"><span data-stu-id="78d08-291">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`.</span></span> <span data-ttu-id="78d08-292">由于传播的单击事件通常会触发 `OnSelectParentDiv` 方法，因此，如果未选中复选框，则选择第二个子级 `<div>` 会导致父级 div 消息出现。</span><span class="sxs-lookup"><span data-stu-id="78d08-292">Since propagated click events normally fire the `OnSelectParentDiv` method, selecting the second child `<div>` results in the parent div message appearing unless the check box is selected.</span></span>

<span data-ttu-id="78d08-293">`Pages/EventHandlerExample7.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-293">`Pages/EventHandlerExample7.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="78d08-294">聚焦元素</span><span class="sxs-lookup"><span data-stu-id="78d08-294">Focus an element</span></span>

<span data-ttu-id="78d08-295">在[元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上调用 <xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A> 来将代码中的元素作为焦点。</span><span class="sxs-lookup"><span data-stu-id="78d08-295">Call <xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A> on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code.</span></span> <span data-ttu-id="78d08-296">在下面的示例中，选择按钮可将 `<input>` 元素作为焦点。</span><span class="sxs-lookup"><span data-stu-id="78d08-296">In the following example, select the button to focus the `<input>` element.</span></span>

<span data-ttu-id="78d08-297">`Pages/EventHandlerExample8.razor`:</span><span class="sxs-lookup"><span data-stu-id="78d08-297">`Pages/EventHandlerExample8.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample8.razor?highlight=16)]

::: moniker-end
