---
title: ASP.NET Core Razor 组件
author: guardrex
description: 了解如何在 Blazor 应用中创建和使用 Razor 组件，包括有关组件中的 Razor 语法、组件命名、命名空间和组件参数的指导。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2021
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
uid: blazor/components/index
ms.openlocfilehash: 723142ab59511392e6fc6d779537bb3cfa01eaa3
ms.sourcegitcommit: 79126facbd939cd21bd3efd02dd5daa4985c6d6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715746"
---
# <a name="aspnet-core-razor-components"></a>ASP.NET Core Razor 组件

Blazor 应用是使用 Razor 组件构建的。 组件是用户界面 (UI) 的自包含部分，具有用于启用动态行为的处理逻辑。 组件可在项目之间嵌套、重复使用和共享。

## <a name="component-classes"></a>组件类

组件是使用 C# 和 HTML 标记的组合在 [Razor](xref:mvc/views/razor) 组件文件（文件扩展名为 `.razor`）中实现的。

### <a name="razor-syntax"></a>Razor 语法

组件使用 [Razor 语法](xref:mvc/views/razor)。 组件广泛使用了两个 Razor 功能，即指令和指令特性 。 这两个功能是前缀为 `@` 的保留关键字，出现在 Razor 标记中：

* [指令](xref:mvc/views/razor#directives)：更改组件标记的分析或运行方式。 例如，[`@page`][9] 指令使用路由模板指定可路由组件，可以由用户请求在浏览器中按特定 URL 直接访问。
* [指令特性](xref:mvc/views/razor#directive-attributes)：更改组件元素的分析方式或运行方式。 例如，`<input>` 元素的 [`@bind`][10] 指令特性会将数据绑定到元素的值。

本文和 Blazor 文档集的其他文章中进一步说明了在组件中使用的指令和指令特性。 有关 Razor 语法的一般信息，请参阅 <xref:mvc/views/razor>。

### <a name="names"></a>名称

组件的名称必须以大写字符开头：

* `ProductDetail.razor` 有效。
* `productDetail.razor` 无效。

整个 Blazor 文档中使用的常见 Blazor 命名约定包括：

* 组件文件路径使用 Pascal 大小写 &dagger;，在显示组件代码示例之前出现。 路径指示典型文件夹位置。 例如，`Pages/ProductDetail.razor` 指示 `ProductDetail` 组件具有文件名 `ProductDetail.razor`，并位于应用的 `Pages` 文件夹中。
* 可路由组件的组件文件路径与其 URL 匹配，对于组件路由模板中各单词之间的空格会显示连字符。 例如，在浏览器中，通过相对 URL `/product-detail` 请求具有路由模板 `/product-detail` (`@page "/product-detail"`) 的 `ProductDetail` 组件。

&dagger;Pascal 大小写（大写 camel 形式）是不带空格和标点符号的命名约定，其中每个单词的首字母大写（包括第一个单词）。

### <a name="routing"></a>路由

可以通过使用 [`@page`][9] 指令为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。 编译具有 [`@page`][9] 指令的 Razor 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>。 在运行时，路由器将使用 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 搜索组件类，并呈现具有与请求的 URL 匹配的路由模板的任何组件。

以下 `HelloWorld` 组件使用路由模板 `/hello-world`。 可通过相对 URL `/hello-world` 访问组件呈现的网页。 当使用默认协议、主机和端口在本地运行 Blazor 应用时，会在浏览器中通过 `https://localhost:5001/hello-world` 请求 `HelloWorld` 组件。 生成网页的组件通常位于 `Pages` 文件夹中，但可以使用任何文件夹保存组件（包括在嵌套文件夹中）。

`Pages/HelloWorld.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/HelloWorld.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/HelloWorld.razor)]

::: moniker-end

前面的组件在浏览器中通过 `/hello-world` 进行加载，无论是否将组件添加到应用的 UI 导航。 （可选）组件可以添加到 `NavMenu` 组件，以便在应用基于 UI 的导航中显示组件链接。

对于前面的 `HelloWorld` 组件，将以下 `NavLink` 组件添加到 `NavMenu` 组件。 在无序列表标记 `<ul>...</ul>` 之间，将 `NavLink` 组件添加到新列表项中 (`<li>...</li>`)。

`Shared/NavMenu.razor`:

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="hello-world">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Hello World!
    </NavLink>
</li>
```

有关详细信息（包括 `NavLink` 和 `NavMenu` 组件的说明），请参阅 <xref:blazor/fundamentals/routing>。

### <a name="markup"></a>标记

组件的 UI 使用由 Razor 标记、C# 和 HTML 组成的 [Razor 语法](xref:mvc/views/razor)进行定义。 在编译应用时，HTML 标记和 C# 呈现逻辑转换为组件类。 生成的类的名称与文件名匹配。

组件类的成员在一个或多个 [`@code`][1] 块中定义。 在 [`@code`][1] 块中，组件状态使用 C# 进行指定和处理：

* 属性和字段初始化表达式。
* 由父组件和路由参数传递的自变量的参数值。
* 用于用户事件处理、生命周期事件和自定义组件逻辑的方法。

组件成员使用以 `@` 符号开头的 C# 表达式在呈现逻辑中进行使用。 例如，通过为字段名称添加 `@` 前缀来呈现 C# 字段。 下面 `Markup` 示例计算并呈现：

* `headingFontStyle`，表示标题元素的 CSS 属性值 `font-style`。
* `headingText`，表示标题元素的内容。

`Pages/Markup.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/Markup.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/Markup.razor)]

::: moniker-end

> [!NOTE]
> Blazor 文档中的示例会为私有成员指定 [`private` 访问修饰符](/dotnet/csharp/language-reference/keywords/private)。 私有成员的范围限定为组件的类。 但是，C# 会在没有访问修饰符存在时采用 `private` 访问修饰符，因此在自己的代码中将成员显式标记为“`private`”是可选的。 有关访问修饰符的详细信息，请参阅[访问修饰符（C# 编程指南）](/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)。

Blazor 框架在内部将组件作为[呈现树](https://developer.mozilla.org/docs/Web/Performance/How_browsers_work#render)进行处理，该树是组件的[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 和[级联样式表对象模型 (CSSOM)](https://developer.mozilla.org/docs/Web/API/CSS_Object_Model) 的组合。 最初呈现组件后，会重新生成组件的呈现树以响应事件。 Blazor 会将新呈现树与以前的呈现树进行比较，并将所有修改应用于浏览器的 DOM 以进行显示。 有关更多信息，请参见<xref:blazor/components/rendering>。

组件是普通 [C# 类](/dotnet/csharp/programming-guide/classes-and-structs/classes)，可以放置在项目中的任何位置。 生成网页的组件通常位于 `Pages` 文件夹中。 非页面组件通常放置在 `Shared` 文件夹或添加到项目的自定义文件夹中。

### <a name="nested-components"></a>嵌套组件

通过使用 HTML 语法声明组件，组件可以包含其他组件。 使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。

请考虑以下 `Heading` 组件，其他组件可以使用该组件显示标题。

`Shared/Heading.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/Heading.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/Heading.razor)]

::: moniker-end

`HeadingExample` 组件中的以下标记会在 `<Heading />` 标记出现的位置呈现前面的 `Heading` 组件。

`Pages/HeadingExample.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/HeadingExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/HeadingExample.razor)]

::: moniker-end

如果某个组件包含一个 HTML 元素，该元素的大写首字母与相同命名空间中的组件名称不匹配，则会发出警告，指示该元素名称异常。 为组件的命名空间添加 [`@using`][2] 指令使组件可用，从而解决此警告。 有关详细信息，请参阅[命名空间](#namespaces)部分。

此部分中显示的 `Heading` 组件示例没有 [`@page`][9] 指令，因此用户无法在浏览器中通过直接请求直接访问 `Heading` 组件。 但是，具有 [`@page`][9] 指令的任何组件都可以嵌套在另一个组件中。 如果通过在 Razor 文件顶部包含 `@page "/heading"` 可直接访问 `Heading`组件，则会在 `/heading` 和 `/heading-example` 处为浏览器请求呈现该组件。

### <a name="namespaces"></a>命名空间

通常，组件的命名空间是从应用的根命名空间和该组件在应用内的位置（文件夹）派生而来的。 如果应用的根命名空间是 `BlazorSample`，并且 `Counter` 组件位于 `Pages` 文件夹中：

* `Counter` 组件的命名空间为 `BlazorSample.Pages`。
* 组件的完全限定类型名称为 `BlazorSample.Pages.Counter`。

对于保存组件的自定义文件夹，将 [`@using`][2] 指令添加到父组件或应用的 `_Imports.razor` 文件。 下面的示例提供 `Components` 文件夹中的组件：

```razor
@using BlazorSample.Components
```

> [!NOTE]
> `_Imports.razor` 文件中的 [`@using`][2] 指令仅适用于 Razor 文件 (`.razor`)，而不适用于 C# 文件 (`.cs`)。

还可以使用其完全限定的名称来引用组件，而不需要 [`@using`][2] 指令。 以下示例直接引用应用的 `Components` 文件夹中的 `ProductDetail` 组件：

```razor
<BlazorSample.Components.ProductDetail />
```

使用 Razor 创建的组件的命名空间基于以下内容（按优先级顺序）：

* Razor 文件标记中的 [`@namespace`][8] 指令（例如 `@namespace BlazorSample.CustomNamespace`）。
* 项目文件中项目的 `RootNamespace`（例如 `<RootNamespace>BlazorSample</RootNamespace>`）。
* 项目名称，取自项目文件的文件名 (`.csproj`)，以及从项目根到组件的路径。 例如，框架将具有项目命名空间 `BlazorSample` (`BlazorSample.csproj`) 的 `{PROJECT ROOT}/Pages/Index.razor` 解析到 `Index` 组件的命名空间 `BlazorSample.Pages`。 `{PROJECT ROOT}` 是项目根路径。 组件遵循 C# 名称绑定规则。 对于本示例中的 `Index` 组件，范围内的组件是所有组件：
  * 在同一文件夹 `Pages` 中。
  * 未显式指定其他命名空间的项目根中的组件。

不支持以下项目：

* `global::` 限定。
* 导入具有别名 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 语句的组件。 例如，`@using Foo = Bar` 不受支持。
* 部分限定的名称。 例如，无法将 `@using BlazorSample` 添加到组件中，然后使用 `<Shared.NavMenu></Shared.NavMenu>` 在应用的 `Shared` 文件夹中引用 `NavMenu` 组件 (`Shared/NavMenu.razor`)。

### <a name="partial-class-support"></a>分部类支持

组件以 [C# 分部类](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)的形式生成，使用以下任一方法进行创作：

* 单个文件包含在一个或多个 [`@code`][1] 块、HTML 标记和 Razor 标记中定义的 C# 代码。 Blazor 项目模板使用此单文件方法来定义其组件。
* HTML 和 Razor 标记位于 Razor 文件 (`.razor`) 中。 C# 代码位于定义为分部类的代码隐藏文件 (`.cs`) 中。

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 定义特定于组件的样式的组件样式表是单独的文件 (`.css`)。 Blazor CSS 隔离以后在 <xref:blazor/components/css-isolation>中进行介绍。

::: moniker-end

下面的示例显示了从 Blazor 项目模板生成的应用中具有 [`@code`][1] 块的默认 `Counter` 组件。 标记和 C# 代码位于同一个文件中。 这是在创作组件时采用的最常见方法。

`Pages/Counter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/Counter.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/Counter.razor)]

::: moniker-end

以下 `Counter` 组件使用带有分部类的代码隐藏文件从 C# 代码中拆分 HTML 和 Razor 标记：

`Pages/CounterPartialClass.razor`:

```razor
@page "/counter-partial-class"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Pages/CounterPartialClass.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class CounterPartialClass
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

`_Imports.razor` 文件中的 [`@using`][2] 指令仅适用于 Razor 文件 (`.razor`)，而不适用于 C# 文件 (`.cs`)。 根据需要将命名空间添加到分部类文件中。

组件使用的典型命名空间：

::: moniker range=">= aspnetcore-5.0"

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
using Microsoft.AspNetCore.Components.Web.Virtualization;
using Microsoft.JSInterop;
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
using Microsoft.JSInterop;
```

::: moniker-end

典型命名空间还包含应用的命名空间以及与应用的 `Shared` 文件夹对应的命名空间：

```csharp
using BlazorSample;
using BlazorSample.Shared;
```

### <a name="specify-a-base-class"></a>指定基类

[`@inherits`][6] 指令用于指定组件的基类。 下面的示例演示组件如何继承基类以提供组件的属性和方法。 `BlazorRocksBase` 基类派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase>。

`Pages/BlazorRocks.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/BlazorRocks.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/BlazorRocks.razor)]

::: moniker-end

`BlazorRocksBase.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/BlazorRocksBase.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/BlazorRocksBase.cs)]

::: moniker-end

## <a name="component-parameters"></a>组件参数

组件参数将数据传递给组件，使用组件类中包含 [`[Parameter]` 特性](xref:Microsoft.AspNetCore.Components.ParameterAttribute)的公共 [C# 属性](/csharp/programming-guide/classes-and-structs/properties)进行定义。 在下面的示例中，内置引用类型 (<xref:System.String?displayProperty=fullName>) 和用户定义的引用类型 (`PanelBody`) 作为组件参数进行传递。

`PanelBody.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/PanelBody.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/PanelBody.cs)]

::: moniker-end

`Shared/ParameterChild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/ParameterChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/ParameterChild.razor)]

::: moniker-end

> [!WARNING]
> 支持为组件参数提供初始值，但不会创建在首次呈现后向自身参数写入的组件。 有关详细信息，请参阅本文的[重写参数](#overwritten-parameters)部分。

`ParameterChild` 组件的 `Title` 和 `Body` 组件参数通过自变量在呈现组件实例的 HTML 标记中进行设置。 以下 `ParameterParent` 组件会呈现两个 `ParameterChild` 组件：

* 第一个 `ParameterChild` 组件在呈现时不提供参数自变量。
* 第二个 `ParameterChild` 组件从 `ParameterParent` 组件接收 `Title` 和 `Body` 的值，后者使用[显式 C# 表达式](xref:mvc/views/razor#explicit-razor-expressions)设置 `PanelBody` 的属性值。

`Pages/ParameterParent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ParameterParent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ParameterParent.razor)]

::: moniker-end

当 `ParameterParent` 组件未提供组件参数值时，来自 `ParameterParent` 组件的以下呈现 HTML 标记会显示 `ParameterChild` 组件默认值。 当 `ParameterParent` 组件提供组件参数值时，它们会替换 `ParameterChild` 组件的默认值。

> [!NOTE]
> 为进行阐明，呈现 CSS 样式类未显示在以下呈现 HTML 标记中。

```html
<h1>Child component (without attribute values)</h1>

<div>
    <div>Set By Child</div>
    <div>Set by child.</div>
</div>

<h1>Child component (with attribute values)</h1>

<div>
    <div>Set by Parent</div>
    <div>Set by parent.</div>
</div>
```

使用 [Razor 的保留 `@` 符号](xref:mvc/views/razor#razor-syntax)，将方法的 C# 字段、属性或结果作为 HTML 特性值分配给组件参数。 以下 `ParameterParent2` 组件显示前面 `ParameterChild` 组件的四个实例，并将其 `Title` 参数值设置为：

* `title` 字段的值。
* `GetTitle` C# 方法的结果。
* 带有<xref:System.DateTime.ToLongDateString%2A> 的长格式当前本地日期，使用[隐式 C# 表达式](xref:mvc/views/razor#implicit-razor-expressions)。
* `panelData` 对象的 `Title` 属性。

`Pages/ParameterParent2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ParameterParent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ParameterParent2.razor)]

::: moniker-end

> [!NOTE]
> 将 C# 成员分配给组件参数时，请使用符号 `@` 为成员添加前缀，绝不要为参数的 HTML 特性添加前缀。
>
> 正确：
>
> ```razor
> <ParameterChild Title="@title" />
> ```
>
> 不正确：
>
> ```razor
> <ParameterChild @Title="title" />
> ```

与 Razor 页面 (`.cshtml`) 不同，在呈现组件时，Blazor 不能在 Razor 表达式中执行异步工作。 这是因为 Blazor 是为呈现交互式 UI 而设计的。 在交互式 UI 中，屏幕必须始终显示某些内容，因此阻止呈现流是没有意义的。 相反，异步工作是在一个[异步生命周期事件](xref:blazor/components/lifecycle)期间执行的。 在每个异步生命周期事件之后，组件可能会再次呈现。 不支持以下 Razor 语法：

```razor
<ParameterChild Title="@await ..." />
```

生成应用时，前面示例中的代码会生成编译器错误：

> “await”运算符只能用于异步方法中。 请考虑用“async”修饰符标记此方法，并将其返回类型更改为“Task”。

若要在前面的示例中异步获取 `Title` 参数的值，组件可以使用 [`OnInitializedAsync` 生命周期事件](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)，如以下示例所示：

```razor
<ParameterChild Title="@title" />

@code {
    private string title;
    
    protected override async Task OnInitializedAsync()
    {
        title = await ...;
    }
}
```

有关更多信息，请参见<xref:blazor/components/lifecycle>。

不支持使用显式 Razor 表达式连接文本和表达式结果以赋值给参数。 下面的示例尝试将文本“`Set by `”与对象属性值连接在一起。 尽管 Razor 页面 (`.cshtml`) 支持此语法，但对在组件中赋值给子级的 `Title` 参数无效。 不支持以下 Razor 语法：

```razor
<ParameterChild Title="Set by @(panelData.Title)" />
```

生成应用时，前面示例中的代码会生成编译器错误：

> 组件属性不支持复杂内容（混合 C# 和标记）。

若要支持组合值赋值，请使用方法、字段或属性。 下面的示例在 C# 方法 `GetTitle` 中将“`Set by `”与对象属性值连接在一起：

`Pages/ParameterParent3.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ParameterParent3.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ParameterParent3.razor)]

::: moniker-end

有关详细信息，请参阅 <xref:mvc/views/razor>。

> [!WARNING]
> 支持为组件参数提供初始值，但不会创建在首次呈现后向自身参数写入的组件。 有关详细信息，请参阅本文的[重写参数](#overwritten-parameters)部分。

应将组件参数声明为自动属性，这意味着它们不应在其 `get` 或 `set` 访问器中包含自定义逻辑。 例如，下面的 `StartData` 属性是自动属性：

```csharp
[Parameter]
public DateTime StartData { get; set; }
```

不要在 `get` 或 `set` 访问器中放置自定义逻辑，因为组件参数专门用作父组件向子组件传送信息的通道。 如果子组件属性的 `set` 访问器包含导致父组件重新呈现的逻辑，则会导致一个无限的呈现循环。

若要转换已接收的参数值，请执行以下操作：

* 将参数属性保留为自动属性，以表示所提供的原始数据。
* 创建另一个属性或方法，用于基于参数属性提供转换后的数据。

重写 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set-onparameterssetasync) 以在每次收到新数据时转换接收到的参数。

支持将初始值写入组件参数，因为初始值赋值不会干扰 Blazor 的自动组件呈现。 在组件中，使用 <xref:System.DateTime.Now?displayProperty=nameWithType> 将当前本地 <xref:System.DateTime> 赋予 `StartData` 是有效语法：

```csharp
[Parameter]
public DateTime StartData { get; set; } = DateTime.Now;
```

进行 <xref:System.DateTime.Now?displayProperty=nameWithType> 的初始赋值之后，请勿在开发人员代码中向 `StartData` 赋值。 有关详细信息，请参阅本文的[重写参数](#overwritten-parameters)部分。

## <a name="route-parameters"></a>路由参数

组件可以在 [`@page`][9] 指令的路由模板中指定路由参数。 [Blazor 路由器](xref:blazor/fundamentals/routing)使用路由参数来填充相应的组件参数。

::: moniker range=">= aspnetcore-5.0"

支持可选路由参数。 在下面的示例中，`text` 可选参数将 route 段的值赋给组件的 `Text` 属性。 如果该段不存在，则在 [`OnInitialized` 生命周期方法](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)中将 `Text` 的值设置为 `fantastic`。

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/RouteParameter.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/RouteParameter.razor?highlight=2,7-8)]

不支持可选路由参数，因此在前面的示例中应用了两个 [`@page`][9] 指令。 第一个 [`@page`][9] 指令允许导航到没有路由参数的组件。 第二个 [`@page`][9] 指令会接收 `{text}` 路由参数，并将值赋予 `Text` 属性。

::: moniker-end

要了解 catch-all 路由参数 `{*pageRoute}`（它可跨多个文件夹边界捕获路径），请参阅 <xref:blazor/fundamentals/routing#catch-all-route-parameters>。

## <a name="overwritten-parameters"></a>重写参数

Blazor 框架通常会施加安全的父级到子级参数的赋值：

* 不会意外覆盖参数。
* 最大程度地减少副作用。 例如，可避免附加的呈现，因为它们可能会创建无限的呈现循环。

当父组件重新呈现时，子组件会接收可能覆盖现有值的新参数值。 当开发带有一个或多个数据绑定参数的组件，并且开发人员直接写入子组件中的参数时，经常会发生意外覆盖子组件中的参数值：

* 子组件通过父组件中的一个或多个参数值呈现。
* 子级直接写入参数的值。
* 父组件重新呈现并覆盖子参数的值。

覆盖参数值的可能性也会延伸到子组件的属性 `set` 访问器中。

> [!IMPORTANT]
> 我们的通用指南不是创建在首次呈现后直接向自身参数写入的组件。

请考虑使用以下有故障的 `Expander` 组件，它们会：

* 呈现子内容。
* 切换以使用组件参数 (`Expanded`) 显示子内容。
* 组件直接写入 `Expanded` 参数，该参数演示了覆盖的参数存在的问题，应避免这样做。

在下面的 `Expander` 组件演示了此方案的不正确方法之后，会显示修改后的 `Expander` 组件以演示正确的方法。 下面的示例可以放置在本地示例应中，以体验所述的行为。

`Shared/Expander.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/BadExpander.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/BadExpander.razor)]

::: moniker-end

`Expander` 组件会添加到可调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 的以下 `ExpanderExample` 父组件中：

* 在开发人员代码中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会向组件通知其状态已更改，并且通常会触发组件呈现以更新 UI。 以后在 <xref:blazor/components/lifecycle>和 <xref:blazor/components/rendering>中会更详细地讨论 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。
* 按钮的 `@onclick` 指令特性会将事件处理程序附加到按钮的 `onclick` 事件。 以后在 <xref:blazor/components/event-handling>中会更详细地讨论事件处理。

`Pages/ExpanderExample.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ExpanderExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ExpanderExample.razor)]

::: moniker-end

最初，在切换 `Expanded` 属性时，`Expander` 组件独立地作出行为。 子组件会按预期方式维护其状态。 在父组件中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 时，第一个子组件的 `Expanded` 会重新重置为它的初始值 (`true`)。 第二个 `Expander` 组件的 `Expanded` 值不会重置，因为第二个组件中没有呈现任何子内容。

要维持在前述情况中的状态，请在 `Expander` 组件中使用私有字段来保留它的切换状态。

以下经修定的 `Expander` 组件：

* 接受父项中的 `Expanded` 组件参数值。
* 将组件参数值分配给 [`OnInitialized` 事件](xref:blazor/components/lifecycle#component-initialization-oninitializedasync)中的私有字段 (`expanded`)。
* 使用私有字段来维护其内部切换状态，该状态演示如何避免直接写入参数。

> [!NOTE]
> 此部分中的建议可扩展到组件参数 `set` 访问器中的类似逻辑，这可能会产生类似的不良副作用。

`Shared/Expander.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/Expander.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/Expander.razor)]

::: moniker-end

有关其他信息，请参阅 [Blazor 双向绑定错误 (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599)。

## <a name="child-content"></a>子内容

组件可以设置另一个组件的内容。 分配组件提供子组件的开始标记与结束标记之间的内容。

在下面的示例中，`RenderFragmentChild` 组件具有一个 `ChildContent` 属性，它将要呈现的 UI 段表示为 <xref:Microsoft.AspNetCore.Components.RenderFragment>。 `ChildContent` 在组件 Razor 标记中的位置是在最终 HTML 输出中呈现内容的位置。

`Shared/RenderFragmentChild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/RenderFragmentChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/RenderFragmentChild.razor)]

::: moniker-end

> [!IMPORTANT]
> 必须按约定将接收 <xref:Microsoft.AspNetCore.Components.RenderFragment> 内容的属性命名为 `ChildContent`。

以下 `RenderFragmentParent` 组件通过将内容置于子组件的开始标记和结束标记内，来提供用于呈现 `RenderFragmentChild` 的内容。

`Pages/RenderFragmentParent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/RenderFragmentParent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/RenderFragmentParent.razor)]

::: moniker-end

由于 Blazor 呈现子内容的方式，如果在 `RenderFragmentChild` 组件的内容中使用递增循环变量，则在 [`for`](/dotnet/csharp/language-reference/keywords/for) 循环内呈现组件需要本地索引变量。 下面的示例可以添加到前面的 `RenderFragmentParent` 组件：

```razor
<h1>Three children with an index variable</h1>

@for (int c = 0; c < 3; c++)
{
    var current = c;

    <RenderFragmentChild>
        Count: @current
    </RenderFragmentChild>
}
```

或者，将 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环与 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> 结合使用，而不是使用 [`for`](/dotnet/csharp/language-reference/keywords/for) 循环。 下面的示例可以添加到前面的 `RenderFragmentParent` 组件：

```razor
<h1>Second example of three children with an index variable</h1>

@foreach (var c in Enumerable.Range(0,3))
{
    <RenderFragmentChild>
        Count: @c
    </RenderFragmentChild>
}
```

若要了解如何将 <xref:Microsoft.AspNetCore.Components.RenderFragment> 用作组件 UI 的模板，请参阅以下文章：

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a>属性展开和任意参数

除了组件的声明参数外，组件还可以捕获和呈现其他属性。 其他特性可以在字典中捕获，然后在使用 [`@attributes`][3] Razor 指令特性呈现组件时，将其展开到元素上。 对于定义生成支持各种自定义项的标记元素的组件，此方案非常有用。 例如，为支持多个参数的 `<input>` 单独定义属性可能比较繁琐。

在下面的 `Splat` 组件中：

* 第一个 `<input>` 元素 (`id="useIndividualParams"`) 使用单个组件参数。
* 第二个 `<input>` 元素 (`id="useAttributesDict"`) 使用特性展开。

`Pages/Splat.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/Splat.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/Splat.razor)]

::: moniker-end

网页中呈现的 `<input>` 元素是相同的：

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

若要接受任意特性，请定义[组件参数](#component-parameters)，并将 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性设置为 `true`：

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

借助 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 上的 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性，参数可以匹配所有不匹配其他任何参数的特性。 组件只能使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 定义单个参数。 与 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 一起使用的属性类型必须可以使用字符串键从 [`Dictionary<string, object>`](xref:System.Collections.Generic.Dictionary%602) 中分配。 使用 [`IEnumerable<KeyValuePair<string, object>>`](xref:System.Collections.Generic.IEnumerable%601) 或 [`IReadOnlyDictionary<string, object>`](xref:System.Collections.Generic.IReadOnlyDictionary%602) 也是此方案中的选项。

相对于元素特性位置的 [`@attributes`][3] 位置很重要。 在元素上展开 [`@attributes`][3] 时，将从右到左（从最后一个到第一个）处理特性。 请考虑以下使用子组件的父组件示例：

`Shared/AttributeOrderChild1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild1.razor)]

::: moniker-end

`Pages/AttributeOrderParent1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent1.razor)]

::: moniker-end

`AttributeOrderChild1` 组件的 `extra` 属性设置为 [`@attributes`][3] 右侧。 通过附加特性传递时，`AttributeOrderParent1` 组件的呈现的 `<div>` 包含 `extra="5"`，因为特性是从右到左（从最后一个到第一个）处理的：

```html
<div extra="5" />
```

在下面的示例中，`extra` 和 [`@attributes`][3] 的顺序在子组件的 `<div>` 中反转：

`Shared/AttributeOrderChild2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/AttributeOrderChild2.razor)]

::: moniker-end

`Pages/AttributeOrderParent2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/AttributeOrderParent2.razor)]

::: moniker-end

通过附加特性传递时，父组件呈现的网页中的 `<div>` 包含 `extra="10"`：

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>捕获对组件的引用

组件引用提供了一种引用组件实例以便发出命令的方法。 若要捕获组件引用，请执行以下操作：

* 向子组件添加 [`@ref`][4] 特性。
* 定义与子组件类型相同的字段。

呈现组件时，将用组件实例填充字段。 然后，可以在实例上调用 .NET 方法。

请考虑以下 `ReferenceChild` 组件，它会在调用其 `ChildMethod` 时记录消息。

`Shared/ReferenceChild.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/ReferenceChild.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/ReferenceChild.razor)]

::: moniker-end

组件引用仅在呈现组件后才进行填充，其输出包含 `ReferenceChild` 的元素。 在呈现组件之前，没有任何可引用的内容。

若要在组件完成呈现后操作组件引用，请使用 [`OnAfterRender` 或 `OnAfterRenderAsync` 方法](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)。

若要结合使用事件处理程序和引用变量，请使用 Lambda 表达式，或在 [`OnAfterRender` 或 `OnAfterRenderAsync` 方法](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)中分配事件处理程序委托。 这可确保在分配事件处理程序之前先分配引用变量。

以下 lambda 方法使用前面的 `ReferenceChild` 组件。

`Pages/ReferenceParent1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent1.razor)]

::: moniker-end

以下委托方法使用前面的 `ReferenceChild` 组件。

`Pages/ReferenceParent2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent2.razor)]

::: moniker-end

在循环中使用集合引用组件。 如下示例中：

* 组件会添加到 <xref:System.Collections.Generic.List%601> 中。
* 会为每个组件创建一个按钮，它通过其在 `ChildMethod` 中的组件索引触发相应组件的 <xref:System.Collections.Generic.List%601>。

`Pages/ReferenceParent3.razor` 使用前面的 `ReferenceChild` 组件：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent3.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ReferenceParent3.razor)]

::: moniker-end

尽管捕获组件引用使用与[捕获元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)类似的语法，但捕获组件引用不是 JavaScript 互操作功能。 组件引用不会传递给 JavaScript 代码。 组件引用只在 .NET 代码中使用。

> [!IMPORTANT]
> 不要使用组件引用来改变子组件的状态。 请改用常规声明性组件参数将数据传递给子组件。 使用组件参数使子组件在正确的时间自动重新呈现。 有关详细信息，请参阅[组件参数](#component-parameters)部分和文章 <xref:blazor/components/data-binding>。

## <a name="synchronization-context"></a>同步上下文

Blazor 使用同步上下文 (<xref:System.Threading.SynchronizationContext>) 来强制执行单个逻辑线程。 组件的[生命周期方法](xref:blazor/components/lifecycle)和 Blazor 引发的事件回调都在此同步上下文上执行。

Blazor Server的同步上下文尝试模拟单线程环境，使其与浏览器中的单线程 WebAssembly 模型紧密匹配。 在任意给定的时间点，工作只在一个线程上执行，这会造成单个逻辑线程的印象。 不会同时执行两个操作。

### <a name="avoid-thread-blocking-calls"></a>避免阻止线程的调用

通常，不要在组件中调用以下方法。 以下方法阻止执行线程，进而阻止应用继续工作，直到基础 <xref:System.Threading.Tasks.Task> 完成：

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

> [!NOTE]
> 使用此部分中所述的线程阻止方法的 Blazor 文档示例只是使用方法进行演示，而不是用作建议编码指导。 例如，一些组件代码演示通过调用 <xref:System.Threading.Thread.Sleep%2A?displayProperty=nameWithType> 来模拟长时间运行的进程。

### <a name="invoke-component-methods-externally-to-update-state"></a>在外部调用组件方法以更新状态

如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法，它将代码执行调度到 Blazor 的同步上下文。 例如，请考虑以下通告程序服务，它可向任何侦听组件通知更新的状态。 可以从应用中的任何位置调用 `Update` 方法。

`Notifier.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Notifier.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Notifier.cs)]

::: moniker-end

注册 `Notifier` 服务：

* 在 Blazor WebAssembly 应用中，在 `Program.Main` 中将服务注册为单一实例：

  ```csharp
  builder.Services.AddSingleton<Notifier>();
  ```

* 在 Blazor Server 应用中，在 `Startup.ConfigureServices` 中将服务注册为有作用域：

  ```csharp
  services.AddScoped<Notifier>();
  ```

使用 `Notifier` 服务更新组件。

`Pages/NotifierExample.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/NotifierExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/NotifierExample.razor)]

::: moniker-end

在上面的示例中：

* `Notifier` 在 Blazor 的同步上下文之外调用组件的 `OnNotify` 方法。 `InvokeAsync` 用于切换到正确的上下文，并将呈现排入队列。 有关详细信息，请参阅 <xref:blazor/components/rendering>。
* 组件会实现 <xref:System.IDisposable>。 `OnNotify` 委托在 `Dispose` 方法中取消订阅，在释放组件时，框架会调用此方法。 有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>使用 \@ 键控制是否保留元素和组件

在呈现元素或组件的列表并且元素或组件随后发生更改时，Blazor 必须决定之前的元素或组件中有哪些可以保留，以及模型对象应如何映射到这些元素或组件。 通常，此过程是自动的，可以忽略，但在某些情况下，可能需要控制该过程。

请考虑使用以下 `Details` 和 `People` 组件：

* `Details` 组件从 `People` 父组件接收数据 `Data`，这些数据会显示在 `<input>` 元素中。 当用户选择一个 `<input>` 元素时，显示的任何给定 `<input>` 元素都可以从用户接收页面焦点。
* `People` 组件使用 `Details` 组件创建人员对象列表以进行显示 。 每三秒向集合中添加一个新人员。

此演示使你可以：

* 从多个呈现的 `Details` 组件中选择一个 `<input>`。
* 随着人员集合自动增长，研究页面焦点的行为。

`Shared/Details.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/index/Details.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/index/Details.razor)]

::: moniker-end

在以下 `People` 组件中，在 `OnTimerCallback` 中添加人员的每个迭代都会导致 Blazor 重新生成整个集合。 页面的焦点保持在 `<input>` 元素的相同索引位置处，因此每次添加人员时，焦点都会移动。 将焦点从用户选择的内容移开是不可取的行为。 使用以下组件演示不良行为后，使用 [`@key`][5] 指令特性改善用户的体验。

`Pages/People.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/People.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/People.razor)]

::: moniker-end

`people` 集合的内容会随插入、删除或重新排序的条目而更改。 重新呈现可能会导致可见的行为差异。 每次向 `people` 集合中插入一个人员时，当前具有焦点的元素的前一个元素便会接收焦点。 用户的焦点会丢失。

可通过 [`@key`][5] 指令特性来控制元素或组件到集合的映射过程。 使用 [`@key`][5] 可保证基于键的值保留元素或组件。 如果前面示例中的 `Details` 组件在 `person` 项上键入，则 Blazor 会忽略重新呈现未更改的 `Details` 组件。

若要修改 `People` 组件以将 [`@key`][5] 指令特性用于 `people` 集合，请将 `<Details>` 元素更新为以下内容：

```razor
<Details @key="person" Data="@person.Data" />
```

当 `people` 集合发生更改时，会保留 `Details` 实例与 `person` 实例之间的关联。 当在集合的开头插入 `Person` 时，会在相应位置插入一个新的 `Details` 实例。 其他实例保持不变。 因此，在将人员添加到集合时，用户的焦点不会丢失。

使用 [`@key`][5] 指令特性时，其他集合更新会表现出相同的行为：

* 如果从集合中删除实例，则仅从 UI 中删除相应的组件实例。 其他实例保持不变。
* 如果对集合项进行重新排序，则保留相应的组件实例，并在 UI 中重新排序。

> [!IMPORTANT]
> 对于每个容器元素或组件而言，键是本地的。 不会在整个文档中全局地比较键。

### <a name="when-to-use-key"></a>何时使用 \@key

通常，每当呈现列表（例如，在 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 块中）以及存在适当的值定义 [`@key`][5] 时，都可以使用 [`@key`][5]。

在对象未更改时，也可以使用 [`@key`][5] 保留元素或组件子树，如下面的示例所示。

示例 1：

```razor
<li @key="person">
    <input value="@person.Data" />
</li>
```

示例 2：

```razor
<div @key="person">
    @* other HTML elements *@
</div>
```

如果 `person` 实例更改，则 [`@key`][5] 特性指令会强制 Blazor：

* 放弃整个 `<li>` 或 `<div>` 及其后代。
* 在 UI 中使用新元素和组件重新生成子树。

这有助于保证在子树中的集合发生更改时不保留 UI 状态。

### <a name="when-not-to-use-key"></a>何时不使用 \@key

使用 [`@key`][5] 进行呈现时，会产生性能成本。 性能成本不是很大，但仅在保留元素或组件对应用有利的情况下才指定 [`@key`][5]。

即使未使用 [`@key`][5]，Blazor 也会尽可能地保留子元素和组件实例。 使用 [`@key`][5] 的唯一优点是控制如何将模型实例映射到保留的组件实例，而不是选择映射的 Blazor。

### <a name="values-to-use-for-key"></a>要用于 \@key 的值

通常，为 [`@key`][5] 提供以下值之一：

* 模型对象实例。 例如，在前面的示例中使用了 `Person` 实例 (`person`)。 这可确保基于对象引用相等性的保留。
* 唯一标识符。 例如，唯一标识符可以基于类型为 `int`、`string` 或 `Guid` 的主键值。

确保用于 [`@key`][5] 的值不冲突。 如果在同一父元素内检测到冲突值，则 Blazor 引发异常，因为它无法明确地将旧元素或组件映射到新元素或组件。 仅使用非重复值，例如对象实例或主键值。

## <a name="apply-an-attribute"></a>应用属性

可以通过 [`@attribute`][7] 指令将特性应用于组件。 下面的示例将 [`[Authorize]` 特性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)应用于组件的类：

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>条件 HTML 元素属性

HTML 元素特性属性基于 .NET 值有条件地设置。 如果值为 `false` 或 `null`，则属性未设置。 如果值为 `true`，则属性已设置。

在下面的示例中，`IsCompleted` 确定是否设置了 `<input>` 元素的 `checked` 属性。

`Pages/ConditionalAttribute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/ConditionalAttribute.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/ConditionalAttribute.razor)]

::: moniker-end

有关详细信息，请参阅 <xref:mvc/views/razor>。

> [!WARNING]
> .NET 类型为 `bool` 时，某些 HTML 属性（如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）无法正常运行。 在这些情况下，请使用 `string` 类型，而不是 `bool`。

## <a name="raw-html"></a>原始 HTML

通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文字文本。 若要呈现原始 HTML，请将 HTML 内容包装在 <xref:Microsoft.AspNetCore.Components.MarkupString> 值中。 将该值分析为 HTML 或 SVG，并插入到 DOM 中。

> [!WARNING]
> 呈现从任何不受信任的源构造的原始 HTML 存在安全风险，应始终避免 。

下面的示例演示如何使用 <xref:Microsoft.AspNetCore.Components.MarkupString> 类型向组件的呈现输出添加静态 HTML 内容块。

`Pages/MarkupStringExample.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/MarkupStringExample.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/MarkupStringExample.razor)]

::: moniker-end

## <a name="razor-templates"></a>Razor 模板

可以使用 Razor 模板语法定义呈现片段，从而定义 UI 片段。 Razor 模板使用以下格式：

```razor
@<{HTML tag}>...</{HTML tag}>
```

下面的示例演示如何在组件中指定 <xref:Microsoft.AspNetCore.Components.RenderFragment> 和 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 值并直接呈现模板。 还可以将呈现片段作为参数传递给[模板化组件](xref:blazor/components/templated-components)。

`Pages/RazorTemplate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/index/RazorTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/index/RazorTemplate.razor)]

::: moniker-end

以上代码的呈现输出：

```html
<p>The time is 4/19/2021 8:54:46 AM.</p>
<p>Pet: Nutty Rex</p>
```

## <a name="static-assets"></a>静态资产

Blazor 遵循 ASP.NET Core 应用对于静态资产的约定。 静态资产位于项目的 [`web root` (`wwwroot`) 文件夹](xref:fundamentals/index#web-root)中或是 `wwwroot` 文件夹下的文件夹中。

使用基相对路径 (`/`) 来引用静态资产的 Web 根。 在下面的示例中，`logo.png` 实际位于 `{PROJECT ROOT}/wwwroot/images` 文件夹中。 `{PROJECT ROOT}` 是应用的项目根。

```razor
<img alt="Company logo" src="/images/logo.png" />
```

组件不支持波浪符斜杠表示法 (`~/`)。

有关设置应用基路径的信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。

## <a name="tag-helpers-arent-supported-in-components"></a>组件中不支持标记帮助程序

组件中不支持 [`Tag Helpers`](xref:mvc/views/tag-helpers/intro)。 若要在 Blazor 中提供类似标记帮助程序的功能，请创建一个具有与标记帮助程序相同功能的组件，并改为使用该组件。

## <a name="scalable-vector-graphics-svg-images"></a>可缩放的向量图形 (SVG) 图像

由于 Blazor 呈现 HTML，因此通过 `<img>` 标记支持浏览器支持的图像，包括可缩放的矢量图形 (SVG) 图像(`.svg`)：

```html
<img alt="Example image" src="image.svg" />
```

同样，样式表文件 (`.css`) 的 CSS 规则支持 SVG 图像：

```css
.element-class {
    background-image: url("image.svg");
}
```

但是，并非在所有情况下都支持内联的 SVG 标记。 如果将 `<svg>` 标记直接放入 Razor 文件 (`.razor`)，则支持基本图像呈现，但很多高级场景尚不受支持。 例如，当前未遵循 `<use>` 标记，并且 [`@bind`][10] 不能与某些 SVG 标记一起使用。 有关详细信息，请参阅 [Blazor (dotnet/aspnetcore #18271) 中的 SVG 支持](https://github.com/dotnet/aspnetcore/issues/18271)。

## <a name="whitespace-rendering-behavior"></a>空白的呈现行为

::: moniker range=">= aspnetcore-5.0"

除非将 [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) 指令与值 `true` 一起使用，否则在以下情况下默认删除额外的空白：

* 元素中的前导或尾随空白。
* <xref:Microsoft.AspNetCore.Components.RenderFragment>/<xref:Microsoft.AspNetCore.Components.RenderFragment%601> 参数中的前导或尾随（例如，传递到另一个组件的子内容）。
* 在 C# 代码块（例如 `@if` 和 `@foreach`）之前或之后。

但是，在使用 CSS 规则（例如 `white-space: pre`）时，删除空白可能会影响呈现输出。 若要禁用此性能优化并保留空白，请执行以下任一操作：

* 将 `@preservewhitespace true` 指令添加到 Razor 文件 (`.razor`) 的顶部，从而将首选项应用于特定组件。
* 将 `@preservewhitespace true` 指令添加到 `_Imports.razor` 文件中，从而将首选项应用于子目录或整个项目。

在大多数情况下，不需要执行任何操作，因为应用程序通常会继续正常运行（但速度会更快）。 如果去除空白会导致特定组件出现呈现问题，请在该组件中使用 `@preservewhitespace true` 来禁用此优化。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

空白将保留在组件的源标记中。 即使在没有视觉效果的情况下，只有空白的文本也会呈现在浏览器的 DOM 中。

请考虑以下组件标记：

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

前面的示例呈现以下不必要的空白：

* 在 `@foreach` 代码块外。
* 围绕 `<li>` 元素。
* 围绕 `@item.Text` 输出。

100 项的列表会导致超过 400 个空白区域。 任何额外空白都不会在视觉上影响呈现的输出。

呈现组件的静态 HTML 时，不会保留标记中的空白。 例如，查看组件 Razor 文件 (`.razor`) 中以下 `<img>` 标记的呈现输出：

```razor
<img     alt="Example image"   src="img.png"     />
```

不会保留前面标记中的空白：

```razor
<img alt="Example image" src="img.png" />
```

::: moniker-end

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
