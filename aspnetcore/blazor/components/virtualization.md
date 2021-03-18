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
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="5eb1c-103">ASP.NET Core Blazor 组件虚拟化</span><span class="sxs-lookup"><span data-stu-id="5eb1c-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="5eb1c-104">使用 Blazor 框架的内置虚拟化支持和 [`Virtualize` 组件](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)提高组件呈现的感知性能。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support with the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span></span> <span data-ttu-id="5eb1c-105">虚拟化是一种技术，用于将 UI 呈现限制为仅当前可见的部分。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="5eb1c-106">例如，当应用必须呈现项的长列表，并且在任何给定的时间只需要一小部分项可见时，虚拟化很有帮助。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span>

<span data-ttu-id="5eb1c-107">使用 `Virtualize` 组件的情形：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-107">Use the `Virtualize` component when:</span></span>

* <span data-ttu-id="5eb1c-108">在循环中呈现一组数据项。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-108">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="5eb1c-109">由于滚动，大多数项不可见。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-109">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="5eb1c-110">呈现的项的大小相同。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-110">The rendered items are the same size.</span></span>

<span data-ttu-id="5eb1c-111">当用户滚动到 `Virtualize` 组件的项列表中的任意点时，组件将计算要显示的可见项。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-111">When the user scrolls to an arbitrary point in the `Virtualize` component's list of items, the component calculates the visible items to show.</span></span> <span data-ttu-id="5eb1c-112">看不见的项将不会呈现。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-112">Unseen items aren't rendered.</span></span>

<span data-ttu-id="5eb1c-113">如果不使用虚拟化，典型列表可能会使用 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来呈现列表中的每一项。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in a list.</span></span> <span data-ttu-id="5eb1c-114">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-114">In the following example:</span></span>

* <span data-ttu-id="5eb1c-115">`allFlights` 是飞机航班的集合。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-115">`allFlights` is a collection of airplane flights.</span></span>
* <span data-ttu-id="5eb1c-116">`FlightSummary` 组件显示有关每个航班的详细信息。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-116">The `FlightSummary` component displays details about each flight.</span></span>
* <span data-ttu-id="5eb1c-117">[`@key` 指令属性](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)按照航班 `FlightId` 保留每个 `FlightSummary` 组件与其呈现的航班之间的关系。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-117">The [`@key` directive attribute](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) preserves the relationship of each `FlightSummary` component to its rendered flight by the flight's `FlightId`.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="5eb1c-118">如果该集合包含数千个航班，则呈现这些航班会花费较长时间，并且用户会感觉到明显的 UI 滞后。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-118">If the collection contains thousands of flights, rendering the flights takes a long time and users experience a noticeable UI lag.</span></span> <span data-ttu-id="5eb1c-119">大多数航班不会呈现，因为它们超出了 `<div>` 元素的高度。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-119">Most of the flights aren't rendered because they fall outside of the height of the `<div>` element.</span></span>

<span data-ttu-id="5eb1c-120">相反，不要一次性呈现整个航班列表，而是将上一个示例中的 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环替换为 `Virtualize` 组件：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-120">Instead of rendering the entire list of flights at once, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop in the preceding example with the `Virtualize` component:</span></span>

* <span data-ttu-id="5eb1c-121">将 `allFlights` 指定为 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> 的固定项源。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-121">Specify `allFlights` as a fixed item source to <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="5eb1c-122">`Virtualize` 组件仅呈现当前可见的航班。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-122">Only the currently visible flights are rendered by the `Virtualize` component.</span></span>
* <span data-ttu-id="5eb1c-123">使用 `Context` 参数为每个航班指定上下文。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-123">Specify a context for each flight with the `Context` parameter.</span></span> <span data-ttu-id="5eb1c-124">在下面的示例中，`flight` 用作上下文，负责提供对每个航班成员的访问。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-124">In the following example, `flight` is used as the context, which provides access to each flight's members.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="5eb1c-125">如果未使用 `Context` 参数指定上下文，可以在项内容模板中使用 `context` 的值来访问每个航班的成员：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-125">If a context isn't specified with the `Context` parameter, use the value of `context` in the item content template to access each flight's members:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="5eb1c-126">`Virtualize` 组件：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-126">The `Virtualize` component:</span></span>

* <span data-ttu-id="5eb1c-127">根据容器的高度和呈现的项的大小来计算要呈现的项数。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-127">Calculates the number of items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="5eb1c-128">随着用户滚动，重新计算并重新呈现项。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-128">Recalculates and rerenders the items as the user scrolls.</span></span>
* <span data-ttu-id="5eb1c-129">仅从与当前可见区域相对应的外部 API 中获取记录切片，而不是下载集合中的所有数据。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-129">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="5eb1c-130">`Virtualize` 组件的项内容可以包括：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-130">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="5eb1c-131">纯 HTML 和 Razor 代码，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-131">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="5eb1c-132">一个或多个 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-132">One or more Razor components.</span></span>
* <span data-ttu-id="5eb1c-133">HTML/Razor 和 Razor 组件的组合。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-133">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="5eb1c-134">项提供程序委托</span><span class="sxs-lookup"><span data-stu-id="5eb1c-134">Item provider delegate</span></span>

<span data-ttu-id="5eb1c-135">如果不想将所有项加载到内存中，可向组件的 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 参数指定项提供程序委托方法，以按需异步检索请求的项。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-135">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="5eb1c-136">在下面的示例中，`LoadEmployees` 方法向 `Virtualize` 组件提供项：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-136">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="5eb1c-137">项提供程序接收 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>，它指定从特定起始索引开始的请求项数。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-137">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="5eb1c-138">然后，项提供程序在数据库或其他服务中检索请求的项，并以 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> 形式将这些项与项总数一起返回。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-138">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="5eb1c-139">项提供程序可以选择按每个请求检索项，也可以将项缓存以便后续使用。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-139">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="5eb1c-140">`Virtualize` 组件只能从其参数中接受一个项源，因此，请不要尝试在使用项提供程序的同时为 `Items` 分配集合。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-140">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="5eb1c-141">如果同时分配两个，则在运行时设置组件的参数时，将引发 <xref:System.InvalidOperationException>。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-141">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="5eb1c-142">以下 `LoadEmployees` 方法示例从 `EmployeeService` 加载员工（未显示）：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-142">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="5eb1c-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> 指示组件从其 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> 重新请求数据。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="5eb1c-144">当外部数据更改时，这很有用。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-144">This is useful when external data changes.</span></span> <span data-ttu-id="5eb1c-145">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> 时，无需调用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-145">There's no need to call <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="5eb1c-146">占位符</span><span class="sxs-lookup"><span data-stu-id="5eb1c-146">Placeholder</span></span>

<span data-ttu-id="5eb1c-147">由于从远程数据源请求项可能需要一些时间，你可选择呈现包含项内容的占位符：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-147">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="5eb1c-148">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) 显示内容，直到项数据可用。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-148">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="5eb1c-149">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> 设置列表的项模板。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-149">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="5eb1c-150">项大小</span><span class="sxs-lookup"><span data-stu-id="5eb1c-150">Item size</span></span>

<span data-ttu-id="5eb1c-151">每个项的像素高度可以使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> 进行设置（默认值：50）。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-151">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50).</span></span> <span data-ttu-id="5eb1c-152">下面的示例将每个项的高度从默认值 50 像素更改为 25 像素：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-152">The following example changes the height of each item from the default of 50 pixels to 25 pixels:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="5eb1c-153">默认情况下，`Virtualize` 组件在初始呈现执行 *后* 测量各个项的呈现大小（高度）。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-153">By default, the `Virtualize` component measures the rendering size (height) of individual items *after* the initial render occurs.</span></span> <span data-ttu-id="5eb1c-154">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 来提前提供准确的项大小，以帮助实现准确的初始呈现性能，并确保页面重载时处于正确滚动位置。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-154">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="5eb1c-155">如果默认值 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 导致某些项在当前可见视图之外呈现，则会触发第二次重新呈现。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-155">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="5eb1c-156">若要在虚拟化列表中正确保持浏览器的滚动位置，初始呈现必须正确。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-156">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="5eb1c-157">否则，用户可能会看到错误的项。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-157">If not, users might view the wrong items.</span></span>

## <a name="overscan-count"></a><span data-ttu-id="5eb1c-158">溢出扫描计数</span><span class="sxs-lookup"><span data-stu-id="5eb1c-158">Overscan count</span></span>

<span data-ttu-id="5eb1c-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> 确定在可见区域之前和之后呈现的额外项数。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="5eb1c-160">此设置有助于降低滚动期间的呈现频率。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-160">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="5eb1c-161">但是，值越大，页面中呈现的元素越多（默认值：3）。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-161">However, higher values result in more elements rendered in the page (default: 3).</span></span> <span data-ttu-id="5eb1c-162">下面的示例将 OverscanCount 从默认值三个项更改为四个项：</span><span class="sxs-lookup"><span data-stu-id="5eb1c-162">The following example changes the overscan count from the default of three items to four items:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="5eb1c-163">状态更改</span><span class="sxs-lookup"><span data-stu-id="5eb1c-163">State changes</span></span>

<span data-ttu-id="5eb1c-164">当对 `Virtualize` 组件呈现的项进行更改时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以强制重新计算和重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-164">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="5eb1c-165">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="5eb1c-165">For more information, see <xref:blazor/components/rendering>.</span></span>
