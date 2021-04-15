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
# <a name="consume-aspnet-core-razor-components-from-razor-class-libraries"></a><span data-ttu-id="0206d-103">使用 Razor 类库中的 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="0206d-103">Consume ASP.NET Core Razor components from Razor class libraries</span></span>

<span data-ttu-id="0206d-104">组件可在 [Razor 类库 (RCL)](xref:razor-pages/ui-class) 中跨项目共享。</span><span class="sxs-lookup"><span data-stu-id="0206d-104">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="0206d-105">在应用中包含组件和静态资产，它们来自：</span><span class="sxs-lookup"><span data-stu-id="0206d-105">Include components and static assets in an app from:</span></span>

* <span data-ttu-id="0206d-106">解决方案中的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-106">Another project in the solution.</span></span>
* <span data-ttu-id="0206d-107">引用的 .NET 库。</span><span class="sxs-lookup"><span data-stu-id="0206d-107">A referenced .NET library.</span></span>
* <span data-ttu-id="0206d-108">NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="0206d-108">A NuGet package.</span></span>

<span data-ttu-id="0206d-109">正如组件是常规的 .NET 类型一样，RCL 提供的组件也是普通的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="0206d-109">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="0206d-110">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="0206d-110">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0206d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0206d-111">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0206d-112">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-112">Create a new project.</span></span>
1. <span data-ttu-id="0206d-113">在“新建项目”对话框中，从 ASP.NET Core 项目模板列表中选择“Razor 类库” 。</span><span class="sxs-lookup"><span data-stu-id="0206d-113">In the **Create a new project** dialog, select **Razor Class Library** from the list of ASP.NET Core project templates.</span></span> <span data-ttu-id="0206d-114">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="0206d-114">Select **Next**.</span></span>
1. <span data-ttu-id="0206d-115">在“配置新项目”对话框的“项目名称”字段中提供项目名称，或接受默认项目名称 。</span><span class="sxs-lookup"><span data-stu-id="0206d-115">In the **Configure your new project** dialog, provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0206d-116">本主题中的示例使用项目名称 `ComponentLibrary`。</span><span class="sxs-lookup"><span data-stu-id="0206d-116">Examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="0206d-117">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="0206d-117">Select **Create**.</span></span>
1. <span data-ttu-id="0206d-118">在“创建新的 Razor 类库”对话框中，选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="0206d-118">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="0206d-119">将 RCL 添加到一个解决方案：</span><span class="sxs-lookup"><span data-stu-id="0206d-119">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="0206d-120">打开解决方案。</span><span class="sxs-lookup"><span data-stu-id="0206d-120">Open the solution.</span></span>
   1. <span data-ttu-id="0206d-121">在解决方案资源管理器中，右击解决方案。</span><span class="sxs-lookup"><span data-stu-id="0206d-121">Right-click the solution in **Solution Explorer**.</span></span> <span data-ttu-id="0206d-122">选择“添加” > “现有项目” 。</span><span class="sxs-lookup"><span data-stu-id="0206d-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="0206d-123">导航到 RCL 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="0206d-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="0206d-124">选择 RCL 的项目文件 (`.csproj`)。</span><span class="sxs-lookup"><span data-stu-id="0206d-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="0206d-125">从应用中添加对 RCL 的引用：</span><span class="sxs-lookup"><span data-stu-id="0206d-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="0206d-126">右键单击该应用项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-126">Right-click the app project.</span></span> <span data-ttu-id="0206d-127">选择“添加” > “项目引用”。</span><span class="sxs-lookup"><span data-stu-id="0206d-127">Select **Add** > **Project Reference**.</span></span>
   1. <span data-ttu-id="0206d-128">选择 RCL 项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-128">Select the RCL project.</span></span> <span data-ttu-id="0206d-129">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0206d-129">Select **OK**.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0206d-130">如果在从模板生成 RCL 时选中了“支持页和视图”复选框以支持页面和视图，则执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0206d-130">If the **Support pages and views** check box is selected to support pages and views when generating the RCL from the template:</span></span>

* <span data-ttu-id="0206d-131">使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：</span><span class="sxs-lookup"><span data-stu-id="0206d-131">Add an `_Imports.razor` file to root of the generated RCL project with the following contents to enable Razor component authoring:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* <span data-ttu-id="0206d-132">在项目文件 (`.csproj`) 中添加以下 `SupportedPlatform` 项：</span><span class="sxs-lookup"><span data-stu-id="0206d-132">Add the following `SupportedPlatform` item to the project file (`.csproj`):</span></span>

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  <span data-ttu-id="0206d-133">有关 `SupportedPlatform` 项的详细信息，请参阅 [Blazor WebAssembly 的浏览器兼容性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)部分。</span><span class="sxs-lookup"><span data-stu-id="0206d-133">For more information on the `SupportedPlatform` item, see the [Browser compatibility analyzer for Blazor WebAssembly](#browser-compatibility-analyzer-for-blazor-webassembly) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0206d-134">如果在从模板生成 RCL 时选中了“支持页面和视图”复选框，以支持页面和视图，则使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：</span><span class="sxs-lookup"><span data-stu-id="0206d-134">If the **Support pages and views** check box is selected to support pages and views when generating the RCL from the template, add an `_Imports.razor` file to root of the generated RCL project with the following contents to enable Razor component authoring:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0206d-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0206d-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0206d-136">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-136">Create a new project.</span></span>
1. <span data-ttu-id="0206d-137">在“Web 和控制台”下的边栏中选择“应用”。 </span><span class="sxs-lookup"><span data-stu-id="0206d-137">In the sidebar under **Web and Console**, select **App**.</span></span> <span data-ttu-id="0206d-138">在“ASP.NET Core”下，从显示的项目模板中选择“Razor 类库”。</span><span class="sxs-lookup"><span data-stu-id="0206d-138">Under **ASP.NET Core**, select **Razor Class Library** from the project templates shown.</span></span> <span data-ttu-id="0206d-139">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="0206d-139">Select **Next**.</span></span>
1. <span data-ttu-id="0206d-140">通过“目标框架”下拉列表选择库的目标框架。</span><span class="sxs-lookup"><span data-stu-id="0206d-140">Select the target framework for the library with the **Target Framework** dropdown list.</span></span> <span data-ttu-id="0206d-141">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="0206d-141">Select **Next**.</span></span>
1. <span data-ttu-id="0206d-142">在“配置新的类库”对话框的“项目名称”字段中提供项目名称。 </span><span class="sxs-lookup"><span data-stu-id="0206d-142">In the **Configure your new Class Library** dialog, provide a project name in the **Project Name** field.</span></span> <span data-ttu-id="0206d-143">本主题中的示例使用项目名称 `ComponentLibrary`。</span><span class="sxs-lookup"><span data-stu-id="0206d-143">Examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="0206d-144">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="0206d-144">Select **Create**.</span></span>
1. <span data-ttu-id="0206d-145">将 RCL 添加到一个解决方案：</span><span class="sxs-lookup"><span data-stu-id="0206d-145">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="0206d-146">打开解决方案。</span><span class="sxs-lookup"><span data-stu-id="0206d-146">Open the solution.</span></span>
   1. <span data-ttu-id="0206d-147">在解决方案资源管理器中，右击解决方案。</span><span class="sxs-lookup"><span data-stu-id="0206d-147">Right-click the solution in **Solution Explorer**.</span></span> <span data-ttu-id="0206d-148">选择“添加” > “现有项目” 。</span><span class="sxs-lookup"><span data-stu-id="0206d-148">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="0206d-149">导航到 RCL 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="0206d-149">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="0206d-150">选择 RCL 的项目文件 (`.csproj`)。</span><span class="sxs-lookup"><span data-stu-id="0206d-150">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="0206d-151">从应用中添加对 RCL 的引用：</span><span class="sxs-lookup"><span data-stu-id="0206d-151">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="0206d-152">右键单击该应用项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-152">Right-click the app project.</span></span> <span data-ttu-id="0206d-153">选择“添加” > “引用” 。</span><span class="sxs-lookup"><span data-stu-id="0206d-153">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="0206d-154">选择 RCL 项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-154">Select the RCL project.</span></span> <span data-ttu-id="0206d-155">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0206d-155">Select **OK**.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0206d-156">如果在从模板生成 RCL 时选中了“支持页和视图”复选框以支持页面和视图，则执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0206d-156">If the **Support pages and views** check box is selected to support pages and views when generating the RCL from the template:</span></span>

* <span data-ttu-id="0206d-157">使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：</span><span class="sxs-lookup"><span data-stu-id="0206d-157">Add an `_Imports.razor` file to root of the generated RCL project with the following contents to enable Razor component authoring:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* <span data-ttu-id="0206d-158">在项目文件 (`.csproj`) 中添加以下 `SupportedPlatform` 项：</span><span class="sxs-lookup"><span data-stu-id="0206d-158">Add the following `SupportedPlatform` item to the project file (`.csproj`):</span></span>

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  <span data-ttu-id="0206d-159">有关 `SupportedPlatform` 项的详细信息，请参阅 [Blazor WebAssembly 的浏览器兼容性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)部分。</span><span class="sxs-lookup"><span data-stu-id="0206d-159">For more information on the `SupportedPlatform` item, see the [Browser compatibility analyzer for Blazor WebAssembly](#browser-compatibility-analyzer-for-blazor-webassembly) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0206d-160">如果在从模板生成 RCL 时选中了“支持页面和视图”复选框，以支持页面和视图，则使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：</span><span class="sxs-lookup"><span data-stu-id="0206d-160">If the **Support pages and views** check box is selected to support pages and views when generating the RCL from the template, add an `_Imports.razor` file to root of the generated RCL project with the following contents to enable Razor component authoring:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="0206d-161">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0206d-161">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

1. <span data-ttu-id="0206d-162">在命令行界面中通过 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令使用 Razor 类库项目模板 (`razorclasslib`)。</span><span class="sxs-lookup"><span data-stu-id="0206d-162">Use the **Razor Class Library** project template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="0206d-163">下面的示例使用 `-o|--output` 选项创建了 RCL 并将其命名为 `ComponentLibrary`。</span><span class="sxs-lookup"><span data-stu-id="0206d-163">In the following example, an RCL is created and named `ComponentLibrary` using the `-o|--output` option.</span></span> <span data-ttu-id="0206d-164">执行命令时，自动创建包含 `ComponentLibrary` 的文件夹：</span><span class="sxs-lookup"><span data-stu-id="0206d-164">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

1. <span data-ttu-id="0206d-165">若要将库添加到现有项目中，请在命令行界面中使用 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 命令。</span><span class="sxs-lookup"><span data-stu-id="0206d-165">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="0206d-166">在下面的命令中，`{PATH TO LIBRARY}` 占位符是库的项目文件夹的路径：</span><span class="sxs-lookup"><span data-stu-id="0206d-166">In the following command, the `{PATH TO LIBRARY}` placeholder is the path to the library's project folder:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0206d-167">如果在从模板生成 RCL 时使用了 `-s|--support-pages-and-views` 选项，以便支持页面和视图，则执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0206d-167">If the `-s|--support-pages-and-views` option is used to support pages and views when generating the RCL from the template:</span></span>

* <span data-ttu-id="0206d-168">使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：</span><span class="sxs-lookup"><span data-stu-id="0206d-168">Add an `_Imports.razor` file to root of the generated RCL project with the following contents to enable Razor component authoring:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Components.Web
  ```

* <span data-ttu-id="0206d-169">在项目文件 (`.csproj`) 中添加以下 `SupportedPlatform` 项：</span><span class="sxs-lookup"><span data-stu-id="0206d-169">Add the following `SupportedPlatform` item to the project file (`.csproj`):</span></span>

  ```xml
  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>
  ```

  <span data-ttu-id="0206d-170">有关 `SupportedPlatform` 项的详细信息，请参阅 [Blazor WebAssembly 的浏览器兼容性分析器](#browser-compatibility-analyzer-for-blazor-webassembly)部分。</span><span class="sxs-lookup"><span data-stu-id="0206d-170">For more information on the `SupportedPlatform` item, see the [Browser compatibility analyzer for Blazor WebAssembly](#browser-compatibility-analyzer-for-blazor-webassembly) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0206d-171">如果在从模板生成 RCL 时使用了 `-s|--support-pages-and-views` 选项，以支持页面和视图，则使用以下内容，在生成的 RCL 项目的根中添加 `_Imports.razor` 文件，以便能够执行 Razor 组件创作：</span><span class="sxs-lookup"><span data-stu-id="0206d-171">If the `-s|--support-pages-and-views` option is used to support pages and views when generating the RCL from the template, add an `_Imports.razor` file to root of the generated RCL project with the following contents to enable Razor component authoring:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web
```

::: moniker-end

---

## <a name="consume-a-razor-component-from-an-rcl"></a><span data-ttu-id="0206d-172">使用 RCL 中的 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="0206d-172">Consume a Razor component from an RCL</span></span>

<span data-ttu-id="0206d-173">若要在其他项目中使用 RCL 中的组件，则使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="0206d-173">To consume components from an RCL in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="0206d-174">使用包含 RCL 命名空间的完整组件类型名称。</span><span class="sxs-lookup"><span data-stu-id="0206d-174">Use the full component type name, which includes the RCL's namespace.</span></span>
* <span data-ttu-id="0206d-175">如果 Razor 的 [`@using`](xref:mvc/views/razor#using) 指令声明了 RCL 的命名空间，则可以使用不含 RCL 命名空间的名称添加各个组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-175">Individual components can be added by name without the RCL's namespace if Razor's [`@using`](xref:mvc/views/razor#using) directive declares the RCL's namespace.</span></span> <span data-ttu-id="0206d-176">使用以下方法：</span><span class="sxs-lookup"><span data-stu-id="0206d-176">Use the following approaches:</span></span>
  * <span data-ttu-id="0206d-177">将 `@using` 指令添加到各个组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-177">Add the `@using` directive to individual components.</span></span>
  * <span data-ttu-id="0206d-178">在顶级 `_Imports.razor` 文件中包含 `@using` 指令，使库的组件可用于整个项目。</span><span class="sxs-lookup"><span data-stu-id="0206d-178">include the `@using` directive in the top-level `_Imports.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="0206d-179">将指令添加到任何级别的 `_Imports.razor` 文件，将命名空间应用于文件夹中的单个组件或一组组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-179">Add the directive to an `_Imports.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span> <span data-ttu-id="0206d-180">使用 `_Imports.razor` 文件时，各个组件不需要包含表示 RCL 命名空间的 `@using` 指令。</span><span class="sxs-lookup"><span data-stu-id="0206d-180">When an `_Imports.razor` file is used, individual components don't require an `@using` directive for the RCL's namespace.</span></span>

<span data-ttu-id="0206d-181">在下面的示例中，`ComponentLibrary` 是包含 `Component1` 组件的 RCL。</span><span class="sxs-lookup"><span data-stu-id="0206d-181">In the following examples, `ComponentLibrary` is an RCL containing the `Component1` component.</span></span> <span data-ttu-id="0206d-182">如果从 RCL 项目模板创建的是不支持页面和视图 RCL，则示例组件 `Component1` 组件会自动添加到其中。</span><span class="sxs-lookup"><span data-stu-id="0206d-182">The `Component1` component is an example component automatically added to an RCL created from the RCL project template that isn't created to support pages and views.</span></span>

> [!NOTE]
> <span data-ttu-id="0206d-183">如果创建的 RCL 支持页面和视图，则在 RCL 中手动添加 `Component1` 组件及其静态资产，以便能够按照本文中的示例操作。</span><span class="sxs-lookup"><span data-stu-id="0206d-183">If the RCL is created to support pages and views, manually add the `Component1` component and its static assets to the RCL if you plan to follow the examples in this article.</span></span> <span data-ttu-id="0206d-184">组件和静态资产如以下部分中所示。</span><span class="sxs-lookup"><span data-stu-id="0206d-184">The component and static assets are shown in this section.</span></span>

<span data-ttu-id="0206d-185">`ComponentLibrary` RCL 中的 `Component1.razor`：</span><span class="sxs-lookup"><span data-stu-id="0206d-185">`Component1.razor` in the `ComponentLibrary` RCL:</span></span>

```razor
<div class="my-component">
    This component is defined in the <strong>ComponentLibrary</strong> package.
</div>
```

<span data-ttu-id="0206d-186">在使用 RCL 的应用中，使用其命名空间引用 `Component1` 组件，如下面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="0206d-186">In the app that consumes the RCL, reference the `Component1` component using its namespace, as the following example shows.</span></span>

<span data-ttu-id="0206d-187">`Pages/ConsumeComponent1.razor`:</span><span class="sxs-lookup"><span data-stu-id="0206d-187">`Pages/ConsumeComponent1.razor`:</span></span>

```razor
@page "/consume-component-1"

<h1>Consume component (full namespace example)</h1>

<ComponentLibrary.Component1 />
```

<span data-ttu-id="0206d-188">或者，不使用其命名空间，而是添加一个 [`@using`](xref:mvc/views/razor#using) 指令以使用该组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-188">Alternatively, add a [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace.</span></span> <span data-ttu-id="0206d-189">以下 `@using` 指令还可以出现在当前文件夹中或其上方的任何 `_Imports.razor` 文件中。</span><span class="sxs-lookup"><span data-stu-id="0206d-189">The following `@using` directive can also appear in any `_Imports.razor` file in or above the current folder.</span></span>

<span data-ttu-id="0206d-190">`Pages/ConsumeComponent2.razor`:</span><span class="sxs-lookup"><span data-stu-id="0206d-190">`Pages/ConsumeComponent2.razor`:</span></span>

```razor
@page "/consume-component-2"
@using ComponentLibrary

<h1>Consume component (<code>@@using</code> example)</h1>

<Component1 />
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0206d-191">如果库组件使用 [CSS 隔离](xref:blazor/components/css-isolation)，组件样式会自动用于使用库的应用。</span><span class="sxs-lookup"><span data-stu-id="0206d-191">For library components that use [CSS isolation](xref:blazor/components/css-isolation), the component styles are automatically made available to the consuming app.</span></span> <span data-ttu-id="0206d-192">无需在使用库的应用中链接库的各个组件样式表。</span><span class="sxs-lookup"><span data-stu-id="0206d-192">There's no need to link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="0206d-193">在前面的示例中，`Component1` 的样式表 (`Component1.razor.css`) 将自动包含在内。</span><span class="sxs-lookup"><span data-stu-id="0206d-193">For the preceding examples, `Component1`'s stylesheet (`Component1.razor.css`) is included automatically.</span></span>

<span data-ttu-id="0206d-194">`ComponentLibrary` RCL 中的 `Component1.razor.css`：</span><span class="sxs-lookup"><span data-stu-id="0206d-194">`Component1.razor.css` in the `ComponentLibrary` RCL:</span></span>

```css
.my-component {
    border: 2px dashed red;
    padding: 1em;
    margin: 1em 0;
    background-image: url('background.png');
}
```

<span data-ttu-id="0206d-195">还会包含 RCL 项目模板中的背景图像，并将其保留在 RCL 的 `wwwroot` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="0206d-195">The background image is also included from the RCL project template and resides in the `wwwroot` folder of the RCL.</span></span>

<span data-ttu-id="0206d-196">`ComponentLibrary` RCL 中的 `wwwroot/background.png`：</span><span class="sxs-lookup"><span data-stu-id="0206d-196">`wwwroot/background.png` in the `ComponentLibrary` RCL:</span></span>

![来自 RCL 项目模板的带斜条纹的背景图像](class-libraries/_static/background.png)

::: moniker-end

::: moniker range=">= aspnetcore-6.0"

<span data-ttu-id="0206d-198">若要从库的 `wwwroot` 文件夹中的样式表中提供其他库组件样式，请使用框架的 `Link` 组件链接样式表。</span><span class="sxs-lookup"><span data-stu-id="0206d-198">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's `Link` component.</span></span>

<span data-ttu-id="0206d-199">下一个示例中使用了以下背景图像。</span><span class="sxs-lookup"><span data-stu-id="0206d-199">The following background image is used in the next example.</span></span> <span data-ttu-id="0206d-200">实现此部分中所示的示例后，请右键单击该图像以将其保存在本地。</span><span class="sxs-lookup"><span data-stu-id="0206d-200">If you implement the example shown in this section, right-click the image to save it locally.</span></span>

<span data-ttu-id="0206d-201">`ComponentLibrary` RCL 中的 `wwwroot/extra-background.png`：</span><span class="sxs-lookup"><span data-stu-id="0206d-201">`wwwroot/extra-background.png` in the `ComponentLibrary` RCL:</span></span>

![开发人员添加到库中的带斜条纹的背景图像](class-libraries/_static/extra-background.png)

<span data-ttu-id="0206d-203">使用 `extra-style` 类向 RCL 添加新的样式表。</span><span class="sxs-lookup"><span data-stu-id="0206d-203">Add a new stylesheet to the RCL with an `extra-style` class.</span></span>

<span data-ttu-id="0206d-204">`ComponentLibrary` RCL 中的 `wwwroot/additionalStyles.css`：</span><span class="sxs-lookup"><span data-stu-id="0206d-204">`wwwroot/additionalStyles.css` in the `ComponentLibrary` RCL:</span></span>

```css
.extra-style {
    border: 2px dashed blue;
    padding: 1em;
    margin: 1em 0;
    background-image: url('extra-background.png');
}
```

<span data-ttu-id="0206d-205">将组件添加到使用 `extra-style` 类的 RCL。</span><span class="sxs-lookup"><span data-stu-id="0206d-205">Add a component to the RCL that uses the `extra-style` class.</span></span>

<span data-ttu-id="0206d-206">`ComponentLibrary` RCL 中的 `ExtraStyles.razor`：</span><span class="sxs-lookup"><span data-stu-id="0206d-206">`ExtraStyles.razor` in the `ComponentLibrary` RCL:</span></span>

```razor
<Link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />

<div class="extra-style">
    <p>
        This component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="0206d-207">在使用 RCL 的 `ExtraStyles` 组件的应用中添加一个页面。</span><span class="sxs-lookup"><span data-stu-id="0206d-207">Add a page to the app that uses the `ExtraStyles` component from the RCL.</span></span>

<span data-ttu-id="0206d-208">`Pages/ConsumeComponent3.razor`:</span><span class="sxs-lookup"><span data-stu-id="0206d-208">`Pages/ConsumeComponent3.razor`:</span></span>

```razor
@page "/consume-component-3"
@using ComponentLibrary

<h1>Consume component (<code>additionalStyles.css</code> example)</h1>

<ExtraStyles />
```

<span data-ttu-id="0206d-209">在子组件中使用 `Link` 组件时，如果呈现 `Link` 组件的子组件，链接的资源就可用于父组件的任何其他子组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-209">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component if the child with the `Link` component is rendered.</span></span>

<span data-ttu-id="0206d-210">可在应用的 `Link` 标记中链接到库的样式表，来代替使用 `<head>` 组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-210">An alternative to using the `Link` component is to link to the library's stylesheet in the app's `<head>` markup.</span></span>

<span data-ttu-id="0206d-211">`wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server)：</span><span class="sxs-lookup"><span data-stu-id="0206d-211">`wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```diff
+ <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
```

<span data-ttu-id="0206d-212">在子组件中使用 `Link` 组件，与在 `wwwroot/index.html` 或 `Pages/_Host.cshtml` 中放置一个 `<link>` HTML 标记之间的区别是，框架组件已呈现的 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="0206d-212">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="0206d-213">可以根据应用程序状态进行修改。</span><span class="sxs-lookup"><span data-stu-id="0206d-213">Can be modified by application state.</span></span> <span data-ttu-id="0206d-214">不能根据应用程序状态修改硬编码 `<link>` HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="0206d-214">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="0206d-215">将在不再呈现父组件的情况下从 HTML `<head>` 中被删除。</span><span class="sxs-lookup"><span data-stu-id="0206d-215">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0206d-216">RCL 的示例组件 `Component1` 使用以下背景图像和样式表。</span><span class="sxs-lookup"><span data-stu-id="0206d-216">The following background image and stylesheet are used by the RCL's `Component1` example component.</span></span> <span data-ttu-id="0206d-217">无需将这些静态资产添加到从 RCL 项目模板创建的新 RCL，因为项目模板会自动添加它们。</span><span class="sxs-lookup"><span data-stu-id="0206d-217">There's no need to add these static assets to a new RCL created from the RCL project template, as they're added automatically by the project template.</span></span>

<span data-ttu-id="0206d-218">`ComponentLibrary` RCL 中的 `wwwroot/background.png`：</span><span class="sxs-lookup"><span data-stu-id="0206d-218">`wwwroot/background.png` in the `ComponentLibrary` RCL:</span></span>

![RCL 项目模板添加到库中的带斜条纹的背景图像](class-libraries/_static/background.png)

<span data-ttu-id="0206d-220">`ComponentLibrary` RCL 中的 `wwwroot/styles.css`：</span><span class="sxs-lookup"><span data-stu-id="0206d-220">`wwwroot/styles.css` in the `ComponentLibrary` RCL:</span></span>

```css
.my-component {
    border: 2px dashed red;
    padding: 1em;
    margin: 1em 0;
    background-image: url('background.png');
}
```

<span data-ttu-id="0206d-221">若要提供 `Component1` 的 `my-component` CSS 类，请在应用的 `<head>` 标记中链接到库的样式表。</span><span class="sxs-lookup"><span data-stu-id="0206d-221">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `<head>` markup.</span></span>

<span data-ttu-id="0206d-222">`wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server)：</span><span class="sxs-lookup"><span data-stu-id="0206d-222">`wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```diff
+ <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
```

::: moniker-end

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="0206d-223">创建具有静态资产的 RCL</span><span class="sxs-lookup"><span data-stu-id="0206d-223">Create an RCL with static assets</span></span>

<span data-ttu-id="0206d-224">RCL 的静态资产可用于任何使用该库的应用。</span><span class="sxs-lookup"><span data-stu-id="0206d-224">An RCL's static assets are available to any app that consumes the library.</span></span>

<span data-ttu-id="0206d-225">将静态资产放在 RCL 的 `wwwroot` 文件夹中，并在应用中使用以下路径引用静态资产：`_content/{LIBRARY NAME}/{ASSET FILE NAME}`。</span><span class="sxs-lookup"><span data-stu-id="0206d-225">Place static assets in the `wwwroot` folder of the RCL and reference the static assets with the following path in the app: `_content/{LIBRARY NAME}/{ASSET FILE NAME}`.</span></span> <span data-ttu-id="0206d-226">占位符 `{LIBRARY NAME}` 是库的名称。</span><span class="sxs-lookup"><span data-stu-id="0206d-226">The `{LIBRARY NAME}` placeholder is the library name.</span></span> <span data-ttu-id="0206d-227">`{ASSET FILE NAME}` 占位符是文件名称。</span><span class="sxs-lookup"><span data-stu-id="0206d-227">The `{ASSET FILE NAME}` placeholder is the file name.</span></span>

<span data-ttu-id="0206d-228">下面的示例演示了如何使用名为 `ComponentLibrary` 的 RCL 和使用该 RCL 的 Blazor 应用使用 RCL 静态资产。</span><span class="sxs-lookup"><span data-stu-id="0206d-228">The following example demonstrates the use of RCL static assets with an RCL named `ComponentLibrary` and a Blazor app that consumes the RCL.</span></span> <span data-ttu-id="0206d-229">此应用包含对 `ComponentLibrary` RCL 的项目引用。</span><span class="sxs-lookup"><span data-stu-id="0206d-229">The app has a project reference for the `ComponentLibrary` RCL.</span></span>

<span data-ttu-id="0206d-230">本部分的示例中使用了下面的 Jeep&reg; 图像。</span><span class="sxs-lookup"><span data-stu-id="0206d-230">The following Jeep&reg; image is used in this section's example.</span></span> <span data-ttu-id="0206d-231">实现此部分中所示的示例后，请右键单击该图像以将其保存在本地。</span><span class="sxs-lookup"><span data-stu-id="0206d-231">If you implement the example shown in this section, right-click the image to save it locally.</span></span>

<span data-ttu-id="0206d-232">`ComponentLibrary` RCL 中的 `wwwroot/jeep-yj.png`：</span><span class="sxs-lookup"><span data-stu-id="0206d-232">`wwwroot/jeep-yj.png` in the `ComponentLibrary` RCL:</span></span>

![Jeep YJ&reg;](class-libraries/_static/jeep-yj.png)

<span data-ttu-id="0206d-234">在 RCL 中添加以下 `JeepYJ` 组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-234">Add the following `JeepYJ` component to the RCL.</span></span>

<span data-ttu-id="0206d-235">`ComponentLibrary` RCL 中的 `JeepYJ.razor`：</span><span class="sxs-lookup"><span data-stu-id="0206d-235">`JeepYJ.razor` in the `ComponentLibrary` RCL:</span></span>

```razor
<h3>ComponentLibrary.JeepYJ</h3>

<p>
    <img alt="Jeep YJ&reg;" src="_content/ComponentLibrary/jeep-yj.png" />
</p>
```

<span data-ttu-id="0206d-236">在使用 `ComponentLibrary` RCL 的应用中添加以下 `Jeep` 组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-236">Add the following `Jeep` component to the app that consumes the `ComponentLibrary` RCL.</span></span> <span data-ttu-id="0206d-237">`Jeep` 组件使用：</span><span class="sxs-lookup"><span data-stu-id="0206d-237">The `Jeep` component uses:</span></span>

* <span data-ttu-id="0206d-238">`ComponentLibrary` RCL 的 `wwwroot` 文件夹中的Jeep YJ&reg; 图像。</span><span class="sxs-lookup"><span data-stu-id="0206d-238">The Jeep YJ&reg; image from the `ComponentLibrary` RCL's `wwwroot` folder.</span></span>
* <span data-ttu-id="0206d-239">RCL 中的 `JeepYJ` 组件。</span><span class="sxs-lookup"><span data-stu-id="0206d-239">The `JeepYJ` component from the RCL.</span></span>

<span data-ttu-id="0206d-240">`Pages/Jeep.razor`:</span><span class="sxs-lookup"><span data-stu-id="0206d-240">`Pages/Jeep.razor`:</span></span>

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

<span data-ttu-id="0206d-241">呈现的 `Jeep` 组件：</span><span class="sxs-lookup"><span data-stu-id="0206d-241">Rendered `Jeep` component:</span></span>

![Jeep 组件](class-libraries/_static/jeep-component.png)

<span data-ttu-id="0206d-243&quot;>有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-243&quot;>For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name=&quot;supply-components-and-static-assets-to-multiple-hosted-blazor-apps&quot;></a><span data-ttu-id=&quot;0206d-244&quot;>向多个托管的 Blazor 应用提供组件和静态资产</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-244&quot;>Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id=&quot;0206d-245&quot;>有关详细信息，请参阅 <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries-for-multiple-blazor-webassembly-apps>。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-245&quot;>For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries-for-multiple-blazor-webassembly-apps>.</span></span>

::: moniker range=&quot;>= aspnetcore-5.0&quot;

## <a name=&quot;browser-compatibility-analyzer-for-blazor-webassembly&quot;></a><span data-ttu-id=&quot;0206d-246&quot;>Blazor WebAssembly 的浏览器兼容性分析器</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-246&quot;>Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id=&quot;0206d-247&quot;>Blazor WebAssembly 应用面向整个 .NET API 外围应用，但由于浏览器沙盒约束，并非所有 .NET API 在 WebAssembly 上都受支持。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-247&quot;>Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id=&quot;0206d-248&quot;>在 WebAssembly 上运行时，不支持的 API 将引发 <xref:System.PlatformNotSupportedException>。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-248&quot;>Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id=&quot;0206d-249&quot;>当应用使用应用目标平台不支持的 API 时，平台兼容性分析器会向开发人员发出警告。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-249&quot;>A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id=&quot;0206d-250&quot;>对于 Blazor WebAssembly 应用，这意味着需要检查浏览器是否支持这些 API。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-250&quot;>For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id=&quot;0206d-251&quot;>为兼容性分析器注释 .NET Framework API 是一个持续的过程，因此并不是所有的 .NET Framework API 当前都已进行注释。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-251&quot;>Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id=&quot;0206d-252&quot;>Blazor WebAssembly 和 RCL 项目自动启用浏览器兼容性检查，方法是使用 `SupportedPlatform` MSBuild 项将 `browser` 添加为支持的平台。</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-252&quot;>Blazor WebAssembly and RCL projects *automatically* enable browser compatibility checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id=&quot;0206d-253&quot;>库开发人员可以手动将 `SupportedPlatform` 项添加到库的项目文件以启用该功能：</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-253&quot;>Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include=&quot;browser&quot; />
</ItemGroup>
```

<span data-ttu-id=&quot;0206d-254&quot;>编写库时，通过将 `browser` 指定为 <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> 来指示浏览器不支持特定的 API：</span><span class=&quot;sxs-lookup&quot;><span data-stu-id=&quot;0206d-254&quot;>When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform(&quot;browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="0206d-255">有关详细信息，请参阅[在特定平台（dotnet/designs GitHub 存储库）上将 API 注释为不受支持](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)。</span><span class="sxs-lookup"><span data-stu-id="0206d-255">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="blazor-javascript-isolation-and-object-references"></a><span data-ttu-id="0206d-256">Blazor JavaScript 隔离和对象引用</span><span class="sxs-lookup"><span data-stu-id="0206d-256">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="0206d-257">Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。</span><span class="sxs-lookup"><span data-stu-id="0206d-257">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="0206d-258">JavaScript 隔离具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="0206d-258">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="0206d-259">导入的 JavaScript 不再污染全局命名空间。</span><span class="sxs-lookup"><span data-stu-id="0206d-259">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="0206d-260">库和组件的使用者不需要手动导入相关的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="0206d-260">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="0206d-261">有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。</span><span class="sxs-lookup"><span data-stu-id="0206d-261">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="0206d-262">生成并打包库，再将其传送到 NuGet</span><span class="sxs-lookup"><span data-stu-id="0206d-262">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="0206d-263">由于包含 Razor 组件的 Razor 类库是标准的 .NET 库，因此将它们打包并传送到 NuGet 与将任何库打包并传送到 NuGet 没有什么区别。</span><span class="sxs-lookup"><span data-stu-id="0206d-263">Because Razor class libraries that contain Razor components are standard .NET libraries, packing and shipping them to NuGet is no different from packing and shipping any library to NuGet.</span></span> <span data-ttu-id="0206d-264">在命令行界面中使用 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 命令，执行打包操作：</span><span class="sxs-lookup"><span data-stu-id="0206d-264">Packing is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="0206d-265">在命令行界面中使用 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 命令，将包上传到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="0206d-265">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="trademarks"></a><span data-ttu-id="0206d-266">商标</span><span class="sxs-lookup"><span data-stu-id="0206d-266">Trademarks</span></span>

<span data-ttu-id="0206d-267">Jeep 和 Jeep YJ 是 [FCA 美国有限责任公司 (Stellantis NV)](https://www.stellantis.com) 的注册商标商标。 </span><span class="sxs-lookup"><span data-stu-id="0206d-267">*Jeep* and *Jeep YJ* are registered trademarks of [FCA US LLC (Stellantis NV)](https://www.stellantis.com).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0206d-268">其他资源</span><span class="sxs-lookup"><span data-stu-id="0206d-268">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="0206d-269">向库添加 XML 中间语言 (IL) 裁边器配置文件</span><span class="sxs-lookup"><span data-stu-id="0206d-269">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="0206d-270">Razor 类库的 CSS 隔离支持</span><span class="sxs-lookup"><span data-stu-id="0206d-270">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="0206d-271">向库添加 XML 中间语言 (IL) 链接器配置文件</span><span class="sxs-lookup"><span data-stu-id="0206d-271">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
