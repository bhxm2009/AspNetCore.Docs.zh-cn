---
title: ASP.NET Core 中的捆绑和缩小静态资产
author: rick-anderson
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
ms.openlocfilehash: 12701d02ed38e68c1833ee51d9ea87337d17065a
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106563584"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="cef97-103">ASP.NET Core 中的捆绑和缩小静态资产</span><span class="sxs-lookup"><span data-stu-id="cef97-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="cef97-104">作者：[Scott Addie](https://twitter.com/Scott_Addie) 和 [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="cef97-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="cef97-105">本文介绍应用捆绑和缩小的好处，包括如何在 ASP.NET Core Web 应用中使用这些功能。</span><span class="sxs-lookup"><span data-stu-id="cef97-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="cef97-106">什么是捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="cef97-106">What is bundling and minification</span></span>

<span data-ttu-id="cef97-107">捆绑和缩小是可以在 Web 应用中应用的两个不同的性能优化。</span><span class="sxs-lookup"><span data-stu-id="cef97-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="cef97-108">捆绑和缩小一起使用，可减少服务器的请求数并减小请求的静态资产的大小，从而提高性能。</span><span class="sxs-lookup"><span data-stu-id="cef97-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="cef97-109">捆绑和缩小主要缩短第一个页面请求加载时间。</span><span class="sxs-lookup"><span data-stu-id="cef97-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="cef97-110">请求网页后，浏览器会缓存静态资产（JavaScript、CSS 和图像）。</span><span class="sxs-lookup"><span data-stu-id="cef97-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="cef97-111">因此，在请求相同资产的同一站点上请求相同的一个或多个页面时，捆绑和缩小不会提高性能。</span><span class="sxs-lookup"><span data-stu-id="cef97-111">So, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="cef97-112">如果未在资产上正确设置 expires 标头，且未使用捆绑和缩小，则浏览器的新鲜度启发会在几天后将资产标记为过期。</span><span class="sxs-lookup"><span data-stu-id="cef97-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="cef97-113">此外，浏览器还需要对每个资产进行验证请求。</span><span class="sxs-lookup"><span data-stu-id="cef97-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="cef97-114">在这种情况下，即使在第一个页面请求后，捆绑和缩小仍能提高性能。</span><span class="sxs-lookup"><span data-stu-id="cef97-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="cef97-115">捆绑</span><span class="sxs-lookup"><span data-stu-id="cef97-115">Bundling</span></span>

<span data-ttu-id="cef97-116">捆绑将多个文件合并到单个文件中。</span><span class="sxs-lookup"><span data-stu-id="cef97-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="cef97-117">捆绑可减少呈现 Web 资产（如网页）所需的服务器请求数。</span><span class="sxs-lookup"><span data-stu-id="cef97-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="cef97-118">可以专门为 CSS、JavaScript 等创建任意数量的单个捆绑。文件越少，从浏览器发送到服务器或从提供应用程序的服务发送的 HTTP 请求就越少。</span><span class="sxs-lookup"><span data-stu-id="cef97-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files mean fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="cef97-119">这会提高第一页加载性能。</span><span class="sxs-lookup"><span data-stu-id="cef97-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="cef97-120">缩小</span><span class="sxs-lookup"><span data-stu-id="cef97-120">Minification</span></span>

<span data-ttu-id="cef97-121">缩小在不更改功能的情况下从代码中删除不必要的字符。</span><span class="sxs-lookup"><span data-stu-id="cef97-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="cef97-122">因此，请求的资产（如 CSS、图像和 JavaScript 文件）的大小大幅减小。</span><span class="sxs-lookup"><span data-stu-id="cef97-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="cef97-123">缩小的常见副作用包括将变量名称缩短为一个字符、删除注释和不必要的空格。</span><span class="sxs-lookup"><span data-stu-id="cef97-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="cef97-124">考虑以下 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="cef97-124">Consider the following JavaScript function:</span></span>

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

<span data-ttu-id="cef97-125">缩小将函数缩减为以下内容：</span><span class="sxs-lookup"><span data-stu-id="cef97-125">Minification reduces the function to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="cef97-126">除了删除注释和不必要的空格外，还进行了以下参数和变量名称重命名：</span><span class="sxs-lookup"><span data-stu-id="cef97-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="cef97-127">原始</span><span class="sxs-lookup"><span data-stu-id="cef97-127">Original</span></span> | <span data-ttu-id="cef97-128">重命名</span><span class="sxs-lookup"><span data-stu-id="cef97-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="cef97-129">捆绑和缩小的影响</span><span class="sxs-lookup"><span data-stu-id="cef97-129">Impact of bundling and minification</span></span>

<span data-ttu-id="cef97-130">下表概述了单独加载资产与使用捆绑和缩小之间的差异：</span><span class="sxs-lookup"><span data-stu-id="cef97-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="cef97-131">操作</span><span class="sxs-lookup"><span data-stu-id="cef97-131">Action</span></span> | <span data-ttu-id="cef97-132">使用捆绑/缩小</span><span class="sxs-lookup"><span data-stu-id="cef97-132">With B/M</span></span> | <span data-ttu-id="cef97-133">不使用捆绑/缩小</span><span class="sxs-lookup"><span data-stu-id="cef97-133">Without B/M</span></span> | <span data-ttu-id="cef97-134">更改</span><span class="sxs-lookup"><span data-stu-id="cef97-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="cef97-135">文件请求</span><span class="sxs-lookup"><span data-stu-id="cef97-135">File Requests</span></span>  | <span data-ttu-id="cef97-136">7</span><span class="sxs-lookup"><span data-stu-id="cef97-136">7</span></span>   | <span data-ttu-id="cef97-137">18</span><span class="sxs-lookup"><span data-stu-id="cef97-137">18</span></span>     | <span data-ttu-id="cef97-138">157%</span><span class="sxs-lookup"><span data-stu-id="cef97-138">157%</span></span>
<span data-ttu-id="cef97-139">传输的 KB</span><span class="sxs-lookup"><span data-stu-id="cef97-139">KB Transferred</span></span> | <span data-ttu-id="cef97-140">156</span><span class="sxs-lookup"><span data-stu-id="cef97-140">156</span></span> | <span data-ttu-id="cef97-141">264.68</span><span class="sxs-lookup"><span data-stu-id="cef97-141">264.68</span></span> | <span data-ttu-id="cef97-142">70%</span><span class="sxs-lookup"><span data-stu-id="cef97-142">70%</span></span>
<span data-ttu-id="cef97-143">加载时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="cef97-143">Load Time (ms)</span></span> | <span data-ttu-id="cef97-144">885</span><span class="sxs-lookup"><span data-stu-id="cef97-144">885</span></span> | <span data-ttu-id="cef97-145">2360</span><span class="sxs-lookup"><span data-stu-id="cef97-145">2360</span></span>   | <span data-ttu-id="cef97-146">167%</span><span class="sxs-lookup"><span data-stu-id="cef97-146">167%</span></span>

<span data-ttu-id="cef97-147">对于 HTTP 请求头，浏览器是非常详细的。</span><span class="sxs-lookup"><span data-stu-id="cef97-147">Browsers are fairly verbose regarding HTTP request headers.</span></span> <span data-ttu-id="cef97-148">捆绑时，已发送的总字节数指标明显减少。</span><span class="sxs-lookup"><span data-stu-id="cef97-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="cef97-149">加载时间显示了显著改进，但本示例在本地运行。</span><span class="sxs-lookup"><span data-stu-id="cef97-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="cef97-150">将捆绑和缩小与通过网络传输的资产结合使用时，可实现更高的性能提升。</span><span class="sxs-lookup"><span data-stu-id="cef97-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="cef97-151">选择捆绑和缩小策略</span><span class="sxs-lookup"><span data-stu-id="cef97-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="cef97-152">ASP.NET Core 与 WebOptimizer（一种开源捆绑和缩小解决方案）兼容。</span><span class="sxs-lookup"><span data-stu-id="cef97-152">ASP.NET Core is compatible with WebOptimizer, an open-source bundling and minification solution.</span></span> <span data-ttu-id="cef97-153">有关设置说明和示例项目，请参阅 [WebOptimizer](https://github.com/ligershark/WebOptimizer)。</span><span class="sxs-lookup"><span data-stu-id="cef97-153">For set up instructions and sample projects, see [WebOptimizer](https://github.com/ligershark/WebOptimizer).</span></span> <span data-ttu-id="cef97-154">ASP.NET Core 不提供本机捆绑和缩小解决方案。</span><span class="sxs-lookup"><span data-stu-id="cef97-154">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span>

<span data-ttu-id="cef97-155">第三方工具（如 [Gulp](https://gulpjs.com) 和 [Webpack](https://webpack.js.org)）提供了针对捆绑和缩小的自动化工作流，以及 Lint 分析和映像优化。</span><span class="sxs-lookup"><span data-stu-id="cef97-155">Third-party tools, such as [Gulp](https://gulpjs.com) and [Webpack](https://webpack.js.org), provide workflow automation for bundling and minification, as well as linting and image optimization.</span></span> <span data-ttu-id="cef97-156">通过使用设计时捆绑和缩小，在应用部署之前创建缩小文件。</span><span class="sxs-lookup"><span data-stu-id="cef97-156">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="cef97-157">在部署之前进行捆绑和缩小具有减少服务器负载的优点。</span><span class="sxs-lookup"><span data-stu-id="cef97-157">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="cef97-158">但是，必须认识到，设计时捆绑和缩小会增加生成的复杂性，并且仅适用于静态文件。</span><span class="sxs-lookup"><span data-stu-id="cef97-158">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="cef97-159">基于环境的捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="cef97-159">Environment-based bundling and minification</span></span>

<span data-ttu-id="cef97-160">最佳做法是，应在生产环境中使用应用的捆绑文件和缩小文件。</span><span class="sxs-lookup"><span data-stu-id="cef97-160">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="cef97-161">在开发过程中，原始文件可简化应用的调试。</span><span class="sxs-lookup"><span data-stu-id="cef97-161">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="cef97-162">使用视图中的[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)指定要包含在页面中的文件。</span><span class="sxs-lookup"><span data-stu-id="cef97-162">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="cef97-163">环境标记帮助程序仅在特定[环境](xref:fundamentals/environments)中运行时呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="cef97-163">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cef97-164">以下 `environment` 标记将在 `Development` 环境中运行时呈现未处理的 CSS 文件：</span><span class="sxs-lookup"><span data-stu-id="cef97-164">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

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

<span data-ttu-id="cef97-165">以下 `environment` 标记将在非 `Development` 环境中运行时呈现捆绑的和缩小的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="cef97-165">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="cef97-166">例如，在 `Production` 或 `Staging` 中运行将触发这些样式表的呈现：</span><span class="sxs-lookup"><span data-stu-id="cef97-166">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="cef97-167">其他资源</span><span class="sxs-lookup"><span data-stu-id="cef97-167">Additional resources</span></span>

* [<span data-ttu-id="cef97-168">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="cef97-168">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="cef97-169">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="cef97-169">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
