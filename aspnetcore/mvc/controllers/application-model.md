---
title: 使用 ASP.NET Core 中的应用程序模型
author: rick-anderson
description: 了解如何读取和控制应用程序模型，从而修改 MVC 元素在 ASP.NET Core 中的行为。
ms.author: riande
ms.date: 04/05/2021
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
uid: mvc/controllers/application-model
ms.openlocfilehash: 8772a432f137fd6d0278208963bee03f6e8a715b
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564040"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="11ce6-103">使用 ASP.NET Core 中的应用程序模型</span><span class="sxs-lookup"><span data-stu-id="11ce6-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="11ce6-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="11ce6-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="11ce6-105">ASP.NET Core MVC 会定义一个 *应用程序模型*，用于表示 MVC 应用的各个组件。</span><span class="sxs-lookup"><span data-stu-id="11ce6-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="11ce6-106">读取并处理此模型，以修改 MVC 元素的行为方式。</span><span class="sxs-lookup"><span data-stu-id="11ce6-106">Read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="11ce6-107">默认情况下，MVC 遵循特定的约定来确定哪些类被视为控制器、这些类上的哪些方法是操作以及参数和路由的行为方式。</span><span class="sxs-lookup"><span data-stu-id="11ce6-107">By default, MVC follows certain conventions to determine which classes are considered controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="11ce6-108">自定义此行为以满足应用程序的需求，方法是创建自定义约定，并将其作为全局或特性应用。</span><span class="sxs-lookup"><span data-stu-id="11ce6-108">Customize this behavior to suit an app's needs by creating custom conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers-iapplicationmodelprovider"></a><span data-ttu-id="11ce6-109">模型和提供程序 (`IApplicationModelProvider`) </span><span class="sxs-lookup"><span data-stu-id="11ce6-109">Models and Providers (`IApplicationModelProvider`)</span></span>

<span data-ttu-id="11ce6-110">ASP.NET Core MVC 应用程序模型包括用于描述 MVC 应用程序的抽象接口和具体实现类。</span><span class="sxs-lookup"><span data-stu-id="11ce6-110">The ASP.NET Core MVC application model includes both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="11ce6-111">此模型是 MVC 根据默认约定发现应用的控制器、操作、操作参数、路由和筛选器的结果。</span><span class="sxs-lookup"><span data-stu-id="11ce6-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="11ce6-112">通过使用应用程序模型，修改应用以遵循默认 MVC 行为中的不同约定。</span><span class="sxs-lookup"><span data-stu-id="11ce6-112">By working with the application model, modify an app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="11ce6-113">参数、名称、路由和筛选器都用作操作和控制器的配置数据。</span><span class="sxs-lookup"><span data-stu-id="11ce6-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="11ce6-114">ASP.NET Core MVC 应用程序模型具有以下结构：</span><span class="sxs-lookup"><span data-stu-id="11ce6-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="11ce6-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="11ce6-115">ApplicationModel</span></span>
  * <span data-ttu-id="11ce6-116">控制器 (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="11ce6-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="11ce6-117">操作 (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="11ce6-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="11ce6-118">参数 (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="11ce6-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="11ce6-119">该模型的每个级别都有权访问公用 `Properties` 集合，层次结构中的较低级别可以访问和覆盖由较高级别设置的属性值。</span><span class="sxs-lookup"><span data-stu-id="11ce6-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="11ce6-120">创建操作时，属性保存到 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> 中。</span><span class="sxs-lookup"><span data-stu-id="11ce6-120">The properties are persisted to the <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> when the actions are created.</span></span> <span data-ttu-id="11ce6-121">之后，当处理请求时，可通过 <xref:Microsoft.AspNetCore.Mvc.ActionContext.ActionDescriptor?displayProperty=nameWithType> 访问某个约定添加或修改的任何属性。</span><span class="sxs-lookup"><span data-stu-id="11ce6-121">Then when a request is being handled, any properties a convention added or modified can be accessed through <xref:Microsoft.AspNetCore.Mvc.ActionContext.ActionDescriptor?displayProperty=nameWithType>.</span></span> <span data-ttu-id="11ce6-122">使用 "属性" 是一种基于每个操作配置筛选器、模型绑定器和其他应用模型方面的好方法。</span><span class="sxs-lookup"><span data-stu-id="11ce6-122">Using properties is a great way to configure filters, model binders, and other app model aspects on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="11ce6-123"><xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType>集合不是线程安全的 (，因此在应用启动后) 写入。</span><span class="sxs-lookup"><span data-stu-id="11ce6-123">The <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> collection isn't thread safe (for writes) after app startup.</span></span> <span data-ttu-id="11ce6-124">约定是将数据安全添加到此集合的最佳方式。</span><span class="sxs-lookup"><span data-stu-id="11ce6-124">Conventions are the best way to safely add data to this collection.</span></span>

<span data-ttu-id="11ce6-125">ASP.NET Core MVC 使用接口定义的提供程序模式加载应用程序模型 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-125">ASP.NET Core MVC loads the application model using a provider pattern, defined by the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> interface.</span></span> <span data-ttu-id="11ce6-126">此部分介绍此提供程序的工作原理的一些内部实现细节。</span><span class="sxs-lookup"><span data-stu-id="11ce6-126">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="11ce6-127">使用提供程序模式是一种高级主题，主要用于框架使用。</span><span class="sxs-lookup"><span data-stu-id="11ce6-127">Use of the provider pattern is an advanced subject, primarily for framework use.</span></span> <span data-ttu-id="11ce6-128">大多数应用程序应使用约定，而不是提供程序模式。</span><span class="sxs-lookup"><span data-stu-id="11ce6-128">Most apps should use conventions, not the provider pattern.</span></span>

<span data-ttu-id="11ce6-129">接口的实现彼此 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> "换行"，每个实现 <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuting%2A> 基于其属性以升序调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider.Order> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-129">Implementations of the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> interface "wrap" one another, where each implementation calls <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuting%2A> in ascending order based on its <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider.Order> property.</span></span> <span data-ttu-id="11ce6-130">然后，按相反的顺序调用 <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuted%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="11ce6-130">The <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuted%2A> method is then called in reverse order.</span></span> <span data-ttu-id="11ce6-131">该框架定义了多个提供程序：</span><span class="sxs-lookup"><span data-stu-id="11ce6-131">The framework defines several providers:</span></span>

<span data-ttu-id="11ce6-132">首先 (`Order=-1000`)：</span><span class="sxs-lookup"><span data-stu-id="11ce6-132">First (`Order=-1000`):</span></span>

* `DefaultApplicationModelProvider`

<span data-ttu-id="11ce6-133">然后 (`Order=-990`)：</span><span class="sxs-lookup"><span data-stu-id="11ce6-133">Then (`Order=-990`):</span></span>

* `AuthorizationApplicationModelProvider`
* `CorsApplicationModelProvider`

> [!NOTE]
> <span data-ttu-id="11ce6-134">调用的具有相同值的两个提供程序的顺序 `Order` 是不确定的，不应依赖于。</span><span class="sxs-lookup"><span data-stu-id="11ce6-134">The order in which two providers with the same value for `Order` are called is undefined and shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="11ce6-135"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 是一种高级概念，框架创建者可对其进行扩展。</span><span class="sxs-lookup"><span data-stu-id="11ce6-135"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="11ce6-136">通常情况下，应用应使用约定，而框架应使用提供者。</span><span class="sxs-lookup"><span data-stu-id="11ce6-136">In general, apps should use conventions, and frameworks should use providers.</span></span> <span data-ttu-id="11ce6-137">主要不同之处在于提供程序始终先于约定运行。</span><span class="sxs-lookup"><span data-stu-id="11ce6-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="11ce6-138">`DefaultApplicationModelProvider` 建立了由 ASP.NET Core MVC 使用的许多默认行为。</span><span class="sxs-lookup"><span data-stu-id="11ce6-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="11ce6-139">其职责包括：</span><span class="sxs-lookup"><span data-stu-id="11ce6-139">Its responsibilities include:</span></span>

* <span data-ttu-id="11ce6-140">将全局筛选器添加到上下文</span><span class="sxs-lookup"><span data-stu-id="11ce6-140">Adding global filters to the context</span></span>
* <span data-ttu-id="11ce6-141">将控制器添加到上下文</span><span class="sxs-lookup"><span data-stu-id="11ce6-141">Adding controllers to the context</span></span>
* <span data-ttu-id="11ce6-142">将公共控制器方法作为操作添加</span><span class="sxs-lookup"><span data-stu-id="11ce6-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="11ce6-143">将操作方法参数添加到上下文</span><span class="sxs-lookup"><span data-stu-id="11ce6-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="11ce6-144">应用路由和其他属性</span><span class="sxs-lookup"><span data-stu-id="11ce6-144">Applying route and other attributes</span></span>

<span data-ttu-id="11ce6-145">某些内置行为由 `DefaultApplicationModelProvider` 实现。</span><span class="sxs-lookup"><span data-stu-id="11ce6-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="11ce6-146">此提供程序负责构造 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ControllerModel> ，后者又引用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel> 、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PropertyModel> 和 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ParameterModel> 实例。</span><span class="sxs-lookup"><span data-stu-id="11ce6-146">This provider is responsible for constructing the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ControllerModel>, which in turn references <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel>, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PropertyModel>, and <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ParameterModel> instances.</span></span> <span data-ttu-id="11ce6-147">`DefaultApplicationModelProvider`类是一个内部框架实现细节，将来可能会更改。</span><span class="sxs-lookup"><span data-stu-id="11ce6-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that may change in the future.</span></span>

<span data-ttu-id="11ce6-148">`AuthorizationApplicationModelProvider` 负责应用与 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 属性关联的行为。</span><span class="sxs-lookup"><span data-stu-id="11ce6-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> attributes.</span></span> <span data-ttu-id="11ce6-149">有关详细信息，请参阅 <xref:security/authorization/simple>。</span><span class="sxs-lookup"><span data-stu-id="11ce6-149">For more information, see <xref:security/authorization/simple>.</span></span>

<span data-ttu-id="11ce6-150">`CorsApplicationModelProvider`与和关联的实现 <xref:Microsoft.AspNetCore.Cors.Infrastructure.IEnableCorsAttribute> 行为 <xref:Microsoft.AspNetCore.Cors.Infrastructure.IDisableCorsAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-150">The `CorsApplicationModelProvider` implements behavior associated with <xref:Microsoft.AspNetCore.Cors.Infrastructure.IEnableCorsAttribute> and <xref:Microsoft.AspNetCore.Cors.Infrastructure.IDisableCorsAttribute>.</span></span> <span data-ttu-id="11ce6-151">有关详细信息，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="11ce6-151">For more information, see <xref:security/cors>.</span></span>

<span data-ttu-id="11ce6-152">有关此部分中所述的框架内部提供程序的信息不可通过 [.NET API 浏览器](https://docs.microsoft.com/dotnet/api/)获得。</span><span class="sxs-lookup"><span data-stu-id="11ce6-152">Information on the framework's internal providers described in this section aren't available via the [.NET API browser](https://docs.microsoft.com/dotnet/api/).</span></span> <span data-ttu-id="11ce6-153">但是，可以在 [ASP.NET Core 引用源 (dotnet/Aspnetcore GitHub 存储库) ](https://github.com/dotnet/aspnetcore)检查提供程序。</span><span class="sxs-lookup"><span data-stu-id="11ce6-153">However, the providers may be inspected in the [ASP.NET Core reference source (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="11ce6-154">使用 GitHub 搜索按名称查找提供者，并使用 " **切换分支/标记** " 下拉列表选择源的版本。</span><span class="sxs-lookup"><span data-stu-id="11ce6-154">Use GitHub search to find the providers by name and select the version of the source with the **Switch branches/tags** dropdown list.</span></span>

## <a name="conventions"></a><span data-ttu-id="11ce6-155">约定</span><span class="sxs-lookup"><span data-stu-id="11ce6-155">Conventions</span></span>

<span data-ttu-id="11ce6-156">应用程序模型定义了约定抽象，通过约定抽象来自定义模型行为比重写整个模型或提供程序更简单。</span><span class="sxs-lookup"><span data-stu-id="11ce6-156">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="11ce6-157">建议使用这些抽象方法修改应用的行为。</span><span class="sxs-lookup"><span data-stu-id="11ce6-157">These abstractions are the recommended way to modify an app's behavior.</span></span> <span data-ttu-id="11ce6-158">约定提供一种方法来编写动态应用自定义项的代码。</span><span class="sxs-lookup"><span data-stu-id="11ce6-158">Conventions provide a way to write code that dynamically applies customizations.</span></span> <span data-ttu-id="11ce6-159">尽管 [筛选器](xref:mvc/controllers/filters) 提供了修改框架行为的方法，但自定义允许控制整个应用程序的工作方式。</span><span class="sxs-lookup"><span data-stu-id="11ce6-159">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations permit control over how the whole app works together.</span></span>

<span data-ttu-id="11ce6-160">可用约定如下：</span><span class="sxs-lookup"><span data-stu-id="11ce6-160">The following conventions are available:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IControllerModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IActionModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IParameterModelConvention>

<span data-ttu-id="11ce6-161">约定的应用方法是将它们添加到 MVC 选项，或通过实现属性并将它们应用于控制器、操作或操作参数 (类似于) 的 [筛选器](xref:mvc/controllers/filters) 。与筛选器不同，约定仅在应用启动时执行，而不是在每个请求中执行。</span><span class="sxs-lookup"><span data-stu-id="11ce6-161">Conventions are applied by adding them to MVC options or by implementing attributes and applying them to controllers, actions, or action parameters (similar to [filters](xref:mvc/controllers/filters)).Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

> [!NOTE]
> <span data-ttu-id="11ce6-162">有关 Razor 页路由和应用程序模型提供程序约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-162">For information on Razor Pages route and application model provider conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="modify-the-applicationmodel"></a><span data-ttu-id="11ce6-163">修改 `ApplicationModel`</span><span class="sxs-lookup"><span data-stu-id="11ce6-163">Modify the `ApplicationModel`</span></span>

<span data-ttu-id="11ce6-164">以下约定用于向应用程序模型添加属性：</span><span class="sxs-lookup"><span data-stu-id="11ce6-164">The following convention is used to add a property to the application model:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="11ce6-165">当在中添加 MVC 时，应用程序模型约定作为选项应用 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="11ce6-165">Application model conventions are applied as options when MVC is added in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="11ce6-166">可从 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> 控制器操作内的集合访问属性：</span><span class="sxs-lookup"><span data-stu-id="11ce6-166">Properties are accessible from the <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

## <a name="modify-the-controllermodel-description"></a><span data-ttu-id="11ce6-167">修改 `ControllerModel` 说明</span><span class="sxs-lookup"><span data-stu-id="11ce6-167">Modify the `ControllerModel` description</span></span>

<span data-ttu-id="11ce6-168">控制器模型还可以包含自定义属性。</span><span class="sxs-lookup"><span data-stu-id="11ce6-168">The controller model can also include custom properties.</span></span> <span data-ttu-id="11ce6-169">自定义属性将覆盖在应用程序模型中指定的同名的现有属性。</span><span class="sxs-lookup"><span data-stu-id="11ce6-169">Custom properties override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="11ce6-170">以下约定属性可在控制器级别添加说明：</span><span class="sxs-lookup"><span data-stu-id="11ce6-170">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="11ce6-171">此约定作为控制器上的属性应用：</span><span class="sxs-lookup"><span data-stu-id="11ce6-171">This convention is applied as an attribute on a controller:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

## <a name="modify-the-actionmodel-description"></a><span data-ttu-id="11ce6-172">修改 `ActionModel` 说明</span><span class="sxs-lookup"><span data-stu-id="11ce6-172">Modify the `ActionModel` description</span></span>

<span data-ttu-id="11ce6-173">可以将单独的特性约定应用于单个操作，重写已应用于应用程序或控制器级别的行为：</span><span class="sxs-lookup"><span data-stu-id="11ce6-173">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="11ce6-174">将此应用到控制器中的操作后，会显示它如何替代控制器级别约定：</span><span class="sxs-lookup"><span data-stu-id="11ce6-174">Applying this to an action within the controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

## <a name="modify-the-parametermodel"></a><span data-ttu-id="11ce6-175">修改 `ParameterModel`</span><span class="sxs-lookup"><span data-stu-id="11ce6-175">Modify the `ParameterModel`</span></span>

<span data-ttu-id="11ce6-176">可将以下约定应用于操作参数，以修改其 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindingInfo>。</span><span class="sxs-lookup"><span data-stu-id="11ce6-176">The following convention can be applied to action parameters to modify their <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindingInfo>.</span></span> <span data-ttu-id="11ce6-177">以下约定要求参数是路由参数。</span><span class="sxs-lookup"><span data-stu-id="11ce6-177">The following convention requires that the parameter be a route parameter.</span></span> <span data-ttu-id="11ce6-178">其他可能的绑定源（如查询字符串值）将被忽略：</span><span class="sxs-lookup"><span data-stu-id="11ce6-178">Other potential binding sources, such as query string values, are ignored:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="11ce6-179">该属性可应用于任何操作参数：</span><span class="sxs-lookup"><span data-stu-id="11ce6-179">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

<span data-ttu-id="11ce6-180">若要将约定应用于所有操作参数，请将添加 `MustBeInRouteParameterModelConvention` 到 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 中的 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="11ce6-180">To apply the convention to all action parameters, add the `MustBeInRouteParameterModelConvention` to <xref:Microsoft.AspNetCore.Mvc.MvcOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
options.Conventions.Add(new MustBeInRouteParameterModelConvention());
```

## <a name="modify-the-actionmodel-name"></a><span data-ttu-id="11ce6-181">修改 `ActionModel` 名称</span><span class="sxs-lookup"><span data-stu-id="11ce6-181">Modify the `ActionModel` name</span></span>

<span data-ttu-id="11ce6-182">以下约定可修改 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel>，以更新其应用到的操作的 *名称*。</span><span class="sxs-lookup"><span data-stu-id="11ce6-182">The following convention modifies the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel> to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="11ce6-183">新名称以参数形式提供给该属性。</span><span class="sxs-lookup"><span data-stu-id="11ce6-183">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="11ce6-184">路由使用此新名称，因此它会影响用于访问此操作方法的路由：</span><span class="sxs-lookup"><span data-stu-id="11ce6-184">This new name is used by routing, so it affects the route used to reach this action method:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="11ce6-185">此属性应用于 `HomeController` 中的操作方法：</span><span class="sxs-lookup"><span data-stu-id="11ce6-185">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="11ce6-186">即使方法名称为 `SomeName`，该属性也会覆盖 MVC 使用该方法名称的约定，并将操作名称替换为 `MyCoolAction`。</span><span class="sxs-lookup"><span data-stu-id="11ce6-186">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="11ce6-187">因此，用于访问此操作的路由为 `/Home/MyCoolAction`。</span><span class="sxs-lookup"><span data-stu-id="11ce6-187">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="11ce6-188">本部分中的示例与使用内置的基本相同 <xref:Microsoft.AspNetCore.Mvc.ActionNameAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-188">This example in this section is essentially the same as using the built-in <xref:Microsoft.AspNetCore.Mvc.ActionNameAttribute>.</span></span>

## <a name="custom-routing-convention"></a><span data-ttu-id="11ce6-189">自定义路由约定</span><span class="sxs-lookup"><span data-stu-id="11ce6-189">Custom routing convention</span></span>

<span data-ttu-id="11ce6-190">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention> 自定义路由的工作方式。</span><span class="sxs-lookup"><span data-stu-id="11ce6-190">Use an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention> to customize how routing works.</span></span> <span data-ttu-id="11ce6-191">例如，以下约定将控制器的命名空间合并到它们的路由中，并将命名空间中的替换为 `.` `/` 路由：</span><span class="sxs-lookup"><span data-stu-id="11ce6-191">For example, the following convention incorporates controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="11ce6-192">此约定作为选项添加到 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="11ce6-192">The convention is added as an option in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="11ce6-193">使用以下方法向 [中间件](xref:fundamentals/middleware/index) 添加约定 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-193">Add conventions to [middleware](xref:fundamentals/middleware/index) via <xref:Microsoft.AspNetCore.Mvc.MvcOptions> using the following approach.</span></span> <span data-ttu-id="11ce6-194">`{CONVENTION}`占位符是要添加的约定：</span><span class="sxs-lookup"><span data-stu-id="11ce6-194">The `{CONVENTION}` placeholder is the convention to add:</span></span>
>
> ```csharp
> services.Configure<MvcOptions>(c => c.Conventions.Add({CONVENTION}));
> ```

<span data-ttu-id="11ce6-195">下面的示例将约定应用于不使用属性路由的路由，其中控制器的  `Namespace` 名称如下所示：</span><span class="sxs-lookup"><span data-stu-id="11ce6-195">The following example applies a convention to routes that aren't using attribute routing where the controller has  `Namespace` in its name:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

::: moniker range="<= aspnetcore-2.2"

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="11ce6-196">应用程序模型用法 `WebApiCompatShim`</span><span class="sxs-lookup"><span data-stu-id="11ce6-196">Application model usage in `WebApiCompatShim`</span></span>

<span data-ttu-id="11ce6-197">ASP.NET Core MVC 使用一组不同于 ASP.NET Web API 2 的约定。</span><span class="sxs-lookup"><span data-stu-id="11ce6-197">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="11ce6-198">使用自定义约定，可以修改 ASP.NET Core MVC 应用的行为，使其与 web API 应用的行为一致。</span><span class="sxs-lookup"><span data-stu-id="11ce6-198">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a web API app.</span></span> <span data-ttu-id="11ce6-199">Microsoft 为此目的提供了[ `WebApiCompatShim` NuGet 包](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="11ce6-199">Microsoft ships the [`WebApiCompatShim` NuGet package](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="11ce6-200">有关从 ASP.NET Web API 迁移的详细信息，请参阅 <xref:migration/webapi> 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-200">For more information on migration from ASP.NET Web API, see <xref:migration/webapi>.</span></span>

<span data-ttu-id="11ce6-201">使用 Web API 兼容性填充码：</span><span class="sxs-lookup"><span data-stu-id="11ce6-201">To use the Web API Compatibility Shim:</span></span>

* <span data-ttu-id="11ce6-202">将 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="11ce6-202">Add the `Microsoft.AspNetCore.Mvc.WebApiCompatShim` package to the project.</span></span>
* <span data-ttu-id="11ce6-203">通过在中调用来向 MVC 添加约定 <xref:Microsoft.Extensions.DependencyInjection.WebApiCompatShimMvcBuilderExtensions.AddWebApiConventions%2A> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="11ce6-203">Add the conventions to MVC by calling <xref:Microsoft.Extensions.DependencyInjection.WebApiCompatShimMvcBuilderExtensions.AddWebApiConventions%2A> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="11ce6-204">该填充程序提供的约定仅适用于应用中已应用特定属性的部分。</span><span class="sxs-lookup"><span data-stu-id="11ce6-204">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="11ce6-205">以下四个属性用于控制哪些控制器应使用该填充程序的约定来修改自己的约定：</span><span class="sxs-lookup"><span data-stu-id="11ce6-205">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute>

### <a name="action-conventions"></a><span data-ttu-id="11ce6-206">操作约定</span><span class="sxs-lookup"><span data-stu-id="11ce6-206">Action conventions</span></span>

<span data-ttu-id="11ce6-207"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute> 用于根据 (实例将 HTTP 方法映射到操作， `Get` 将映射到 `HttpGet`) 。</span><span class="sxs-lookup"><span data-stu-id="11ce6-207"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute> is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="11ce6-208">它仅适用于不使用属性路由的操作。</span><span class="sxs-lookup"><span data-stu-id="11ce6-208">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="11ce6-209">重载</span><span class="sxs-lookup"><span data-stu-id="11ce6-209">Overloading</span></span>

<span data-ttu-id="11ce6-210"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute> 用于应用 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiOverloadingApplicationModelConvention> 约定。</span><span class="sxs-lookup"><span data-stu-id="11ce6-210"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute> is used to apply the <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiOverloadingApplicationModelConvention> convention.</span></span> <span data-ttu-id="11ce6-211">此约定可向操作选择过程添加 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.OverloadActionConstraint>，以将候选操作限制为其请求满足所有非可选参数的操作。</span><span class="sxs-lookup"><span data-stu-id="11ce6-211">This convention adds an <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.OverloadActionConstraint> to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="11ce6-212">参数约定</span><span class="sxs-lookup"><span data-stu-id="11ce6-212">Parameter conventions</span></span>

<span data-ttu-id="11ce6-213"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute> 用于应用 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiParameterConventionsApplicationModelConvention> 操作约定。</span><span class="sxs-lookup"><span data-stu-id="11ce6-213"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute> is used to apply the <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiParameterConventionsApplicationModelConvention> action convention.</span></span> <span data-ttu-id="11ce6-214">此约定指定用作操作参数的简单类型默认来自 URI，而复杂类型来自请求正文。</span><span class="sxs-lookup"><span data-stu-id="11ce6-214">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="11ce6-215">路由</span><span class="sxs-lookup"><span data-stu-id="11ce6-215">Routes</span></span>

<span data-ttu-id="11ce6-216"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute> 控制是否 `WebApiApplicationModelConvention` 应用控制器约定。</span><span class="sxs-lookup"><span data-stu-id="11ce6-216"><xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute> controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="11ce6-217">如果启用此约定，则会将对 [区域](xref:mvc/controllers/areas) 的支持添加到路由，并指示控制器位于 `api` 区域。</span><span class="sxs-lookup"><span data-stu-id="11ce6-217">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route and indicates the controller is in the `api` area.</span></span>

<span data-ttu-id="11ce6-218">除了一组约定外，兼容性包还包括一个 <xref:System.Web.Http.ApiController?displayProperty=fullName> 替换 WEB API 提供的基类。</span><span class="sxs-lookup"><span data-stu-id="11ce6-218">In addition to a set of conventions, the compatibility package includes a <xref:System.Web.Http.ApiController?displayProperty=fullName> base class that replaces the one provided by web API.</span></span> <span data-ttu-id="11ce6-219">这允许为 web API 编写 web API 控制器，并 `ApiController` 在 ASP.NET CORE MVC 上运行时从其继承到工作。</span><span class="sxs-lookup"><span data-stu-id="11ce6-219">This allows your web API controllers written for web API and inheriting from its `ApiController` to work while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="11ce6-220">[`UseWebApi*`](xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim)前面列出的所有属性将应用于基控制器类。</span><span class="sxs-lookup"><span data-stu-id="11ce6-220">All of the [`UseWebApi*`](xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim) attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="11ce6-221">`ApiController`公开与在 WEB API 中找到的属性、方法和结果类型兼容的属性、方法和结果类型。</span><span class="sxs-lookup"><span data-stu-id="11ce6-221">The `ApiController` exposes properties, methods, and result types that are compatible with those found in web API.</span></span>

::: moniker-end

## <a name="use-apiexplorer-to-document-an-app"></a><span data-ttu-id="11ce6-222">用于 `ApiExplorer` 记录应用</span><span class="sxs-lookup"><span data-stu-id="11ce6-222">Use `ApiExplorer` to document an app</span></span>

<span data-ttu-id="11ce6-223">应用程序模型在每个级别公开了 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel> 属性，该属性可用于遍历应用的结构。</span><span class="sxs-lookup"><span data-stu-id="11ce6-223">The application model exposes an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel> property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="11ce6-224">这可用于 [使用 Swagger 等工具生成 Web api 的帮助页](xref:tutorials/web-api-help-pages-using-swagger)。</span><span class="sxs-lookup"><span data-stu-id="11ce6-224">This can be used to [generate help pages for web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="11ce6-225">`ApiExplorer`属性公开一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel.IsVisible> 属性，该属性可设置为指定应公开应用程序模型的哪些部分。</span><span class="sxs-lookup"><span data-stu-id="11ce6-225">The `ApiExplorer` property exposes an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel.IsVisible> property that can be set to specify which parts of the app's model should be exposed.</span></span> <span data-ttu-id="11ce6-226">使用约定配置此设置：</span><span class="sxs-lookup"><span data-stu-id="11ce6-226">Configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="11ce6-227">如果需要) ，使用此方法 (和其他约定，在应用内的任何级别启用或禁用 API 可见性。</span><span class="sxs-lookup"><span data-stu-id="11ce6-227">Using this approach (and additional conventions if required), API visibility is enabled or disabled at any level within an app.</span></span>
