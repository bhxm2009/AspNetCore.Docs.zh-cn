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
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="6c2d4-103">ASP.NET Core Blazor 高级方案</span><span class="sxs-lookup"><span data-stu-id="6c2d4-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="6c2d4-104">RenderTreeBuilder 手动逻辑</span><span class="sxs-lookup"><span data-stu-id="6c2d4-104">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="6c2d4-105"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供用于操作组件和元素的方法，包括在 C# 代码中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-105"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!WARNING]
> <span data-ttu-id="6c2d4-106">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-106">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an *advanced scenario*.</span></span> <span data-ttu-id="6c2d4-107">格式不正确的组件（例如，未封闭的标记标签）可能导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-107">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span> <span data-ttu-id="6c2d4-108">未定义的行为包括内容呈现损坏、应用功能丢失和安全性受损。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-108">Undefined behavior includes broken content rendering, loss of app features, and **_compromised security_**.</span></span>

<span data-ttu-id="6c2d4-109">以下面的 `PetDetails` 组件为例，此组件可通过手动方式在另一个组件中呈现。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-109">Consider the following `PetDetails` component, which can be manually rendered in another component.</span></span>

<span data-ttu-id="6c2d4-110">`Shared/PetDetails.razor`:</span><span class="sxs-lookup"><span data-stu-id="6c2d4-110">`Shared/PetDetails.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/advanced-scenarios/PetDetails.razor)]

::: moniker-end

<span data-ttu-id="6c2d4-111">在以下 `BuiltContent` 组件中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-111">In the following `BuiltContent` component, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span>

<span data-ttu-id="6c2d4-112">在具有序列号的 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法中，序列号是源代码行号。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-112">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="6c2d4-113">Blazor 差分算法依赖于对应于不同代码行（而不是不同调用的调用）的序列号。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-113">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="6c2d4-114">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件时，请对序列号的参数进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-114">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="6c2d4-115">**通过计算或计数器生成序列号可能导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="6c2d4-115">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="6c2d4-116">有关详细信息，请参阅[序列号与代码行号相关，而不与执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)部分。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-116">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="6c2d4-117">`Pages/BuiltContent.razor`:</span><span class="sxs-lookup"><span data-stu-id="6c2d4-117">`Pages/BuiltContent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/advanced-scenarios/BuiltContent.razor?highlight=6,16-24,28)]

::: moniker-end

> [!WARNING]
> <span data-ttu-id="6c2d4-118"><xref:Microsoft.AspNetCore.Components.RenderTree> 中的类型允许处理呈现操作的 *结果*。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-118">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="6c2d4-119">这些是 Blazor 框架实现的内部细节。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-119">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="6c2d4-120">这些类型应视为 *不稳定*，并且在未来版本中可能会有更改。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-120">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="6c2d4-121">序列号与代码行号相关，而不与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="6c2d4-121">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="6c2d4-122">Razor 组件文件 (`.razor`) 始终被编译。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-122">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="6c2d4-123">与解释代码相比，执行编译的代码具有潜在优势，因为生成编译代码的编译步骤可用于注入信息，从而在运行时提高应用性能。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-123">Executing compiled code has a potential advantage over interpreting code because the compile step that yields the compiled code can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="6c2d4-124">这些改进的关键示例涉及 *序列号*。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-124">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="6c2d4-125">序列号向运行时指示哪些输出来自哪些不同的已排序代码行。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-125">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="6c2d4-126">运行时使用此信息在线性时间内生成高效的树上差分，这比常规树上差分算法通常可以做到的速度快得多。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-126">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="6c2d4-127">以下面的 Razor 组件文件 (`.razor`) 为例：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-127">Consider the following Razor component file (`.razor`):</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="6c2d4-128">前面的 Razor 标记和文本内容编译为如下所示的 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-128">The preceding Razor markup and text content compiles into C# code similar to the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="6c2d4-129">首次执行代码时，如果 `someFlag` 为 `true`，则生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-129">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="6c2d4-130">序列</span><span class="sxs-lookup"><span data-stu-id="6c2d4-130">Sequence</span></span> | <span data-ttu-id="6c2d4-131">类型</span><span class="sxs-lookup"><span data-stu-id="6c2d4-131">Type</span></span>      | <span data-ttu-id="6c2d4-132">数据</span><span class="sxs-lookup"><span data-stu-id="6c2d4-132">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6c2d4-133">0</span><span class="sxs-lookup"><span data-stu-id="6c2d4-133">0</span></span>        | <span data-ttu-id="6c2d4-134">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6c2d4-134">Text node</span></span> | <span data-ttu-id="6c2d4-135">First</span><span class="sxs-lookup"><span data-stu-id="6c2d4-135">First</span></span>  |
| <span data-ttu-id="6c2d4-136">1</span><span class="sxs-lookup"><span data-stu-id="6c2d4-136">1</span></span>        | <span data-ttu-id="6c2d4-137">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6c2d4-137">Text node</span></span> | <span data-ttu-id="6c2d4-138">秒</span><span class="sxs-lookup"><span data-stu-id="6c2d4-138">Second</span></span> |

<span data-ttu-id="6c2d4-139">假设 `someFlag` 变为 `false` 且标记再次呈现。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-139">Imagine that `someFlag` becomes `false` and the markup is rendered again.</span></span> <span data-ttu-id="6c2d4-140">此时，生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-140">This time, the builder receives:</span></span>

| <span data-ttu-id="6c2d4-141">序列</span><span class="sxs-lookup"><span data-stu-id="6c2d4-141">Sequence</span></span> | <span data-ttu-id="6c2d4-142">类型</span><span class="sxs-lookup"><span data-stu-id="6c2d4-142">Type</span></span>       | <span data-ttu-id="6c2d4-143">数据</span><span class="sxs-lookup"><span data-stu-id="6c2d4-143">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="6c2d4-144">1</span><span class="sxs-lookup"><span data-stu-id="6c2d4-144">1</span></span>        | <span data-ttu-id="6c2d4-145">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6c2d4-145">Text node</span></span>  | <span data-ttu-id="6c2d4-146">秒</span><span class="sxs-lookup"><span data-stu-id="6c2d4-146">Second</span></span> |

<span data-ttu-id="6c2d4-147">当运行时执行差分时，它会看到序列 `0` 处的项目已被删除，因此，它会通过单步执行生成以下普通编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-147">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* with a single step:</span></span>

* <span data-ttu-id="6c2d4-148">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-148">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="6c2d4-149">以编程方式生成序列号的问题</span><span class="sxs-lookup"><span data-stu-id="6c2d4-149">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="6c2d4-150">想象一下，你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-150">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="6c2d4-151">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-151">Now, the first output is:</span></span>

| <span data-ttu-id="6c2d4-152">序列</span><span class="sxs-lookup"><span data-stu-id="6c2d4-152">Sequence</span></span> | <span data-ttu-id="6c2d4-153">类型</span><span class="sxs-lookup"><span data-stu-id="6c2d4-153">Type</span></span>      | <span data-ttu-id="6c2d4-154">数据</span><span class="sxs-lookup"><span data-stu-id="6c2d4-154">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6c2d4-155">0</span><span class="sxs-lookup"><span data-stu-id="6c2d4-155">0</span></span>        | <span data-ttu-id="6c2d4-156">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6c2d4-156">Text node</span></span> | <span data-ttu-id="6c2d4-157">First</span><span class="sxs-lookup"><span data-stu-id="6c2d4-157">First</span></span>  |
| <span data-ttu-id="6c2d4-158">1</span><span class="sxs-lookup"><span data-stu-id="6c2d4-158">1</span></span>        | <span data-ttu-id="6c2d4-159">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6c2d4-159">Text node</span></span> | <span data-ttu-id="6c2d4-160">秒</span><span class="sxs-lookup"><span data-stu-id="6c2d4-160">Second</span></span> |

<span data-ttu-id="6c2d4-161">此结果与之前的示例相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-161">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="6c2d4-162">在第二个呈现中，`someFlag` 为 `false`，输出为：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-162">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="6c2d4-163">序列</span><span class="sxs-lookup"><span data-stu-id="6c2d4-163">Sequence</span></span> | <span data-ttu-id="6c2d4-164">类型</span><span class="sxs-lookup"><span data-stu-id="6c2d4-164">Type</span></span>      | <span data-ttu-id="6c2d4-165">数据</span><span class="sxs-lookup"><span data-stu-id="6c2d4-165">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="6c2d4-166">0</span><span class="sxs-lookup"><span data-stu-id="6c2d4-166">0</span></span>        | <span data-ttu-id="6c2d4-167">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6c2d4-167">Text node</span></span> | <span data-ttu-id="6c2d4-168">秒</span><span class="sxs-lookup"><span data-stu-id="6c2d4-168">Second</span></span> |

<span data-ttu-id="6c2d4-169">这次，差分算法看到已发生两个更改。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-169">This time, the diff algorithm sees that *two* changes have occurred.</span></span> <span data-ttu-id="6c2d4-170">此算法将生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="6c2d4-170">The algorithm generates the following edit script:</span></span>

* <span data-ttu-id="6c2d4-171">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-171">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="6c2d4-172">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-172">Remove the second text node.</span></span>

<span data-ttu-id="6c2d4-173">生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-173">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="6c2d4-174">这会导致 **两倍于** 之前长度的差异。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-174">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="6c2d4-175">这是一个普通示例。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-175">This is a trivial example.</span></span> <span data-ttu-id="6c2d4-176">在具有深度嵌套的复杂结构（尤其是带有循环）的更真实的情况下，性能成本通常会更高。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-176">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="6c2d4-177">差分算法必须深入递归到呈现树中，而不是立即确定已插入或删除的循环块或分支。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-177">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm must recurse deeply into the render trees.</span></span> <span data-ttu-id="6c2d4-178">这通常导致生成更长的编辑脚本，因为差分算法获知了关于新旧结构之间关系的错误信息。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-178">This usually results in building longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="6c2d4-179">指南和结论</span><span class="sxs-lookup"><span data-stu-id="6c2d4-179">Guidance and conclusions</span></span>

* <span data-ttu-id="6c2d4-180">如果动态生成序列号，则应用性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-180">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="6c2d4-181">该框架无法在运行时自动创建自己的序列号，因为除非在编译时捕获了必需的信息，否则这些信息不存在。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-181">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="6c2d4-182">不要编写手动实现的冗长 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑块。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-182">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="6c2d4-183">优先使用 `.razor` 文件并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-183">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="6c2d4-184">如果无法避免 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 手动逻辑，请将较长的代码块拆分为封装在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 调用中的较小部分。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-184">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="6c2d4-185">每个区域都有自己的独立序列号空间，因此可在每个区域内从零（或任何其他任意数）重新开始。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-185">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="6c2d4-186">如果序列号已硬编码，则差分算法仅要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-186">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="6c2d4-187">初始值和间隔不相关。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-187">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="6c2d4-188">一个合理选择是使用代码行号作为序列号，或者从零开始并以 1 或 100 的间隔（或任何首选间隔）增加。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-188">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span>
* <span data-ttu-id="6c2d4-189">Blazor 使用序列号，而其他树上差分 UI 框架不使用它们。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-189">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="6c2d4-190">使用序列号时，差分速度要快得多，并且 Blazor 的优势在于编译步骤可为编写 `.razor` 文件的开发人员自动处理序列号。</span><span class="sxs-lookup"><span data-stu-id="6c2d4-190">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
