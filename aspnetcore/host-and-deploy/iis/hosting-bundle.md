---
title: 托管捆绑包
author: rick-anderson
description: 了解如何配置 .NET Core 托管捆绑包。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 044b7ef98610e504eb4887dae88c8636c7c9a8fe
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957660"
---
# <a name="the-net-core-hosting-bundle"></a>.NET Core 托管捆绑包

.NET Core 托管捆绑包是 .NET Core 运行时和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装程序。 该捆绑包允许 ASP.NET Core 应用通过 IIS 运行。

## <a name="install-the-net-core-hosting-bundle"></a>安装 .NET Core 托管捆绑包

> [!IMPORTANT]
> 如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。 在安装 IIS 后再次运行托管捆绑包安装程序。
>
> 如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。 要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。

[!INCLUDE[](~/includes/announcements.md)]

## <a name="direct-download-current-version"></a>直接下载（当前版本）

使用以下链接下载安装程序：

[当前 .NET Core 托管捆绑包安装程序（直接下载）](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a>Visual C++ 可再发行程序包要求

在较旧版本的 Windows（例如 Windows Server 2012 R2）上，安装 Visual Studio C++ 2015、2017、2019 可再发行程序包。 否则，Windows 事件日志中令人困惑的错误消息会报告 `The data is the error.`

[当前 x64 VS C++ 可再发行程序包](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[当前 x86 VS C++ 可再发行程序包](https://aka.ms/vs/16/release/vc_redist.x86.exe)

## <a name="earlier-versions-of-the-installer"></a>先前版本的安装程序

若要获取先前版本的安装程序：

1. 导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。
1. 选择所需的 .NET Core 版本。
1. 在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。
1. 使用“托管捆绑包”链接下载安装程序。

> [!WARNING]
> 某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。 有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

## <a name="options"></a>选项

1. 通过管理员命令行界面运行安装程序时，以下参数可用：

   * `OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。
   * `OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。 当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。
   * `OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。 当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。
   * `OPT_NO_X86=1`：跳过安装 x86 运行时。 确定不会托管 32 位应用时，请使用此参数。 如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。
   * `OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (`applicationHost.config`) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。 仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。

> [!NOTE]
> 有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。

## <a name="restart-iis"></a>重新启动 IIS

安装托管捆绑包后，可能需要手动重新启动 IIS。 例如，在运行 IIS 工作进程的路径上可能不存在 `dotnet` CLI 工具（命令）。

若要手动停止和启动 IIS，请在提升的命令行界面中执行以下命令：

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a>模块版本和托管捆绑安装程序日志

若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：

1. 在托管系统上，导航到 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`。
1. 找到 `aspnetcorev2.dll` 文件。
1. 右键单击该文件，然后从上下文菜单中选择“属性”。
1. 选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。

可在 `C:\Users\%UserName%\AppData\Local\Temp` 中找到模块的托管捆绑安装程序日志。 该文件的名称为 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`，其中占位符 `{TIMESTAMP}` 为文件的时间戳。
