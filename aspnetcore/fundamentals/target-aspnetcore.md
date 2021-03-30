---
title: 使用类库中的 ASP.NET Core API
author: scottaddie
description: 了解如何使用类库中的 ASP.NET Core API。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 454f2523a44f5c1aa12b9a27c21c6a3e933ab81a
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711474"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="12f5d-103">使用类库中的 ASP.NET Core API</span><span class="sxs-lookup"><span data-stu-id="12f5d-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="12f5d-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="12f5d-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="12f5d-105">此文档提供了关于如何使用类库中的 ASP.NET Core API 的指南。</span><span class="sxs-lookup"><span data-stu-id="12f5d-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="12f5d-106">若要查看所有其他的库指南，请参阅[开放源代码库指南](/dotnet/standard/library-guidance/)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="12f5d-107">确定要支持哪些 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="12f5d-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="12f5d-108">ASP.NET Core 遵从 [.NET Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="12f5d-109">确定库要支持哪些 ASP.NET Core 版本时，请参阅支持策略。</span><span class="sxs-lookup"><span data-stu-id="12f5d-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="12f5d-110">库应符合以下条件：</span><span class="sxs-lookup"><span data-stu-id="12f5d-110">A library should:</span></span>

* <span data-ttu-id="12f5d-111">努力支持列为“长期支持”(LTS) 类别的所有 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="12f5d-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="12f5d-112">无需支持列为“生命周期结束”(EOL) 类别的 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="12f5d-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="12f5d-113">由于 ASP.NET Core 预览版已推出，因此已在 [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub 存储库中发布中断性变更。</span><span class="sxs-lookup"><span data-stu-id="12f5d-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="12f5d-114">开发框架功能时，可执行库兼容性测试。</span><span class="sxs-lookup"><span data-stu-id="12f5d-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="12f5d-115">使用 ASP.NET Core 共享框架</span><span class="sxs-lookup"><span data-stu-id="12f5d-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="12f5d-116">随着 .NET Core 3.0 发布，许多 ASP.NET Core 程序集不再作为包发布到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="12f5d-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="12f5d-117">而是改为将这些程序集包含在通过 .NET Core SDK 和运行时安装程序安装的 `Microsoft.AspNetCore.App` 共享框架中。</span><span class="sxs-lookup"><span data-stu-id="12f5d-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="12f5d-118">若要查看不再发布的包列表，请参阅[删除过时的包引用](xref:migration/22-to-30#remove-obsolete-package-references)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="12f5d-119">自 .NET Core 3.0 起，使用 `Microsoft.NET.Sdk.Web` MSBuild SDK 的项目隐式引用此共享框架。</span><span class="sxs-lookup"><span data-stu-id="12f5d-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="12f5d-120">使用 `Microsoft.NET.Sdk` 或 `Microsoft.NET.Sdk.Razor` SDK 的项目必须引用 ASP.NET Core，才能使用共享框架中的 ASP.NET Core API。</span><span class="sxs-lookup"><span data-stu-id="12f5d-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="12f5d-121">若要引用 ASP.NET Core，请将以下 `<FrameworkReference>` 元素添加到项目文件：</span><span class="sxs-lookup"><span data-stu-id="12f5d-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="12f5d-122">仅面向 .NET Core 3.x 的项目支持使用此方式引用 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="12f5d-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="12f5d-123">包括 Blazor 扩展性</span><span class="sxs-lookup"><span data-stu-id="12f5d-123">Include Blazor extensibility</span></span>

<span data-ttu-id="12f5d-124">Blazor 支持 WebAssembly (WASM) 和基于服务器的[托管模型](xref:blazor/hosting-models)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-124">Blazor supports WebAssembly (WASM) and server-based [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="12f5d-125">除非出于特定原因无法同时支持这两种托管模型，否则 [Razor 组件](xref:blazor/components/index)库会同时支持这两种托管模型。</span><span class="sxs-lookup"><span data-stu-id="12f5d-125">Unless there's a specific reason not to support both hosting models, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="12f5d-126">Razor 组件库必须使用 [Microsoft.NET.Sdk.RazorSDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="12f5d-127">同时支持两种托管模型</span><span class="sxs-lookup"><span data-stu-id="12f5d-127">Support both hosting models</span></span>

<span data-ttu-id="12f5d-128">请针对自己的编辑器使用以下说明，以支持 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 和 [Blazor Server](xref:blazor/hosting-models#blazor-server) 项目使用 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="12f5d-128">To support Razor component consumption by [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) and [Blazor Server](xref:blazor/hosting-models#blazor-server) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12f5d-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f5d-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f5d-130">使用 Razor 类库项目模板。</span><span class="sxs-lookup"><span data-stu-id="12f5d-130">Use the **Razor Class Library** project template.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12f5d-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f5d-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="12f5d-132">在“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="12f5d-132">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12f5d-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f5d-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f5d-134">使用 Razor 类库项目模板。</span><span class="sxs-lookup"><span data-stu-id="12f5d-134">Use the **Razor Class Library** project template.</span></span>

---

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="12f5d-135">从项目模板生成的库：</span><span class="sxs-lookup"><span data-stu-id="12f5d-135">The library generated from the project template:</span></span>

* <span data-ttu-id="12f5d-136">基于已安装的 SDK 面向当前的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="12f5d-136">Targets the current .NET framework based on the installed SDK.</span></span>
* <span data-ttu-id="12f5d-137">通过 `SupportedPlatform` MSBuild 项目将 `browser` 包括在内作为支持的平台，启用对平台依赖项的浏览器兼容性检查。</span><span class="sxs-lookup"><span data-stu-id="12f5d-137">Enables browser compatibilty checks for platform dependencies by including `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span>
* <span data-ttu-id="12f5d-138">添加对 [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 的 NuGet 包引用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-138">Adds a NuGet package reference for [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web).</span></span>

<span data-ttu-id="12f5d-139">示例：</span><span class="sxs-lookup"><span data-stu-id="12f5d-139">Example:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Razor">

  <PropertyGroup>
    <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Components.Web" Version="{VERSION}" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="12f5d-140">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="12f5d-140">In the preceding example:</span></span>

* <span data-ttu-id="12f5d-141">`{TARGET FRAMEWORK}` 占位符是 [目标框架名字对象 (TFM) ](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-141">The `{TARGET FRAMEWORK}` placeholder is the [Target Framework Moniker (TFM)](/dotnet/standard/frameworks).</span></span>
* <span data-ttu-id="12f5d-142">`{VERSION}` 占位符是 [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 包的版本。</span><span class="sxs-lookup"><span data-stu-id="12f5d-142">The `{VERSION}` placeholder is the version of the [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) package.</span></span>

### <a name="only-support-the-blazor-server-hosting-model"></a><span data-ttu-id="12f5d-143">仅支持 Blazor Server 托管模型</span><span class="sxs-lookup"><span data-stu-id="12f5d-143">Only support the Blazor Server hosting model</span></span>

<span data-ttu-id="12f5d-144">类库极少只支持 [Blazor Server](xref:blazor/hosting-models#blazor-server) 应用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-144">Class libraries rarely only support [Blazor Server](xref:blazor/hosting-models#blazor-server) apps.</span></span> <span data-ttu-id="12f5d-145">如果类库需要特定于 [Blazor Server](xref:blazor/hosting-models#blazor-server) 的功能（如访问 <xref:Microsoft.AspNetCore.Components.Server.Circuits.CircuitHandler> 或 <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage>），或使用特定于 ASP.NET Core 的功能（如中间件、MVC 控制器或 Razor Pages），请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="12f5d-145">If the class library requires [Blazor Server](xref:blazor/hosting-models#blazor-server)-specific features, such as access to <xref:Microsoft.AspNetCore.Components.Server.Circuits.CircuitHandler>s or <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage>, or uses ASP.NET Core-specific features, such as middleware, MVC controllers, or Razor Pages, use **one** of the following approaches:</span></span>

* <span data-ttu-id="12f5d-146">使用“支持页面和视图”复选框 (Visual Studio) 或 `dotnet new` 命令的 `-s|--support-pages-and-views` 选项创建项目时，指定库支持页面和视图：</span><span class="sxs-lookup"><span data-stu-id="12f5d-146">Specify that the library supports pages and views when the project is created with the **Support pages and views** check box (Visual Studio) or the `-s|--support-pages-and-views` option with the `dotnet new` command:</span></span>

  ```dotnetcli
  dotnet new razorclasslib -s true
  ```

* <span data-ttu-id="12f5d-147">仅在库的项目文件中提供对 ASP.NET Core 的框架引用：</span><span class="sxs-lookup"><span data-stu-id="12f5d-147">Only provide a framework reference to ASP.NET Core in the library's project file:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Razor">

    <ItemGroup>
      <FrameworkReference Include="Microsoft.AspNetCore.App" />
    </ItemGroup>

  </Project>
  ```

### <a name="support-multiple-framework-versions"></a><span data-ttu-id="12f5d-148">支持多个框架版本</span><span class="sxs-lookup"><span data-stu-id="12f5d-148">Support multiple framework versions</span></span>

<span data-ttu-id="12f5d-149">如果库必须支持当前版本向 Blazor 添加的功能，同时还要支持一个或多个早期版本，则让库面向多个目标。</span><span class="sxs-lookup"><span data-stu-id="12f5d-149">If the library must support features added to Blazor in the current release while also supporting one or more earlier releases, multi-target the library.</span></span> <span data-ttu-id="12f5d-150">在 `TargetFrameworks` MSBuild 属性中提供以分号分隔的[目标框架名字对象 (TFM)](/dotnet/standard/frameworks) 列表：</span><span class="sxs-lookup"><span data-stu-id="12f5d-150">Provide a semicolon-separated list of [Target Framework Monikers (TFMs)](/dotnet/standard/frameworks) in the `TargetFrameworks` MSBuild property:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Razor">

  <PropertyGroup>
    <TargetFrameworks>{TARGET FRAMEWORKS}</TargetFrameworks>
  </PropertyGroup>

  <ItemGroup>
    <SupportedPlatform Include="browser" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Components.Web" Version="{VERSION}" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="12f5d-151">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="12f5d-151">In the preceding example:</span></span>

* <span data-ttu-id="12f5d-152">`{TARGET FRAMEWORKS}` 占位符表示以分号分隔的 TFM 列表。</span><span class="sxs-lookup"><span data-stu-id="12f5d-152">The `{TARGET FRAMEWORKS}` placeholder represents the semicolon-separated TFMs list.</span></span> <span data-ttu-id="12f5d-153">例如 `netcoreapp3.1;net5.0`。</span><span class="sxs-lookup"><span data-stu-id="12f5d-153">For example, `netcoreapp3.1;net5.0`.</span></span>
* <span data-ttu-id="12f5d-154">`{VERSION}` 占位符是 [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) 包的版本。</span><span class="sxs-lookup"><span data-stu-id="12f5d-154">The `{VERSION}` placeholder is the version of the [`Microsoft.AspNetCore.Components.Web`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="12f5d-155">从模板生成的项目：</span><span class="sxs-lookup"><span data-stu-id="12f5d-155">The project generated from the template:</span></span>

* <span data-ttu-id="12f5d-156">面向 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="12f5d-156">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="12f5d-157">将 `RazorLangVersion` 属性设置为 `3.0`。</span><span class="sxs-lookup"><span data-stu-id="12f5d-157">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="12f5d-158">.NET Core 3.x 的默认值是 `3.0`。</span><span class="sxs-lookup"><span data-stu-id="12f5d-158">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="12f5d-159">添加以下包引用：</span><span class="sxs-lookup"><span data-stu-id="12f5d-159">Adds the following package references:</span></span>
  * [<span data-ttu-id="12f5d-160">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="12f5d-160">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="12f5d-161">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="12f5d-161">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="12f5d-162">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-162">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="12f5d-163">支持特定托管模型</span><span class="sxs-lookup"><span data-stu-id="12f5d-163">Support a specific hosting model</span></span>

<span data-ttu-id="12f5d-164">支持单个 Blazor 托管模型并不常见。</span><span class="sxs-lookup"><span data-stu-id="12f5d-164">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="12f5d-165">例如，通过完成以下操作来仅支持 [Blazor Server](xref:blazor/hosting-models#blazor-server) 项目的 Razor 组件消耗：</span><span class="sxs-lookup"><span data-stu-id="12f5d-165">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="12f5d-166">面向 .NET Core 3.x。</span><span class="sxs-lookup"><span data-stu-id="12f5d-166">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="12f5d-167">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-167">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="12f5d-168">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-168">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

::: moniker-end

<span data-ttu-id="12f5d-169">有关包含 Razor 组件的库的详细信息，请参阅 <xref:blazor/components/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="12f5d-169">For more information on libraries containing Razor components, see <xref:blazor/components/class-libraries>.</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="12f5d-170">包括 MVC 扩展性</span><span class="sxs-lookup"><span data-stu-id="12f5d-170">Include MVC extensibility</span></span>

<span data-ttu-id="12f5d-171">此部分概述了针对包括以下内容的库的建议：</span><span class="sxs-lookup"><span data-stu-id="12f5d-171">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="12f5d-172">Razor 视图或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="12f5d-172">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="12f5d-173">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="12f5d-173">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="12f5d-174">视图组件</span><span class="sxs-lookup"><span data-stu-id="12f5d-174">View components</span></span>](#view-components)

<span data-ttu-id="12f5d-175">此部分未探讨用于支持多个 MVC 版本的多目标。</span><span class="sxs-lookup"><span data-stu-id="12f5d-175">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="12f5d-176">若要查看关于支持多个 ASP.NET Core 版本的指南，请参阅[支持多个 ASP.NET Core 版本](#support-multiple-aspnet-core-versions)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-176">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="12f5d-177">Razor 视图或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="12f5d-177">Razor views or Razor Pages</span></span>

<span data-ttu-id="12f5d-178">包括 [Razor 视图](xref:mvc/views/overview)或 [Razor 页面](xref:razor-pages/index)的项目必须使用 [Microsoft.NET.Sdk.RazorSDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-178">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="12f5d-179">若项目面向 .NET Core 3.x，它必须满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="12f5d-179">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="12f5d-180">`AddRazorSupportForMvc` MSBuild 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="12f5d-180">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="12f5d-181">具有针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-181">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="12f5d-182">Razor 类库项目模板符合针对面向 .NET Core 3.x 的项目的上述要求。</span><span class="sxs-lookup"><span data-stu-id="12f5d-182">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="12f5d-183">请针对自己的编辑器使用以下说明。</span><span class="sxs-lookup"><span data-stu-id="12f5d-183">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12f5d-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12f5d-184">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12f5d-185">使用 Razor 类库项目模板。</span><span class="sxs-lookup"><span data-stu-id="12f5d-185">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="12f5d-186">应选中此模板的“支持页和视图”复选框。</span><span class="sxs-lookup"><span data-stu-id="12f5d-186">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12f5d-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12f5d-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="12f5d-188">在“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="12f5d-188">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12f5d-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="12f5d-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12f5d-190">此时无任何项目模板支持。</span><span class="sxs-lookup"><span data-stu-id="12f5d-190">No project template support at this time.</span></span>

---

<span data-ttu-id="12f5d-191">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-191">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="12f5d-192">若项目改为面向 .NET Standard，则需要 [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) 包引用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-192">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="12f5d-193">`Microsoft.AspNetCore.Mvc` 包移到了 ASP.NET Core 3.0 的共享框架中，因此不再发布。</span><span class="sxs-lookup"><span data-stu-id="12f5d-193">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="12f5d-194">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-194">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="12f5d-195">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="12f5d-195">Tag Helpers</span></span>

<span data-ttu-id="12f5d-196">包含[标记帮助器](xref:mvc/views/tag-helpers/intro)的项目应使用 `Microsoft.NET.Sdk` SDK。</span><span class="sxs-lookup"><span data-stu-id="12f5d-196">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="12f5d-197">若面向 .NET Core 3.x，则添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-197">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="12f5d-198">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-198">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="12f5d-199">若面向 .NET Standard（以支持 ASP.NET Core 3.x 之前的版本），则添加对 [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-199">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="12f5d-200">`Microsoft.AspNetCore.Mvc.Razor` 包移到了共享框架，因此不再发布。</span><span class="sxs-lookup"><span data-stu-id="12f5d-200">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="12f5d-201">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-201">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="12f5d-202">视图组件</span><span class="sxs-lookup"><span data-stu-id="12f5d-202">View components</span></span>

<span data-ttu-id="12f5d-203">包含[视图组件](xref:mvc/views/view-components)的项目应使用 `Microsoft.NET.Sdk` SDK。</span><span class="sxs-lookup"><span data-stu-id="12f5d-203">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="12f5d-204">若面向 .NET Core 3.x，则添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-204">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="12f5d-205">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-205">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="12f5d-206">若面向 .NET Standard（以支持 ASP.NET Core 3.x 之前的版本），则添加 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-206">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="12f5d-207">`Microsoft.AspNetCore.Mvc.ViewFeatures` 包移到了共享框架，因此不再发布。</span><span class="sxs-lookup"><span data-stu-id="12f5d-207">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="12f5d-208">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-208">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="12f5d-209">支持多个 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="12f5d-209">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="12f5d-210">创作支持多个 ASP.NET Core 变体的库需要多目标。</span><span class="sxs-lookup"><span data-stu-id="12f5d-210">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="12f5d-211">以下列情况为例：标记帮助器库必须支持以下 ASP.NET Core 变体：</span><span class="sxs-lookup"><span data-stu-id="12f5d-211">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="12f5d-212">面向 .NET Framework 4.6.1 的 ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="12f5d-212">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="12f5d-213">面向 .NET Core 2.x 的 ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="12f5d-213">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="12f5d-214">面向 .NET Core 3.x 的 ASP.NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="12f5d-214">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="12f5d-215">以下项目文件通过 `TargetFrameworks` 属性支持这些变体：</span><span class="sxs-lookup"><span data-stu-id="12f5d-215">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="12f5d-216">上面的项目文件将完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="12f5d-216">With the preceding project file:</span></span>

* <span data-ttu-id="12f5d-217">为所有使用者添加 `Markdig` 包。</span><span class="sxs-lookup"><span data-stu-id="12f5d-217">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="12f5d-218">[Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) 的引用</span><span class="sxs-lookup"><span data-stu-id="12f5d-218">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="12f5d-219">已针对面向 .NET Framework 4.6.1 或更高版本或者 .NET Core 2.x 的使用者添加。</span><span class="sxs-lookup"><span data-stu-id="12f5d-219">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="12f5d-220">由于向后兼容性，此包的版本 2.1.0 适用于 ASP.NET Core 2.2。</span><span class="sxs-lookup"><span data-stu-id="12f5d-220">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="12f5d-221">为面向 .NET Core 3.x 的使用者引用共享框架。</span><span class="sxs-lookup"><span data-stu-id="12f5d-221">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="12f5d-222">共享框架中包括 `Microsoft.AspNetCore.Mvc.Razor` 包。</span><span class="sxs-lookup"><span data-stu-id="12f5d-222">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="12f5d-223">或者，可以面向 .NET Standard 2.0，而不面向 .NET Core 2.1 和 .NET Framework 4.6.1：</span><span class="sxs-lookup"><span data-stu-id="12f5d-223">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="12f5d-224">对于上面的项目文件，有以下注意事项：</span><span class="sxs-lookup"><span data-stu-id="12f5d-224">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="12f5d-225">由于库只包含标记帮助器，面向 ASP.NET Core 运行的特定平台（.NET Core 和 .NET Framework）更为简单。</span><span class="sxs-lookup"><span data-stu-id="12f5d-225">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="12f5d-226">其他兼容 .NET Standard 2.0 的目标框架（如 Unity、UWP 和 Xamarin）无法使用标记帮助器。</span><span class="sxs-lookup"><span data-stu-id="12f5d-226">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="12f5d-227">在 .NET Framework 中使用 .NET Standard 2.0 存在一些问题，这些在 .NET Framework 4.7.2 中已得到解决。</span><span class="sxs-lookup"><span data-stu-id="12f5d-227">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="12f5d-228">通过面向 .NET Framework 4.6.1，可以改进使用 .NET Framework 4.6.1 到 4.7.1 的使用者的体验。</span><span class="sxs-lookup"><span data-stu-id="12f5d-228">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="12f5d-229">如果库需要调用平台特定的 API，则面向特定 .NET 实现，而不面向 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="12f5d-229">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="12f5d-230">有关详细信息，请参阅[多目标](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-230">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="12f5d-231">使用未变更的 API</span><span class="sxs-lookup"><span data-stu-id="12f5d-231">Use an API that hasn't changed</span></span>

<span data-ttu-id="12f5d-232">以下面的情况为例：你要将中间件库从 .NET Core 2.2 升级到 3.0。</span><span class="sxs-lookup"><span data-stu-id="12f5d-232">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="12f5d-233">库正在使用的 ASP.NET Core 中间件 API 尚未从 ASP.NET Core 2.2 变更到 3.0。</span><span class="sxs-lookup"><span data-stu-id="12f5d-233">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="12f5d-234">若要继续让 .NET Core 3.0 支持此中间件库，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="12f5d-234">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="12f5d-235">按照[标准库指南](/dotnet/standard/library-guidance/)操作。</span><span class="sxs-lookup"><span data-stu-id="12f5d-235">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="12f5d-236">若共享框架中不存在相应的程序集，则添加针对每个 API 的 NuGet 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-236">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="12f5d-237">使用已变更的 API</span><span class="sxs-lookup"><span data-stu-id="12f5d-237">Use an API that changed</span></span>

<span data-ttu-id="12f5d-238">以下面的情况为例：你要将库从 .NET Core 2.2 升级到 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="12f5d-238">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="12f5d-239">库正在使用的 ASP.NET Core API 包含 ASP.NET Core 3.0 的[中断性变更](/dotnet/core/compatibility/breaking-changes)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-239">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="12f5d-240">请考虑此库是否可以重写为不使用所有版本中已损坏的 API。</span><span class="sxs-lookup"><span data-stu-id="12f5d-240">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="12f5d-241">若可以重写此库，则进行重写，并通过包引用继续面向早期版本的目标框架（例如 .NET Standard 2.0 或 .NET Framework 4.6.1）。</span><span class="sxs-lookup"><span data-stu-id="12f5d-241">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="12f5d-242">若无法重写此库，则执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="12f5d-242">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="12f5d-243">添加针对 .NET Core 3.0 的目标。</span><span class="sxs-lookup"><span data-stu-id="12f5d-243">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="12f5d-244">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-244">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="12f5d-245">结合使用 [#if 预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)和对应的目标框架符号，以进行有条件的代码编译。</span><span class="sxs-lookup"><span data-stu-id="12f5d-245">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="12f5d-246">例如，自 ASP.NET Core 3.0 起默认不可对 HTTP 请求和响应流进行同步读写。</span><span class="sxs-lookup"><span data-stu-id="12f5d-246">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="12f5d-247">默认情况下，ASP.NET Core 2.2 支持同步行为。</span><span class="sxs-lookup"><span data-stu-id="12f5d-247">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="12f5d-248">以一个中间件库为例，在该库中，发生 I/O 时应可进行同步读写。</span><span class="sxs-lookup"><span data-stu-id="12f5d-248">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="12f5d-249">此库应将用于启用同步功能的代码括在相应的预处理器指令中。</span><span class="sxs-lookup"><span data-stu-id="12f5d-249">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="12f5d-250">例如：</span><span class="sxs-lookup"><span data-stu-id="12f5d-250">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="12f5d-251">使用 3.0 引入的 API</span><span class="sxs-lookup"><span data-stu-id="12f5d-251">Use an API introduced in 3.0</span></span>

<span data-ttu-id="12f5d-252">假设你想要使用 ASP.NET Core 3.0 引入的 ASP.NET Core API。</span><span class="sxs-lookup"><span data-stu-id="12f5d-252">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="12f5d-253">请考虑下列问题：</span><span class="sxs-lookup"><span data-stu-id="12f5d-253">Consider the following questions:</span></span>

1. <span data-ttu-id="12f5d-254">此库在功能上是否需要此新 API？</span><span class="sxs-lookup"><span data-stu-id="12f5d-254">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="12f5d-255">库是否可以通过其他方式实现此功能？</span><span class="sxs-lookup"><span data-stu-id="12f5d-255">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="12f5d-256">若此库在功能上需要此 API，并且没有实现它的下层方法：</span><span class="sxs-lookup"><span data-stu-id="12f5d-256">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="12f5d-257">仅面向 .NET Core 3.x。</span><span class="sxs-lookup"><span data-stu-id="12f5d-257">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="12f5d-258">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-258">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="12f5d-259">若此库可以通过其他方式实现此功能：</span><span class="sxs-lookup"><span data-stu-id="12f5d-259">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="12f5d-260">将 .NET Core 3.x 添加为目标框架。</span><span class="sxs-lookup"><span data-stu-id="12f5d-260">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="12f5d-261">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="12f5d-261">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="12f5d-262">结合使用 [#if 预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)和对应的目标框架符号，以进行有条件的代码编译。</span><span class="sxs-lookup"><span data-stu-id="12f5d-262">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="12f5d-263">例如，以下标记帮助器使用 ASP.NET Core 3.0 引入的 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 接口。</span><span class="sxs-lookup"><span data-stu-id="12f5d-263">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="12f5d-264">面向 .NET Core 3.0 的使用者执行 `NETCOREAPP3_0` 目标框架符号定义的代码路径。</span><span class="sxs-lookup"><span data-stu-id="12f5d-264">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="12f5d-265">对于 .NET Core 2.1 和 .NET Framework 4.6.1 使用者，标记帮助器的构造函数参数类型更改为 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>。</span><span class="sxs-lookup"><span data-stu-id="12f5d-265">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="12f5d-266">此更改很有必要，因为 ASP.NET Core 3.0 将 `IHostingEnvironment` 标记为过时，并推荐将 `IWebHostEnvironment` 作为替代项。</span><span class="sxs-lookup"><span data-stu-id="12f5d-266">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="12f5d-267">以下多目标项目文件支持此标记帮助器方案：</span><span class="sxs-lookup"><span data-stu-id="12f5d-267">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="12f5d-268">使用共享框架已删除的 API</span><span class="sxs-lookup"><span data-stu-id="12f5d-268">Use an API removed from the shared framework</span></span>

<span data-ttu-id="12f5d-269">若要使用共享框架已删除的 ASP.NET Core 程序集，请添加相应的包引用。</span><span class="sxs-lookup"><span data-stu-id="12f5d-269">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="12f5d-270">若要查看 ASP.NET Core 3.0 的共享框架已删除的包列表，请参阅[删除过时的包引用](xref:migration/22-to-30#remove-obsolete-package-references)。</span><span class="sxs-lookup"><span data-stu-id="12f5d-270">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="12f5d-271">例如，添加 Web API 客户端：</span><span class="sxs-lookup"><span data-stu-id="12f5d-271">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="12f5d-272">其他资源</span><span class="sxs-lookup"><span data-stu-id="12f5d-272">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="12f5d-273">.NET 实现支持</span><span class="sxs-lookup"><span data-stu-id="12f5d-273">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="12f5d-274">.NET 支持策略</span><span class="sxs-lookup"><span data-stu-id="12f5d-274">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
