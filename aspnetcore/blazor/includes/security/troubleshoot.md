---
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
ms.openlocfilehash: f58da78475d65cbb70b0b177e1b0443535e97d55
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102402302"
---
## <a name="troubleshoot"></a><span data-ttu-id="f4add-101">疑难解答</span><span class="sxs-lookup"><span data-stu-id="f4add-101">Troubleshoot</span></span>

### <a name="common-errors"></a><span data-ttu-id="f4add-102">常见错误</span><span class="sxs-lookup"><span data-stu-id="f4add-102">Common errors</span></span>

* <span data-ttu-id="f4add-103">应用或 Identity 提供者 (IP) 配置错误</span><span class="sxs-lookup"><span data-stu-id="f4add-103">Misconfiguration of the app or Identity Provider (IP)</span></span>

  <span data-ttu-id="f4add-104">最常见的错误是因为配置不正确导致的。</span><span class="sxs-lookup"><span data-stu-id="f4add-104">The most common errors are caused by incorrect configuration.</span></span> <span data-ttu-id="f4add-105">下面是几个示例：</span><span class="sxs-lookup"><span data-stu-id="f4add-105">The following are a few examples:</span></span>
  
  * <span data-ttu-id="f4add-106">根据具体情景的要求，缺少或不正确的颁发机构、实例、租户 ID、租户域、客户端 ID 或重定向 URI 会阻止应用对客户端进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4add-106">Depending on the requirements of the scenario, a missing or incorrect Authority, Instance, Tenant ID, Tenant domain, Client ID, or Redirect URI prevents an app from authenticating clients.</span></span>
  * <span data-ttu-id="f4add-107">不正确的访问令牌范围会阻止客户端访问服务器 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="f4add-107">An incorrect access token scope prevents clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="f4add-108">服务器 API 权限不正确或缺失会阻止客户端访问服务器 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="f4add-108">Incorrect or missing server API permissions prevent clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="f4add-109">运行应用时所用的端口不是 Identity 提供者应用注册的重定向 URI 中配置的端口。</span><span class="sxs-lookup"><span data-stu-id="f4add-109">Running the app at a different port than is configured in the Redirect URI of the Identity Provider's app registration.</span></span>
  
  <span data-ttu-id="f4add-110">本文指导的配置部分显示了正确的配置示例。</span><span class="sxs-lookup"><span data-stu-id="f4add-110">Configuration sections of this article's guidance show examples of the correct configuration.</span></span> <span data-ttu-id="f4add-111">请仔细查看本文的每个部分，以查找应用和 IP 配置错误。</span><span class="sxs-lookup"><span data-stu-id="f4add-111">Carefully check each section of the article looking for app and IP misconfiguration.</span></span>
  
  <span data-ttu-id="f4add-112">如果配置看起来是正确的：</span><span class="sxs-lookup"><span data-stu-id="f4add-112">If the configuration appears correct:</span></span>
  
  * <span data-ttu-id="f4add-113">分析应用程序日志。</span><span class="sxs-lookup"><span data-stu-id="f4add-113">Analyze application logs.</span></span>
  * <span data-ttu-id="f4add-114">通过浏览器的开发人员工具，检查客户端应用和 IP 或服务器应用之间的网络流量。</span><span class="sxs-lookup"><span data-stu-id="f4add-114">Examine the network traffic between the client app and the IP or server app with the browser's developer tools.</span></span> <span data-ttu-id="f4add-115">通常，在发出请求后，IP 或服务器应用会向客户端返回一条确切的错误消息或包含线索的消息，其中指出了导致问题的原因。</span><span class="sxs-lookup"><span data-stu-id="f4add-115">Often, an exact error message or a message with a clue to what's causing the problem is returned to the client by the IP or server app after making a request.</span></span> <span data-ttu-id="f4add-116">有关开发人员工具指导，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="f4add-116">Developer tools guidance is found in the following articles:</span></span>

    * <span data-ttu-id="f4add-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network)（Google 文档）</span><span class="sxs-lookup"><span data-stu-id="f4add-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (Google documentation)</span></span>
    * [<span data-ttu-id="f4add-118">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="f4add-118">Microsoft Edge</span></span>](/microsoft-edge/devtools-guide-chromium/network/)
    * <span data-ttu-id="f4add-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor)（Mozilla 文档）</span><span class="sxs-lookup"><span data-stu-id="f4add-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (Mozilla documentation)</span></span>

  * <span data-ttu-id="f4add-120">根据出现问题的位置，解码用于对客户端进行身份验证或访问服务器 Web API 的 JSON Web 令牌 (JWT) 的内容。</span><span class="sxs-lookup"><span data-stu-id="f4add-120">Decode the contents of a JSON Web Token (JWT) used for authenticating a client or accessing a server web API, depending on where the problem is occurring.</span></span> <span data-ttu-id="f4add-121">有关更多信息，请参阅[检查 JSON Web 令牌 (JWT) 得内容](#inspect-the-content-of-a-json-web-token-jwt)。</span><span class="sxs-lookup"><span data-stu-id="f4add-121">For more information, see [Inspect the content of a JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span></span>
  
  <span data-ttu-id="f4add-122">文档团队会响应文章中的文档反馈和 bug（从 **该页面** 反馈部分新建问题），但无法提供产品支持。</span><span class="sxs-lookup"><span data-stu-id="f4add-122">The documenation team responds to document feedback and bugs in articles (open an issue from the **This page** feedback section) but is unable to provide product support.</span></span> <span data-ttu-id="f4add-123">可以借助多个公共支持论坛来帮助排查应用问题。</span><span class="sxs-lookup"><span data-stu-id="f4add-123">Several public support forums are available to assist with troubleshooting an app.</span></span> <span data-ttu-id="f4add-124">建议如下：</span><span class="sxs-lookup"><span data-stu-id="f4add-124">We recommend the following:</span></span>
  
  * [<span data-ttu-id="f4add-125">Stack Overflow（标记：`blazor`）</span><span class="sxs-lookup"><span data-stu-id="f4add-125">Stack Overflow (tag: `blazor`)</span></span>](https://stackoverflow.com/questions/tagged/blazor)
  * [<span data-ttu-id="f4add-126">ASP.NET Core Slack 团队</span><span class="sxs-lookup"><span data-stu-id="f4add-126">ASP.NET Core Slack Team</span></span>](http://tattoocoder.com/aspnet-slack-sign-up/)
  * <span data-ttu-id="f4add-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span><span class="sxs-lookup"><span data-stu-id="f4add-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span></span>
  
  <span data-ttu-id="f4add-128">对于非安全、非敏感且非机密的可重现框架 bug 报告，请[向 ASP.NET Core 产品团队提交问题](https://github.com/dotnet/aspnetcore/issues)。</span><span class="sxs-lookup"><span data-stu-id="f4add-128">For non-security, non-sensitive, and non-confidential reproducible framework bug reports, [open an issue with the ASP.NET Core product unit](https://github.com/dotnet/aspnetcore/issues).</span></span> <span data-ttu-id="f4add-129">请务必先彻底调查问题原因，并确定无法自行解决问题，在公共支持论坛的社区帮助下同样无法解决问题后，再向该产品团队提交问题。</span><span class="sxs-lookup"><span data-stu-id="f4add-129">Don't open an issue with the product unit until you've thoroughly investigated the cause of a problem and can't resolve it on your own and with the help of the community on a public support forum.</span></span> <span data-ttu-id="f4add-130">如果应用问题是由简单的配置错误引起或涉及第三方服务，该产品团队无法对此进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="f4add-130">The product unit isn't able to troubleshoot individual apps that are broken due to simple misconfiguration or use cases involving third-party services.</span></span> <span data-ttu-id="f4add-131">如果报告包含敏感或机密内容，或者描述了可能会被攻击者利用的潜在产品安全缺陷，请参阅[报告安全问题和 bug（dotnet/aspnetcore GitHub 存储库）](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs)。</span><span class="sxs-lookup"><span data-stu-id="f4add-131">If a report is sensitive or confidential in nature or describes a potential security flaw in the product that attackers may exploit, see [Reporting security issues and bugs (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="f4add-132">AAD 的客户端未获得授权</span><span class="sxs-lookup"><span data-stu-id="f4add-132">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="f4add-133">信息：Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] 授权失败。</span><span class="sxs-lookup"><span data-stu-id="f4add-133">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="f4add-134">不符合以下要求：DenyAnonymousAuthorizationRequirement：要求用户经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4add-134">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="f4add-135">AAD 返回的登录回叫错误：</span><span class="sxs-lookup"><span data-stu-id="f4add-135">Login callback error from AAD:</span></span>

  * <span data-ttu-id="f4add-136">错误：`unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="f4add-136">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="f4add-137">说明：`AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="f4add-137">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="f4add-138">若要解决该错误：</span><span class="sxs-lookup"><span data-stu-id="f4add-138">To resolve the error:</span></span>

  1. <span data-ttu-id="f4add-139">在 Azure 门户中访问[应用的清单](/azure/active-directory/develop/reference-app-manifest)。</span><span class="sxs-lookup"><span data-stu-id="f4add-139">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="f4add-140">将 [`allowPublicClient` 属性](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)设置为 `null` 或 `true`。</span><span class="sxs-lookup"><span data-stu-id="f4add-140">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="f4add-141">Cookie 和站点数据</span><span class="sxs-lookup"><span data-stu-id="f4add-141">Cookies and site data</span></span>

<span data-ttu-id="f4add-142">Cookie 和站点数据在经过应用更新后仍可保持不变，并且会干扰测试和故障排除。</span><span class="sxs-lookup"><span data-stu-id="f4add-142">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="f4add-143">在更改应用代码、更改提供程序的用户帐户或更改提供程序的应用配置时，请清除以下内容：</span><span class="sxs-lookup"><span data-stu-id="f4add-143">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="f4add-144">用户登录 cookie</span><span class="sxs-lookup"><span data-stu-id="f4add-144">User sign-in cookies</span></span>
* <span data-ttu-id="f4add-145">应用 cookie</span><span class="sxs-lookup"><span data-stu-id="f4add-145">App cookies</span></span>
* <span data-ttu-id="f4add-146">缓存和存储的站点数据</span><span class="sxs-lookup"><span data-stu-id="f4add-146">Cached and stored site data</span></span>

<span data-ttu-id="f4add-147">防止存留的 cookie 和站点数据干扰测试和故障排除的一种方法是：</span><span class="sxs-lookup"><span data-stu-id="f4add-147">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="f4add-148">配置浏览器</span><span class="sxs-lookup"><span data-stu-id="f4add-148">Configure a browser</span></span>
  * <span data-ttu-id="f4add-149">使用浏览器测试是否可以配置为在每次关闭浏览器时删除所有 cookie 和站点数据。</span><span class="sxs-lookup"><span data-stu-id="f4add-149">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="f4add-150">对于应用、测试用户或提供程序配置的任何更改，请确保浏览器是手动关闭的或由 IDE 关闭的。</span><span class="sxs-lookup"><span data-stu-id="f4add-150">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="f4add-151">在 Visual Studio 中使用自定义命令以 incognito 或 private 模式打开浏览器：</span><span class="sxs-lookup"><span data-stu-id="f4add-151">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="f4add-152">通过 Visual Studio 的“运行”按钮打开“浏览工具”对话框 。</span><span class="sxs-lookup"><span data-stu-id="f4add-152">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="f4add-153">选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="f4add-153">Select the **Add** button.</span></span>
  * <span data-ttu-id="f4add-154">在“程序”字段中提供浏览器的路径。</span><span class="sxs-lookup"><span data-stu-id="f4add-154">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="f4add-155">以下可执行路径是适用于 Windows 10 的典型安装位置。</span><span class="sxs-lookup"><span data-stu-id="f4add-155">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="f4add-156">如果浏览器安装在其他位置，或者未使用 Windows 10，请提供浏览器可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f4add-156">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="f4add-157">Microsoft Edge：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="f4add-157">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="f4add-158">Google Chrome：`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="f4add-158">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="f4add-159">Mozilla Firefox：`C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="f4add-159">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="f4add-160">在“参数”字段中，提供浏览器用来在 incognito 或 private 模式下执行打开操作的命令行选项。</span><span class="sxs-lookup"><span data-stu-id="f4add-160">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="f4add-161">某些浏览器需要应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="f4add-161">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="f4add-162">Microsoft Edge：请使用 `-inprivate`。</span><span class="sxs-lookup"><span data-stu-id="f4add-162">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="f4add-163">Google Chrome：使用 `--incognito --new-window {URL}`，其中占位符 `{URL}` 是要打开的 URL（例如，`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="f4add-163">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="f4add-164">Mozilla Firefox：使用 `-private -url {URL}`，其中占位符 `{URL}` 是要打开的 URL（例如，`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="f4add-164">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="f4add-165">在“友好名称”字段中提供名称。</span><span class="sxs-lookup"><span data-stu-id="f4add-165">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="f4add-166">例如 `Firefox Auth Testing`。</span><span class="sxs-lookup"><span data-stu-id="f4add-166">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="f4add-167">选择“确定”按钮。</span><span class="sxs-lookup"><span data-stu-id="f4add-167">Select the **OK** button.</span></span>
  * <span data-ttu-id="f4add-168">若要避免在每次迭代使用应用进行测试时必须选择浏览器配置文件，请使用“设置为默认值”按钮将配置文件设置为默认值。</span><span class="sxs-lookup"><span data-stu-id="f4add-168">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="f4add-169">对于应用、测试用户或提供程序配置的任何更改，请确保浏览器是由 IDE 关闭的。</span><span class="sxs-lookup"><span data-stu-id="f4add-169">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="app-upgrades"></a><span data-ttu-id="f4add-170">应用升级</span><span class="sxs-lookup"><span data-stu-id="f4add-170">App upgrades</span></span>

<span data-ttu-id="f4add-171">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="f4add-171">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="f4add-172">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="f4add-172">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f4add-173">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="f4add-173">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f4add-174">从命令 shell 执行 [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) 以清空本地系统的 NuGet 包缓存。</span><span class="sxs-lookup"><span data-stu-id="f4add-174">Clear the local system's NuGet package caches by executing [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>
1. <span data-ttu-id="f4add-175">删除项目的 `bin` 和 `obj` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="f4add-175">Delete the project's `bin` and `obj` folders.</span></span>
1. <span data-ttu-id="f4add-176">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="f4add-176">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f4add-177">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="f4add-177">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

> [!NOTE]
> <span data-ttu-id="f4add-178">不支持使用与应用的目标框架不兼容的包版本。</span><span class="sxs-lookup"><span data-stu-id="f4add-178">Use of package versions incompatible with the app's target framework isn't supported.</span></span> <span data-ttu-id="f4add-179">有关包的信息，请使用 [NuGet Gallery](https://www.nuget.org) 或 [FuGet Package Explorer](https://www.fuget.org) 进行了解。</span><span class="sxs-lookup"><span data-stu-id="f4add-179">For information on a package, use the [NuGet Gallery](https://www.nuget.org) or [FuGet Package Explorer](https://www.fuget.org).</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="f4add-180">运行 Server 应用</span><span class="sxs-lookup"><span data-stu-id="f4add-180">Run the Server app</span></span>

<span data-ttu-id="f4add-181">在对托管的 Blazor 解决方案进行测试和故障排除时，请确保从 **`Server`** 项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="f4add-181">When testing and troubleshooting a hosted Blazor solution, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="f4add-182">例如，在 Visual Studio 中，确认在用以下任一方法启动应用之前，解决方案资源管理器中突出显示了 Server 项目：</span><span class="sxs-lookup"><span data-stu-id="f4add-182">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="f4add-183">选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="f4add-183">Select the **Run** button.</span></span>
* <span data-ttu-id="f4add-184">从菜单栏中，依次使用“调试” > “开始调试” 。</span><span class="sxs-lookup"><span data-stu-id="f4add-184">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="f4add-185">按 F5 <kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="f4add-185">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="f4add-186">检查 JSON Web 令牌 (JWT) 的内容</span><span class="sxs-lookup"><span data-stu-id="f4add-186">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="f4add-187">若要对 JSON Web 令牌 (JWT) 进行解码，请使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。</span><span class="sxs-lookup"><span data-stu-id="f4add-187">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="f4add-188">UI 中的值永远不会离开浏览器。</span><span class="sxs-lookup"><span data-stu-id="f4add-188">Values in the UI never leave your browser.</span></span>

<span data-ttu-id="f4add-189">编码 JWT 示例（为便于显示，已经缩短）：</span><span class="sxs-lookup"><span data-stu-id="f4add-189">Example encoded JWT (shortened for display):</span></span>

> <span data-ttu-id="f4add-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span><span class="sxs-lookup"><span data-stu-id="f4add-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span></span>

<span data-ttu-id="f4add-191">工具针对向 Azure AAD B2C 进行身份验证的应用解码的 JWT 示例：</span><span class="sxs-lookup"><span data-stu-id="f4add-191">Example JWT decoded by the tool for an app that authenticates against Azure AAD B2C:</span></span>

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
