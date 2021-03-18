---
title: ASP.NET Core Blazor 组件虚拟化
author: guardrex
description: 了解如何在 ASP.NET Core Blazor 应用中使用组件虚拟化。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2021
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
uid: blazor/components/virtualization
ms.openlocfilehash: c81732c29b262e9134a4ff7dab077a4f31db96af
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109814"
---
# <a name="aspnet-core-blazor-component-virtualization"></a>ASP.NET Core Blazor 组件虚拟化

使用 Blazor 框架的内置虚拟化支持和 [`Virtualize` 组件](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)提高组件呈现的感知性能。 虚拟化是一种技术，用于将 UI 呈现限制为仅当前可见的部分。 例如，当应用必须呈现项的长列表，并且在任何给定的时间只需要一小部分项可见时，虚拟化很有帮助。

使用 `Virtualize` 组件的情形：

* 在循环中呈现一组数据项。
* 由于滚动，大多数项不可见。
* 呈现的项的大小相同。

当用户滚动到 `Virtualize` 组件的项列表中的任意点时，组件将计算要显示的可见项。 看不见的项将不会呈现。

如果不使用虚拟化，典型列表可能会使用 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来呈现列表中的每一项。 如下示例中：

* `allFlights` 是飞机航班的集合。
* `FlightSummary` 组件显示有关每个航班的详细信息。
* [`@key` 指令属性](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)按照航班 `FlightId` 保留每个 `FlightSummary` 组件与其呈现的航班之间的关系。

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

如果该集合包含数千个航班，则呈现这些航班会花费较长时间，并且用户会感觉到明显的 UI 滞后。 大多数航班不会呈现，因为它们超出了 `<div>` 元素的高度。

相反，不要一次性呈现整个航班列表，而是将上一个示例中的 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环替换为 `Virtualize` 组件：

* 将 `allFlights` 指定为 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> 的固定项源。 `Virtualize` 组件仅呈现当前可见的航班。
* 使用 `Context` 参数为每个航班指定上下文。 在下面的示例中，`flight` 用作上下文，负责提供对每个航班成员的访问。

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

如果未使用 `Context` 参数指定上下文，可以在项内容模板中使用 `context` 的值来访问每个航班的成员：

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

`Virtualize` 组件：

* 根据容器的高度和呈现的项的大小来计算要呈现的项数。
* 随着用户滚动，重新计算并重新呈现项。
* 仅从与当前可见区域相对应的外部 API 中获取记录切片，而不是下载集合中的所有数据。

`Virtualize` 组件的项内容可以包括：

* 纯 HTML 和 Razor 代码，如前面的示例所示。
* 一个或多个 Razor 组件。
* HTML/Razor 和 Razor 组件的组合。

## <a name="item-provider-delegate"></a>项提供程序委托

如果不想将所有项加载到内存中，可向组件的 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 参数指定项提供程序委托方法，以按需异步检索请求的项。 在下面的示例中，`LoadEmployees` 方法向 `Virtualize` 组件提供项：

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

项提供程序接收 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>，它指定从特定起始索引开始的请求项数。 然后，项提供程序在数据库或其他服务中检索请求的项，并以 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> 形式将这些项与项总数一起返回。 项提供程序可以选择按每个请求检索项，也可以将项缓存以便后续使用。

`Virtualize` 组件只能从其参数中接受一个项源，因此，请不要尝试在使用项提供程序的同时为 `Items` 分配集合。 如果同时分配两个，则在运行时设置组件的参数时，将引发 <xref:System.InvalidOperationException>。

以下 `LoadEmployees` 方法示例从 `EmployeeService` 加载员工（未显示）：

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> 指示组件从其 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> 重新请求数据。 当外部数据更改时，这很有用。 使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> 时，无需调用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A>。

## <a name="placeholder"></a>占位符

由于从远程数据源请求项可能需要一些时间，你可选择呈现包含项内容的占位符：

* 使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) 显示内容，直到项数据可用。
* 使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> 设置列表的项模板。

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>项大小

每个项的像素高度可以使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> 进行设置（默认值：50）。 下面的示例将每个项的高度从默认值 50 像素更改为 25 像素：

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

默认情况下，`Virtualize` 组件在初始呈现执行 *后* 测量各个项的呈现大小（高度）。 使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 来提前提供准确的项大小，以帮助实现准确的初始呈现性能，并确保页面重载时处于正确滚动位置。 如果默认值 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 导致某些项在当前可见视图之外呈现，则会触发第二次重新呈现。 若要在虚拟化列表中正确保持浏览器的滚动位置，初始呈现必须正确。 否则，用户可能会看到错误的项。

## <a name="overscan-count"></a>溢出扫描计数

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> 确定在可见区域之前和之后呈现的额外项数。 此设置有助于降低滚动期间的呈现频率。 但是，值越大，页面中呈现的元素越多（默认值：3）。 下面的示例将 OverscanCount 从默认值三个项更改为四个项：

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>状态更改

当对 `Virtualize` 组件呈现的项进行更改时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以强制重新计算和重新呈现组件。 有关详细信息，请参阅 <xref:blazor/components/rendering>。
