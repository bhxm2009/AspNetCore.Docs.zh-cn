---
title: ASP.NET Core 中的捆绑和缩小静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩小技术优化 ASP.NET Core Web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/14/2021
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: d594bbf277907e22b0299b0451e480e9d533d506
ms.sourcegitcommit: 00368bb6a5420983beaced5b62dabc1f94abdeba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557798"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core 中的捆绑和缩小静态资产

作者：[Scott Addie](https://twitter.com/Scott_Addie) 和 [David Pine](https://twitter.com/davidpine7)

本文介绍应用捆绑和缩小的好处，包括如何在 ASP.NET Core Web 应用中使用这些功能。

## <a name="what-is-bundling-and-minification"></a>什么是捆绑和缩小

捆绑和缩小是可以在 Web 应用中应用的两个不同的性能优化。 捆绑和缩小一起使用，可减少服务器的请求数并减小请求的静态资产的大小，从而提高性能。

捆绑和缩小主要缩短第一个页面请求加载时间。 请求网页后，浏览器会缓存静态资产（JavaScript、CSS 和图像）。 因此，在请求相同资产的同一站点上请求相同的一个或多个页面时，捆绑和缩小不会提高性能。 如果未在资产上正确设置 expires 标头，且未使用捆绑和缩小，则浏览器的新鲜度启发会在几天后将资产标记为过期。 此外，浏览器还需要对每个资产进行验证请求。 在这种情况下，即使在第一个页面请求后，捆绑和缩小仍能提高性能。

### <a name="bundling"></a>捆绑

捆绑将多个文件合并到单个文件中。 捆绑可减少呈现 Web 资产（如网页）所需的服务器请求数。 可以专门为 CSS、JavaScript 等创建任意数量的单个捆绑。文件越少，从浏览器发送到服务器或从提供应用程序的服务发送的 HTTP 请求就越少。 这会提高第一页加载性能。

### <a name="minification"></a>缩小

缩小在不更改功能的情况下从代码中删除不必要的字符。 因此，请求的资产（如 CSS、图像和 JavaScript 文件）的大小大幅减小。 缩小的常见副作用包括将变量名称缩短为一个字符、删除注释和不必要的空格。

考虑以下 JavaScript 函数：

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
    ///<signature>
    ///<summary> Adds an alt tab to the image
    // </summary>
    //<param name="imgElement" type="String">The image selector.</param>
    //<param name="ContextForImage" type="String">The image context.</param>
    ///</signature>
    var imageElement = $(imageTagAndImageID, imageContext);
    imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

缩小将函数缩减为以下内容：

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

除了删除注释和不必要的空格外，还进行了以下参数和变量名称重命名：

原始 | 重命名
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>捆绑和缩小的影响

下表概述了单独加载资产与使用捆绑和缩小之间的差异：

操作 | 使用捆绑/缩小 | 不使用捆绑/缩小 | 更改
--- | :---: | :---: | :---:
文件请求  | 7   | 18     | 157%
传输的 KB | 156 | 264.68 | 70%
加载时间（毫秒） | 885 | 2360   | 167%

对于 HTTP 请求头，浏览器是非常详细的。 捆绑时，已发送的总字节数指标明显减少。 加载时间显示了显著改进，但本示例在本地运行。 将捆绑和缩小与通过网络传输的资产结合使用时，可实现更高的性能提升。

## <a name="choose-a-bundling-and-minification-strategy"></a>选择捆绑和缩小策略

ASP.NET Core 与 WebOptimizer（一种开源捆绑和缩小解决方案）兼容。 有关设置说明和示例项目，请参阅 [WebOptimizer](https://github.com/ligershark/WebOptimizer)。 ASP.NET Core 不提供本机捆绑和缩小解决方案。

第三方工具（如 [Gulp](https://gulpjs.com) 和 [Webpack](https://webpack.js.org)）提供了针对捆绑和缩小的自动化工作流，以及 Lint 分析和映像优化。 通过使用设计时捆绑和缩小，在应用部署之前创建缩小文件。 在部署之前进行捆绑和缩小具有减少服务器负载的优点。 但是，必须认识到，设计时捆绑和缩小会增加生成的复杂性，并且仅适用于静态文件。

## <a name="environment-based-bundling-and-minification"></a>基于环境的捆绑和缩小

最佳做法是，应在生产环境中使用应用的捆绑文件和缩小文件。 在开发过程中，原始文件可简化应用的调试。

使用视图中的[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)指定要包含在页面中的文件。 环境标记帮助程序仅在特定[环境](xref:fundamentals/environments)中运行时呈现其内容。

以下 `environment` 标记将在 `Development` 环境中运行时呈现未处理的 CSS 文件：

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment include="Development">
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

以下 `environment` 标记将在非 `Development` 环境中运行时呈现捆绑的和缩小的 CSS 文件。 例如，在 `Production` 或 `Staging` 中运行将触发这些样式表的呈现：

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment exclude="Development">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [使用多个环境](xref:fundamentals/environments)
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)
