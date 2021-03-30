---
title: ASP.NET Core Blazor 高级方案
author: guardrex
description: 了解 Blazor 中的高级方案，包括如何将 RenderTreeBuilder 手动逻辑合并到应用中。
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: bbefdf7272cc09d09baa2e5f2a42d04f91eca1bd
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711199"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core Blazor 高级方案

## <a name="manual-rendertreebuilder-logic"></a>RenderTreeBuilder 手动逻辑

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供用于操作组件和元素的方法，包括在 C# 代码中手动生成组件。

> [!WARNING]
> 使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 创建组件是一种高级方案。 格式不正确的组件（例如，未封闭的标记标签）可能导致未定义的行为。 未定义的行为包括内容呈现损坏、应用功能丢失和安全性受损。

以下面的 `PetDetails` 组件为例，此组件可通过手动方式在另一个组件中呈现。

`Shared/PetDetails.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

在以下 `BuiltContent` 组件中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。

在具有序列号的 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法中，序列号是源代码行号。 Blazor 差分算法依赖于对应于不同代码行（而不是不同调用的调用）的序列号。 使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件时，请对序列号的参数进行硬编码。 **通过计算或计数器生成序列号可能导致性能不佳。** 有关详细信息，请参阅[序列号与代码行号相关，而不与执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)部分。

`Pages/BuiltContent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

> [!WARNING]
> <xref:Microsoft.AspNetCore.Components.RenderTree> 中的类型允许处理呈现操作的 *结果*。 这些是 Blazor 框架实现的内部细节。 这些类型应视为 *不稳定*，并且在未来版本中可能会有更改。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>序列号与代码行号相关，而不与执行顺序相关

Razor 组件文件 (`.razor`) 始终被编译。 与解释代码相比，执行编译的代码具有潜在优势，因为生成编译代码的编译步骤可用于注入信息，从而在运行时提高应用性能。

这些改进的关键示例涉及 *序列号*。 序列号向运行时指示哪些输出来自哪些不同的已排序代码行。 运行时使用此信息在线性时间内生成高效的树上差分，这比常规树上差分算法通常可以做到的速度快得多。

以下面的 Razor 组件文件 (`.razor`) 为例：

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

前面的 Razor 标记和文本内容编译为如下所示的 C# 代码：

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

首次执行代码时，如果 `someFlag` 为 `true`，则生成器会收到：

| 序列 | 类型      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

假设 `someFlag` 变为 `false` 且标记再次呈现。 此时，生成器会收到：

| 序列 | 类型       | 数据   |
| :------: | ---------- | :----: |
| 1        | Text 节点  | 秒 |

当运行时执行差分时，它会看到序列 `0` 处的项目已被删除，因此，它会通过单步执行生成以下普通编辑脚本：

* 删除第一个文本节点。

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>以编程方式生成序列号的问题

想象一下，你编写了以下呈现树生成器逻辑：

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

现在，第一个输出是：

| 序列 | 类型      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

此结果与之前的示例相同，因此不存在负面问题。 在第二个呈现中，`someFlag` 为 `false`，输出为：

| 序列 | 类型      | 数据   |
| :------: | --------- | ------ |
| 0        | Text 节点 | 秒 |

这次，差分算法看到已发生两个更改。 此算法将生成以下编辑脚本：

* 将第一个文本节点的值更改为 `Second`。
* 删除第二个文本节点。

生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。 这会导致 **两倍于** 之前长度的差异。

这是一个普通示例。 在具有深度嵌套的复杂结构（尤其是带有循环）的更真实的情况下，性能成本通常会更高。 差分算法必须深入递归到呈现树中，而不是立即确定已插入或删除的循环块或分支。 这通常导致生成更长的编辑脚本，因为差分算法获知了关于新旧结构之间关系的错误信息。

### <a name="guidance-and-conclusions"></a>指南和结论

* 如果动态生成序列号，则应用性能会受到影响。
* 该框架无法在运行时自动创建自己的序列号，因为除非在编译时捕获了必需的信息，否则这些信息不存在。
* 不要编写手动实现的冗长 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑块。 优先使用 `.razor` 文件并允许编译器处理序列号。 如果无法避免 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 手动逻辑，请将较长的代码块拆分为封装在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 调用中的较小部分。 每个区域都有自己的独立序列号空间，因此可在每个区域内从零（或任何其他任意数）重新开始。
* 如果序列号已硬编码，则差分算法仅要求序列号的值增加。 初始值和间隔不相关。 一个合理选择是使用代码行号作为序列号，或者从零开始并以 1 或 100 的间隔（或任何首选间隔）增加。
* Blazor 使用序列号，而其他树上差分 UI 框架不使用它们。 使用序列号时，差分速度要快得多，并且 Blazor 的优势在于编译步骤可为编写 `.razor` 文件的开发人员自动处理序列号。
