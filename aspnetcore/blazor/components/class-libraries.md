---
title: 使用 Razor 类库中的 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何将外部 Razor 类库中的组件包含在 Blazor 应用中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 02c5edeaaac97cefac8a39279fd4b6644a1535ab
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106563966"
---
# <a name="consume-aspnet-core-razor-components-from-razor-class-libraries"></a>使用 Razor 类库中的 ASP.NET Core Razor 组件

组件可在 [Razor 类库 (RCL)](xref:razor-pages/ui-class) 中跨项目共享。 在应用中包含组件和静态资产，它们来自：

* 解决方案中的另一个项目。
* 引用的 .NET 库。
* NuGet 程序包。

正如组件是常规的 .NET 类型一样，RCL 提供的组件也是普通的 .NET 程序集。

## <a name="create-an-rcl"></a>创建 RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建新项目。
1. 在“新建项目”对话框中，从 ASP.NET Core 项目模板列表中选择“Razor 类库” 。 选择“下一步”  。
1. 在“配置新项目”对话框的“项目名称”字段中提供项目名称，或接受默认项目名称 。 本主题中的示例使用项目名称 `ComponentLibrary`。 选择“创建”。
1. 在“创建新的 Razor 类库”对话框中，选择“创建” 。
1. 将 RCL 添加到一个解决方案：
   1. 打开解决方案。
   1. 在解决方案资源管理器中，右击解决方案。 选择“添加” > “现有项目” 。
   1. 导航到 RCL 的项目文件。
   1. 选择 RCL 的项目文件 (`.csproj`)。
1. 从应用中添加对 RCL 的引用：
   1. 右键单击该应用项目。 选择“添加” > “项目引用”。
   1. 选择 RCL 项目。 选择“确定”。

::: moniker range=">= aspnetcore-5.0"

如果在从模板生成 RCL 时选中了“支持页和视图”复选框以支持页面和视图，则执行以下操作：

* 使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* 在项目文件 (`.csproj`) 中添加以下 `SupportedPlatform` 项：

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  有关 `SupportedPlatform` 项的详细信息，请参阅 [Blazor WebAssembly 的浏览器兼容性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)部分。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

如果在从模板生成 RCL 时选中了“支持页面和视图”复选框，以支持页面和视图，则使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 创建新项目。
1. 在“Web 和控制台”下的边栏中选择“应用”。  在“ASP.NET Core”下，从显示的项目模板中选择“Razor 类库”。 选择“下一步”  。
1. 通过“目标框架”下拉列表选择库的目标框架。 选择“下一步”  。
1. 在“配置新的类库”对话框的“项目名称”字段中提供项目名称。  本主题中的示例使用项目名称 `ComponentLibrary`。 选择“创建”。
1. 将 RCL 添加到一个解决方案：
   1. 打开解决方案。
   1. 在解决方案资源管理器中，右击解决方案。 选择“添加” > “现有项目” 。
   1. 导航到 RCL 的项目文件。
   1. 选择 RCL 的项目文件 (`.csproj`)。
1. 从应用中添加对 RCL 的引用：
   1. 右键单击该应用项目。 选择“添加” > “引用” 。
   1. 选择 RCL 项目。 选择“确定”。

::: moniker range=">= aspnetcore-5.0"

如果在从模板生成 RCL 时选中了“支持页和视图”复选框以支持页面和视图，则执行以下操作：

* 使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* 在项目文件 (`.csproj`) 中添加以下 `SupportedPlatform` 项：

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  有关 `SupportedPlatform` 项的详细信息，请参阅 [Blazor WebAssembly 的浏览器兼容性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)部分。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

如果在从模板生成 RCL 时选中了“支持页面和视图”复选框，以支持页面和视图，则使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

1. 在命令行界面中通过 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令使用 Razor 类库项目模板 (`razorclasslib`)。 下面的示例使用 `-o|--output` 选项创建了 RCL 并将其命名为 `ComponentLibrary`。 执行命令时，自动创建包含 `ComponentLibrary` 的文件夹：

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

1. 若要将库添加到现有项目中，请在命令行界面中使用 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 命令。 在下面的命令中，`{PATH TO LIBRARY}` 占位符是库的项目文件夹的路径：

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

::: moniker range=">= aspnetcore-5.0"

如果在从模板生成 RCL 时使用了 `-s|--support-pages-and-views` 选项，以便支持页面和视图，则执行以下操作：

* 使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* 在项目文件 (`.csproj`) 中添加以下 `SupportedPlatform` 项：

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  有关 `SupportedPlatform` 项的详细信息，请参阅 [Blazor WebAssembly 的浏览器兼容性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)部分。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

如果在从模板生成 RCL 时使用了 `-s|--support-pages-and-views` 选项，以支持页面和视图，则使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

---

## <a name="consume-a-razor-component-from-an-rcl"></a>使用 RCL 中的 Razor 组件

若要在其他项目中使用 RCL 中的组件，则使用以下方法之一：

* 使用包含 RCL 命名空间的完整组件类型名称。
* 如果 Razor 的 [`@using`](xref:mvc/views/razor#using) 指令声明了 RCL 的命名空间，则可以使用不含 RCL 命名空间的名称添加各个组件。 使用以下方法：
  * 将 `@using` 指令添加到各个组件。
  * 在顶级 `_Imports.razor` 文件中包含 `@using` 指令，使库的组件可用于整个项目。 将指令添加到任何级别的 `_Imports.razor` 文件，将命名空间应用于文件夹中的单个组件或一组组件。 使用 `_Imports.razor` 文件时，各个组件不需要包含表示 RCL 命名空间的 `@using` 指令。

在下面的示例中，`ComponentLibrary` 是包含 `Component1` 组件的 RCL。 如果从 RCL 项目模板创建的是不支持页面和视图 RCL，则示例组件 `Component1` 组件会自动添加到其中。

> [!NOTE]
> 如果创建的 RCL 支持页面和视图，则在 RCL 中手动添加 `Component1` 组件及其静态资产，以便能够按照本文中的示例操作。 组件和静态资产如以下部分中所示。

`ComponentLibrary` RCL 中的 `Component1.razor`：

```razor
<div class="my-component">
    This component is defined in the <strong>ComponentLibrary</strong> package.
</div>
```

在使用 RCL 的应用中，使用其命名空间引用 `Component1` 组件，如下面的示例所示。

`Pages/ConsumeComponent1.razor`:

```razor
@page "/consume-component-1"

<h1>Consume component (full namespace example)</h1>

<ComponentLibrary.Component1 />
```

或者，不使用其命名空间，而是添加一个 [`@using`](xref:mvc/views/razor#using) 指令以使用该组件。 以下 `@using` 指令还可以出现在当前文件夹中或其上方的任何 `_Imports.razor` 文件中。

`Pages/ConsumeComponent2.razor`:

```razor
@page "/consume-component-2"
@using ComponentLibrary

<h1>Consume component (<code>@@using</code> example)</h1>

<Component1 />
```

::: moniker range=">= aspnetcore-5.0"

如果库组件使用 [CSS 隔离](xref:blazor/components/css-isolation)，组件样式会自动用于使用库的应用。 无需在使用库的应用中链接库的各个组件样式表。 在前面的示例中，`Component1` 的样式表 (`Component1.razor.css`) 将自动包含在内。

`ComponentLibrary` RCL 中的 `Component1.razor.css`：

```css
.my-component {
    border: 2px dashed red;
    padding: 1em;
    margin: 1em 0;
    background-image: url('background.png');
}
```

还会包含 RCL 项目模板中的背景图像，并将其保留在 RCL 的 `wwwroot` 文件夹中。

`ComponentLibrary` RCL 中的 `wwwroot/background.png`：

![来自 RCL 项目模板的带斜条纹的背景图像](class-libraries/_static/background.png)

::: moniker-end

::: moniker range=">= aspnetcore-6.0"

若要从库的 `wwwroot` 文件夹中的样式表中提供其他库组件样式，请使用框架的 `Link` 组件链接样式表。

下一个示例中使用了以下背景图像。 实现此部分中所示的示例后，请右键单击该图像以将其保存在本地。

`ComponentLibrary` RCL 中的 `wwwroot/extra-background.png`：

![开发人员添加到库中的带斜条纹的背景图像](class-libraries/_static/extra-background.png)

使用 `extra-style` 类向 RCL 添加新的样式表。

`ComponentLibrary` RCL 中的 `wwwroot/additionalStyles.css`：

```css
.extra-style {
    border: 2px dashed blue;
    padding: 1em;
    margin: 1em 0;
    background-image: url('extra-background.png');
}
```

将组件添加到使用 `extra-style` 类的 RCL。

`ComponentLibrary` RCL 中的 `ExtraStyles.razor`：

```razor
<Link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />

<div class="extra-style">
    <p>
        This component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

在使用 RCL 的 `ExtraStyles` 组件的应用中添加一个页面。

`Pages/ConsumeComponent3.razor`:

```razor
@page "/consume-component-3"
@using ComponentLibrary

<h1>Consume component (<code>additionalStyles.css</code> example)</h1>

<ExtraStyles />
```

在子组件中使用 `Link` 组件时，如果呈现 `Link` 组件的子组件，链接的资源就可用于父组件的任何其他子组件。

可在应用的 `Link` 标记中链接到库的样式表，来代替使用 `<head>` 组件。

`wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server)：

```diff
+ <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
```

在子组件中使用 `Link` 组件，与在 `wwwroot/index.html` 或 `Pages/_Host.cshtml` 中放置一个 `<link>` HTML 标记之间的区别是，框架组件已呈现的 HTML 标记：

* 可以根据应用程序状态进行修改。 不能根据应用程序状态修改硬编码 `<link>` HTML 标记。
* 将在不再呈现父组件的情况下从 HTML `<head>` 中被删除。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

RCL 的示例组件 `Component1` 使用以下背景图像和样式表。 无需将这些静态资产添加到从 RCL 项目模板创建的新 RCL，因为项目模板会自动添加它们。

`ComponentLibrary` RCL 中的 `wwwroot/background.png`：

![RCL 项目模板添加到库中的带斜条纹的背景图像](class-libraries/_static/background.png)

`ComponentLibrary` RCL 中的 `wwwroot/styles.css`：

```css
.my-component {
    border: 2px dashed red;
    padding: 1em;
    margin: 1em 0;
    background-image: url('background.png');
}
```

若要提供 `Component1` 的 `my-component` CSS 类，请在应用的 `<head>` 标记中链接到库的样式表。

`wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server)：

```diff
+ <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
```

::: moniker-end

## <a name="create-an-rcl-with-static-assets"></a>创建具有静态资产的 RCL

RCL 的静态资产可用于任何使用该库的应用。

将静态资产放在 RCL 的 `wwwroot` 文件夹中，并在应用中使用以下路径引用静态资产：`_content/{LIBRARY NAME}/{ASSET FILE NAME}`。 占位符 `{LIBRARY NAME}` 是库的名称。 `{ASSET FILE NAME}` 占位符是文件名称。

下面的示例演示了如何使用名为 `ComponentLibrary` 的 RCL 和使用该 RCL 的 Blazor 应用使用 RCL 静态资产。 此应用包含对 `ComponentLibrary` RCL 的项目引用。

本部分的示例中使用了下面的 Jeep&reg; 图像。 实现此部分中所示的示例后，请右键单击该图像以将其保存在本地。

`ComponentLibrary` RCL 中的 `wwwroot/jeep-yj.png`：

![Jeep YJ&reg;](class-libraries/_static/jeep-yj.png)

在 RCL 中添加以下 `JeepYJ` 组件。

`ComponentLibrary` RCL 中的 `JeepYJ.razor`：

```razor
<h3>ComponentLibrary.JeepYJ</h3>

<p>
    <img alt="Jeep YJ&reg;" src="_content/ComponentLibrary/jeep-yj.png" />
</p>
```

在使用 `ComponentLibrary` RCL 的应用中添加以下 `Jeep` 组件。 `Jeep` 组件使用：

* `ComponentLibrary` RCL 的 `wwwroot` 文件夹中的Jeep YJ&reg; 图像。
* RCL 中的 `JeepYJ` 组件。

`Pages/Jeep.razor`:

```razor
@page "/jeep"
@using ComponentLibrary

<div style="float:left;margin-right:10px">
    <h3>Direct use</h3>

    <p>
        <img alt="Jeep YJ&reg;" src="_content/ComponentLibrary/jeep-yj.png" />
    </p>
</div>

<JeepYJ />

<p>
    <em>Jeep</em> and <em>Jeep YJ</em> are registered trademarks of 
    <a href="https://www.stellantis.com">FCA US LLC (Stellantis NV)</a>.
</p>
```

呈现的 `Jeep` 组件：

![Jeep 组件](class-libraries/_static/jeep-component.png)

有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。

## <a name="supply-components-and-static-assets-to-multiple-hosted-blazor-apps&quot;></a>向多个托管的 Blazor 应用提供组件和静态资产

有关详细信息，请参阅 <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries-for-multiple-blazor-webassembly-apps>。

::: moniker range=&quot;>= aspnetcore-5.0&quot;

## <a name=&quot;browser-compatibility-analyzer-for-blazor-webassembly&quot;></a>Blazor WebAssembly 的浏览器兼容性分析器

Blazor WebAssembly 应用面向整个 .NET API 外围应用，但由于浏览器沙盒约束，并非所有 .NET API 在 WebAssembly 上都受支持。 在 WebAssembly 上运行时，不支持的 API 将引发 <xref:System.PlatformNotSupportedException>。 当应用使用应用目标平台不支持的 API 时，平台兼容性分析器会向开发人员发出警告。 对于 Blazor WebAssembly 应用，这意味着需要检查浏览器是否支持这些 API。 为兼容性分析器注释 .NET Framework API 是一个持续的过程，因此并不是所有的 .NET Framework API 当前都已进行注释。

Blazor WebAssembly 和 RCL 项目自动启用浏览器兼容性检查，方法是使用 `SupportedPlatform` MSBuild 项将 `browser` 添加为支持的平台。 库开发人员可以手动将 `SupportedPlatform` 项添加到库的项目文件以启用该功能：

```xml
<ItemGroup>
  <SupportedPlatform Include=&quot;browser&quot; />
</ItemGroup>
```

编写库时，通过将 `browser` 指定为 <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> 来指示浏览器不支持特定的 API：

```csharp
[UnsupportedOSPlatform(&quot;browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

有关详细信息，请参阅[在特定平台（dotnet/designs GitHub 存储库）上将 API 注释为不受支持](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)。

## <a name="blazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 隔离和对象引用

Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。 JavaScript 隔离具有以下优势：

* 导入的 JavaScript 不再污染全局命名空间。
* 库和组件的使用者不需要手动导入相关的 JavaScript。

有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>生成并打包库，再将其传送到 NuGet

由于包含 Razor 组件的 Razor 类库是标准的 .NET 库，因此将它们打包并传送到 NuGet 与将任何库打包并传送到 NuGet 没有什么区别。 在命令行界面中使用 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 命令，执行打包操作：

```dotnetcli
dotnet pack
```

在命令行界面中使用 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 命令，将包上传到 NuGet。

## <a name="trademarks"></a>商标

Jeep 和 Jeep YJ 是 [FCA 美国有限责任公司 (Stellantis NV)](https://www.stellantis.com) 的注册商标商标。 

## <a name="additional-resources"></a>其他资源

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [向库添加 XML 中间语言 (IL) 裁边器配置文件](xref:blazor/host-and-deploy/configure-trimmer)
* [Razor 类库的 CSS 隔离支持](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [向库添加 XML 中间语言 (IL) 链接器配置文件](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
