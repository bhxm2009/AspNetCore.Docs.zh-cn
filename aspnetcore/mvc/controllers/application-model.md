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
# <a name="work-with-the-application-model-in-aspnet-core"></a>使用 ASP.NET Core 中的应用程序模型

作者：[Steve Smith](https://ardalis.com/)

ASP.NET Core MVC 会定义一个 *应用程序模型*，用于表示 MVC 应用的各个组件。 读取并处理此模型，以修改 MVC 元素的行为方式。 默认情况下，MVC 遵循特定的约定来确定哪些类被视为控制器、这些类上的哪些方法是操作以及参数和路由的行为方式。 自定义此行为以满足应用程序的需求，方法是创建自定义约定，并将其作为全局或特性应用。

## <a name="models-and-providers-iapplicationmodelprovider"></a>模型和提供程序 (`IApplicationModelProvider`) 

ASP.NET Core MVC 应用程序模型包括用于描述 MVC 应用程序的抽象接口和具体实现类。 此模型是 MVC 根据默认约定发现应用的控制器、操作、操作参数、路由和筛选器的结果。 通过使用应用程序模型，修改应用以遵循默认 MVC 行为中的不同约定。 参数、名称、路由和筛选器都用作操作和控制器的配置数据。

ASP.NET Core MVC 应用程序模型具有以下结构：

* ApplicationModel
  * 控制器 (ControllerModel)
    * 操作 (ActionModel)
      * 参数 (ParameterModel)

该模型的每个级别都有权访问公用 `Properties` 集合，层次结构中的较低级别可以访问和覆盖由较高级别设置的属性值。 创建操作时，属性保存到 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> 中。 之后，当处理请求时，可通过 <xref:Microsoft.AspNetCore.Mvc.ActionContext.ActionDescriptor?displayProperty=nameWithType> 访问某个约定添加或修改的任何属性。 使用 "属性" 是一种基于每个操作配置筛选器、模型绑定器和其他应用模型方面的好方法。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType>集合不是线程安全的 (，因此在应用启动后) 写入。 约定是将数据安全添加到此集合的最佳方式。

ASP.NET Core MVC 使用接口定义的提供程序模式加载应用程序模型 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 。 此部分介绍此提供程序的工作原理的一些内部实现细节。 使用提供程序模式是一种高级主题，主要用于框架使用。 大多数应用程序应使用约定，而不是提供程序模式。

接口的实现彼此 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> "换行"，每个实现 <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuting%2A> 基于其属性以升序调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider.Order> 。 然后，按相反的顺序调用 <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuted%2A> 方法。 该框架定义了多个提供程序：

首先 (`Order=-1000`)：

* `DefaultApplicationModelProvider`

然后 (`Order=-990`)：

* `AuthorizationApplicationModelProvider`
* `CorsApplicationModelProvider`

> [!NOTE]
> 调用的具有相同值的两个提供程序的顺序 `Order` 是不确定的，不应依赖于。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 是一种高级概念，框架创建者可对其进行扩展。 通常情况下，应用应使用约定，而框架应使用提供者。 主要不同之处在于提供程序始终先于约定运行。

`DefaultApplicationModelProvider` 建立了由 ASP.NET Core MVC 使用的许多默认行为。 其职责包括：

* 将全局筛选器添加到上下文
* 将控制器添加到上下文
* 将公共控制器方法作为操作添加
* 将操作方法参数添加到上下文
* 应用路由和其他属性

某些内置行为由 `DefaultApplicationModelProvider` 实现。 此提供程序负责构造 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ControllerModel> ，后者又引用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel> 、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PropertyModel> 和 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ParameterModel> 实例。 `DefaultApplicationModelProvider`类是一个内部框架实现细节，将来可能会更改。

`AuthorizationApplicationModelProvider` 负责应用与 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 属性关联的行为。 有关详细信息，请参阅 <xref:security/authorization/simple>。

`CorsApplicationModelProvider`与和关联的实现 <xref:Microsoft.AspNetCore.Cors.Infrastructure.IEnableCorsAttribute> 行为 <xref:Microsoft.AspNetCore.Cors.Infrastructure.IDisableCorsAttribute> 。 有关详细信息，请参阅 <xref:security/cors>。

有关此部分中所述的框架内部提供程序的信息不可通过 [.NET API 浏览器](https://docs.microsoft.com/dotnet/api/)获得。 但是，可以在 [ASP.NET Core 引用源 (dotnet/Aspnetcore GitHub 存储库) ](https://github.com/dotnet/aspnetcore)检查提供程序。 使用 GitHub 搜索按名称查找提供者，并使用 " **切换分支/标记** " 下拉列表选择源的版本。

## <a name="conventions"></a>约定

应用程序模型定义了约定抽象，通过约定抽象来自定义模型行为比重写整个模型或提供程序更简单。 建议使用这些抽象方法修改应用的行为。 约定提供一种方法来编写动态应用自定义项的代码。 尽管 [筛选器](xref:mvc/controllers/filters) 提供了修改框架行为的方法，但自定义允许控制整个应用程序的工作方式。

可用约定如下：

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IControllerModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IActionModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IParameterModelConvention>

约定的应用方法是将它们添加到 MVC 选项，或通过实现属性并将它们应用于控制器、操作或操作参数 (类似于) 的 [筛选器](xref:mvc/controllers/filters) 。与筛选器不同，约定仅在应用启动时执行，而不是在每个请求中执行。

> [!NOTE]
> 有关 Razor 页路由和应用程序模型提供程序约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions> 。

## <a name="modify-the-applicationmodel"></a>修改 `ApplicationModel`

以下约定用于向应用程序模型添加属性：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

当在中添加 MVC 时，应用程序模型约定作为选项应用 `Startup.ConfigureServices` ：

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

可从 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> 控制器操作内的集合访问属性：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

## <a name="modify-the-controllermodel-description"></a>修改 `ControllerModel` 说明

控制器模型还可以包含自定义属性。 自定义属性将覆盖在应用程序模型中指定的同名的现有属性。 以下约定属性可在控制器级别添加说明：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

此约定作为控制器上的属性应用：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

## <a name="modify-the-actionmodel-description"></a>修改 `ActionModel` 说明

可以将单独的特性约定应用于单个操作，重写已应用于应用程序或控制器级别的行为：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

将此应用到控制器中的操作后，会显示它如何替代控制器级别约定：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

## <a name="modify-the-parametermodel"></a>修改 `ParameterModel`

可将以下约定应用于操作参数，以修改其 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindingInfo>。 以下约定要求参数是路由参数。 其他可能的绑定源（如查询字符串值）将被忽略：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

该属性可应用于任何操作参数：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

若要将约定应用于所有操作参数，请将添加 `MustBeInRouteParameterModelConvention` 到 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 中的 `Startup.ConfigureServices` ：

```csharp
options.Conventions.Add(new MustBeInRouteParameterModelConvention());
```

## <a name="modify-the-actionmodel-name"></a>修改 `ActionModel` 名称

以下约定可修改 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel>，以更新其应用到的操作的 *名称*。 新名称以参数形式提供给该属性。 路由使用此新名称，因此它会影响用于访问此操作方法的路由：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

此属性应用于 `HomeController` 中的操作方法：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

即使方法名称为 `SomeName`，该属性也会覆盖 MVC 使用该方法名称的约定，并将操作名称替换为 `MyCoolAction`。 因此，用于访问此操作的路由为 `/Home/MyCoolAction`。

> [!NOTE]
> 本部分中的示例与使用内置的基本相同 <xref:Microsoft.AspNetCore.Mvc.ActionNameAttribute> 。

## <a name="custom-routing-convention"></a>自定义路由约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention> 自定义路由的工作方式。 例如，以下约定将控制器的命名空间合并到它们的路由中，并将命名空间中的替换为 `.` `/` 路由：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

此约定作为选项添加到 `Startup.ConfigureServices` ：

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> 使用以下方法向 [中间件](xref:fundamentals/middleware/index) 添加约定 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 。 `{CONVENTION}`占位符是要添加的约定：
>
> ```csharp
> services.Configure<MvcOptions>(c => c.Conventions.Add({CONVENTION}));
> ```

下面的示例将约定应用于不使用属性路由的路由，其中控制器的  `Namespace` 名称如下所示：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

::: moniker range="<= aspnetcore-2.2"

## <a name="application-model-usage-in-webapicompatshim"></a>应用程序模型用法 `WebApiCompatShim`

ASP.NET Core MVC 使用一组不同于 ASP.NET Web API 2 的约定。 使用自定义约定，可以修改 ASP.NET Core MVC 应用的行为，使其与 web API 应用的行为一致。 Microsoft 为此目的提供了[ `WebApiCompatShim` NuGet 包](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。

> [!NOTE]
> 有关从 ASP.NET Web API 迁移的详细信息，请参阅 <xref:migration/webapi> 。

使用 Web API 兼容性填充码：

* 将 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 包添加到项目。
* 通过在中调用来向 MVC 添加约定 <xref:Microsoft.Extensions.DependencyInjection.WebApiCompatShimMvcBuilderExtensions.AddWebApiConventions%2A> `Startup.ConfigureServices` ：

```csharp
services.AddMvc().AddWebApiConventions();
```

该填充程序提供的约定仅适用于应用中已应用特定属性的部分。 以下四个属性用于控制哪些控制器应使用该填充程序的约定来修改自己的约定：

* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute>

### <a name="action-conventions"></a>操作约定

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute> 用于根据 (实例将 HTTP 方法映射到操作， `Get` 将映射到 `HttpGet`) 。 它仅适用于不使用属性路由的操作。

### <a name="overloading"></a>重载

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute> 用于应用 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiOverloadingApplicationModelConvention> 约定。 此约定可向操作选择过程添加 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.OverloadActionConstraint>，以将候选操作限制为其请求满足所有非可选参数的操作。

### <a name="parameter-conventions"></a>参数约定

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute> 用于应用 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiParameterConventionsApplicationModelConvention> 操作约定。 此约定指定用作操作参数的简单类型默认来自 URI，而复杂类型来自请求正文。

### <a name="routes"></a>路由

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute> 控制是否 `WebApiApplicationModelConvention` 应用控制器约定。 如果启用此约定，则会将对 [区域](xref:mvc/controllers/areas) 的支持添加到路由，并指示控制器位于 `api` 区域。

除了一组约定外，兼容性包还包括一个 <xref:System.Web.Http.ApiController?displayProperty=fullName> 替换 WEB API 提供的基类。 这允许为 web API 编写 web API 控制器，并 `ApiController` 在 ASP.NET CORE MVC 上运行时从其继承到工作。 [`UseWebApi*`](xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim)前面列出的所有属性将应用于基控制器类。 `ApiController`公开与在 WEB API 中找到的属性、方法和结果类型兼容的属性、方法和结果类型。

::: moniker-end

## <a name="use-apiexplorer-to-document-an-app"></a>用于 `ApiExplorer` 记录应用

应用程序模型在每个级别公开了 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel> 属性，该属性可用于遍历应用的结构。 这可用于 [使用 Swagger 等工具生成 Web api 的帮助页](xref:tutorials/web-api-help-pages-using-swagger)。 `ApiExplorer`属性公开一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel.IsVisible> 属性，该属性可设置为指定应公开应用程序模型的哪些部分。 使用约定配置此设置：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

如果需要) ，使用此方法 (和其他约定，在应用内的任何级别启用或禁用 API 可见性。
