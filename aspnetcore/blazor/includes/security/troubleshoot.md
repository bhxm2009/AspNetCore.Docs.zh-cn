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
## <a name="troubleshoot"></a>疑难解答

### <a name="common-errors"></a>常见错误

* 应用或 Identity 提供者 (IP) 配置错误

  最常见的错误是因为配置不正确导致的。 下面是几个示例：
  
  * 根据具体情景的要求，缺少或不正确的颁发机构、实例、租户 ID、租户域、客户端 ID 或重定向 URI 会阻止应用对客户端进行身份验证。
  * 不正确的访问令牌范围会阻止客户端访问服务器 Web API 终结点。
  * 服务器 API 权限不正确或缺失会阻止客户端访问服务器 Web API 终结点。
  * 运行应用时所用的端口不是 Identity 提供者应用注册的重定向 URI 中配置的端口。
  
  本文指导的配置部分显示了正确的配置示例。 请仔细查看本文的每个部分，以查找应用和 IP 配置错误。
  
  如果配置看起来是正确的：
  
  * 分析应用程序日志。
  * 通过浏览器的开发人员工具，检查客户端应用和 IP 或服务器应用之间的网络流量。 通常，在发出请求后，IP 或服务器应用会向客户端返回一条确切的错误消息或包含线索的消息，其中指出了导致问题的原因。 有关开发人员工具指导，请参阅以下文章：

    * [Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network)（Google 文档）
    * [Microsoft Edge](/microsoft-edge/devtools-guide-chromium/network/)
    * [Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor)（Mozilla 文档）

  * 根据出现问题的位置，解码用于对客户端进行身份验证或访问服务器 Web API 的 JSON Web 令牌 (JWT) 的内容。 有关更多信息，请参阅[检查 JSON Web 令牌 (JWT) 得内容](#inspect-the-content-of-a-json-web-token-jwt)。
  
  文档团队会响应文章中的文档反馈和 bug（从 **该页面** 反馈部分新建问题），但无法提供产品支持。 可以借助多个公共支持论坛来帮助排查应用问题。 建议如下：
  
  * [Stack Overflow（标记：`blazor`）](https://stackoverflow.com/questions/tagged/blazor)
  * [ASP.NET Core Slack 团队](http://tattoocoder.com/aspnet-slack-sign-up/)
  * [Blazor Gitter](https://gitter.im/aspnet/Blazor)
  
  对于非安全、非敏感且非机密的可重现框架 bug 报告，请[向 ASP.NET Core 产品团队提交问题](https://github.com/dotnet/aspnetcore/issues)。 请务必先彻底调查问题原因，并确定无法自行解决问题，在公共支持论坛的社区帮助下同样无法解决问题后，再向该产品团队提交问题。 如果应用问题是由简单的配置错误引起或涉及第三方服务，该产品团队无法对此进行故障排除。 如果报告包含敏感或机密内容，或者描述了可能会被攻击者利用的潜在产品安全缺陷，请参阅[报告安全问题和 bug（dotnet/aspnetcore GitHub 存储库）](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs)。

::: moniker range=">= aspnetcore-5.0"

* AAD 的客户端未获得授权

  > 信息：Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] 授权失败。 不符合以下要求：DenyAnonymousAuthorizationRequirement：要求用户经过身份验证。

  AAD 返回的登录回叫错误：

  * 错误：`unauthorized_client`
  * 说明：`AADB2C90058: The provided application is not configured to allow public clients.`

  若要解决该错误：

  1. 在 Azure 门户中访问[应用的清单](/azure/active-directory/develop/reference-app-manifest)。
  1. 将 [`allowPublicClient` 属性](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)设置为 `null` 或 `true`。

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookie 和站点数据

Cookie 和站点数据在经过应用更新后仍可保持不变，并且会干扰测试和故障排除。 在更改应用代码、更改提供程序的用户帐户或更改提供程序的应用配置时，请清除以下内容：

* 用户登录 cookie
* 应用 cookie
* 缓存和存储的站点数据

防止存留的 cookie 和站点数据干扰测试和故障排除的一种方法是：

* 配置浏览器
  * 使用浏览器测试是否可以配置为在每次关闭浏览器时删除所有 cookie 和站点数据。
  * 对于应用、测试用户或提供程序配置的任何更改，请确保浏览器是手动关闭的或由 IDE 关闭的。
* 在 Visual Studio 中使用自定义命令以 incognito 或 private 模式打开浏览器：
  * 通过 Visual Studio 的“运行”按钮打开“浏览工具”对话框 。
  * 选择“添加”按钮。
  * 在“程序”字段中提供浏览器的路径。 以下可执行路径是适用于 Windows 10 的典型安装位置。 如果浏览器安装在其他位置，或者未使用 Windows 10，请提供浏览器可执行文件的路径。
    * Microsoft Edge：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome：`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox：`C:\Program Files\Mozilla Firefox\firefox.exe`
  * 在“参数”字段中，提供浏览器用来在 incognito 或 private 模式下执行打开操作的命令行选项。 某些浏览器需要应用的 URL。
    * Microsoft Edge：请使用 `-inprivate`。
    * Google Chrome：使用 `--incognito --new-window {URL}`，其中占位符 `{URL}` 是要打开的 URL（例如，`https://localhost:5001`）。
    * Mozilla Firefox：使用 `-private -url {URL}`，其中占位符 `{URL}` 是要打开的 URL（例如，`https://localhost:5001`）。
  * 在“友好名称”字段中提供名称。 例如 `Firefox Auth Testing`。
  * 选择“确定”按钮。
  * 若要避免在每次迭代使用应用进行测试时必须选择浏览器配置文件，请使用“设置为默认值”按钮将配置文件设置为默认值。
  * 对于应用、测试用户或提供程序配置的任何更改，请确保浏览器是由 IDE 关闭的。

### <a name="app-upgrades"></a>应用升级

正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。 在某些情况下，不同的包可能在执行主要升级时中断应用。 可以按照以下说明来修复其中大部分问题：

1. 从命令 shell 执行 [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) 以清空本地系统的 NuGet 包缓存。
1. 删除项目的 `bin` 和 `obj` 文件夹。
1. 还原并重新生成项目。
1. 在重新部署应用前，在服务器上删除部署文件夹中的所有文件。

> [!NOTE]
> 不支持使用与应用的目标框架不兼容的包版本。 有关包的信息，请使用 [NuGet Gallery](https://www.nuget.org) 或 [FuGet Package Explorer](https://www.fuget.org) 进行了解。

### <a name="run-the-server-app"></a>运行 Server 应用

在对托管的 Blazor 解决方案进行测试和故障排除时，请确保从 **`Server`** 项目运行应用。 例如，在 Visual Studio 中，确认在用以下任一方法启动应用之前，解决方案资源管理器中突出显示了 Server 项目：

* 选择“运行”按钮。
* 从菜单栏中，依次使用“调试” > “开始调试” 。
* 按 F5 <kbd></kbd>。

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>检查 JSON Web 令牌 (JWT) 的内容

若要对 JSON Web 令牌 (JWT) 进行解码，请使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。 UI 中的值永远不会离开浏览器。

编码 JWT 示例（为便于显示，已经缩短）：

> eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q

工具针对向 Azure AAD B2C 进行身份验证的应用解码的 JWT 示例：

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
