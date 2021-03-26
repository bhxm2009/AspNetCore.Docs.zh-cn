---
title: ASP.NET Core 中的 Azure Key Vault 配置提供程序
author: rick-anderson
description: 了解如何使用 Azure Key Vault 配置提供程序通过在运行时加载的名称/值对来配置应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli, contperf-fy21q3
ms.date: 03/17/2021
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
uid: security/key-vault-configuration
ms.openlocfilehash: 940458147f1a03eb2964518dc0e1b042775d693e
ms.sourcegitcommit: 9d185d06b343ec2fb91e7c01a4b14dd924f041c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2021
ms.locfileid: "105614908"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="cf0e3-103">ASP.NET Core 中的 Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="cf0e3-103">Azure Key Vault configuration provider in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf0e3-104">本文档说明如何使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-104">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) configuration provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="cf0e3-105">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-105">Azure Key Vault is a cloud-based service that helps safeguard cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="cf0e3-106">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-106">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="cf0e3-107">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-107">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="cf0e3-108">满足 FIPS 140-2 第2级验证的硬件安全模块在存储配置数据时 (Hsm) 的要求。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-108">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSMs) when storing configuration data.</span></span>

<span data-ttu-id="cf0e3-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cf0e3-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="cf0e3-110">包</span><span class="sxs-lookup"><span data-stu-id="cf0e3-110">Packages</span></span>

<span data-ttu-id="cf0e3-111">添加以下包的包引用：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-111">Add package references for the following packages:</span></span>

* [<span data-ttu-id="cf0e3-112">Azure.Extensions.AspNetCore.Configuration.Secrets</span><span class="sxs-lookup"><span data-stu-id="cf0e3-112">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="cf0e3-113">[Microsoft.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="cf0e3-113">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="cf0e3-114">示例应用</span><span class="sxs-lookup"><span data-stu-id="cf0e3-114">Sample app</span></span>

<span data-ttu-id="cf0e3-115">该示例应用在 `#define` *程序 .cs* 顶部由预处理器指令确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-115">The sample app runs in either of two modes determined by the `#define` preprocessor directive at the top of *Program.cs*:</span></span>

* <span data-ttu-id="cf0e3-116">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-116">`Certificate`: Demonstrates using an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="cf0e3-117">可以从任何位置运行此示例，无论部署到 Azure App Service 还是任何可为 ASP.NET Core 应用提供服务的主机。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-117">This sample can be run from any location, whether deployed to Azure App Service or any host that can serve an ASP.NET Core app.</span></span>
* <span data-ttu-id="cf0e3-118">`Managed`：演示如何使用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-118">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview).</span></span> <span data-ttu-id="cf0e3-119">托管标识对应用进行身份验证，以便与 Azure Active Directory (AD) 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-119">The managed identity authenticates the app to Azure Key Vault with Azure Active Directory (AD) authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="cf0e3-120">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码 (客户端密钥) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="cf0e3-121">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="cf0e3-122">按照 [使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-122">Follow the guidance in the [Use the managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="cf0e3-123">有关使用预处理器指令配置示例应用 () 的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-123">For more information configuring a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="cf0e3-124">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="cf0e3-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="cf0e3-125">使用 [机密管理器](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-125">Set secrets locally using [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cf0e3-126">在开发环境中的本地计算机上运行示例应用时，会从本地用户机密存储区中加载机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="cf0e3-127">机密管理器需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-127">Secret Manager requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="cf0e3-128">将属性值 (`{GUID}`) 设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="cf0e3-129">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="cf0e3-130">配置节 (层次结构值) `:` 在 [ASP.NET Core 配置](xref:fundamentals/configuration/index) 项名称中将 (冒号) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="cf0e3-131">机密管理器用在打开的命令行界面 [中，其中](xref:fundamentals/index#content-root) `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-131">Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="cf0e3-132">从项目的 [内容根](xref:fundamentals/index#content-root) 在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="cf0e3-133">如果这些机密存储在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="cf0e3-134">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="cf0e3-135">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="cf0e3-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="cf0e3-136">完成以下步骤以创建 Azure Key Vault，并在其中存储示例应用的机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-136">Complete the following steps to create an Azure Key Vault and store the sample app's secrets in it.</span></span> <span data-ttu-id="cf0e3-137">有关详细信息，请参阅[快速入门：使用 Azure CLI 在 Azure Key Vault 中设置和检索机密](/azure/key-vault/quick-create-cli)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-137">For more information, see [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli).</span></span>

1. <span data-ttu-id="cf0e3-138">使用 [Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud Shell：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-138">Open Azure Cloud Shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="cf0e3-139">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="cf0e3-140">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="cf0e3-141">在浏览器中打开 Cloud Shell，并提供 " **启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="cf0e3-142">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="cf0e3-143">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/) 和 [概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="cf0e3-144">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="cf0e3-145">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的名称， `{LOCATION}` 是 Azure 区域：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the new resource group's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cf0e3-146">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the new key vault's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cf0e3-147">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="cf0e3-148">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="cf0e3-149">配置节 (的分层值) 使用 `--` (两个短划线) 作为分隔符，因为密钥保管库机密名称中不允许使用冒号。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-149">Hierarchical values (configuration sections) use `--` (two dashes) as a delimiter, as colons aren't allowed in key vault secret names.</span></span> <span data-ttu-id="cf0e3-150">冒号在 [ASP.NET Core 配置](xref:fundamentals/configuration/index)中从子项分隔节。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-150">Colons delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="cf0e3-151">当机密加载到应用的配置中时，用冒号替换两个短划线序列。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-151">The two-dash sequence is replaced with a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="cf0e3-152">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="cf0e3-153">这些值包括一个 `_prod` 后缀，用于将它们与 `_dev` 从机密管理器中的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from Secret Manager.</span></span> <span data-ttu-id="cf0e3-154">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-154">Replace `{KEY VAULT NAME}` with the name of the key vault you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="cf0e3-155">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="cf0e3-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="cf0e3-156">配置 Azure AD、Azure Key Vault 和应用，以便在 **Azure 外托管应用时** 使用 Azure AD 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure AD Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="cf0e3-157">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="cf0e3-158">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但不建议这样做。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, it's not recommended.</span></span> <span data-ttu-id="cf0e3-159">在 Azure 中托管应用时，请改用 [azure 资源的托管标识](#use-managed-identities-for-azure-resources) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-159">Instead, use [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="cf0e3-160">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-160">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="cf0e3-161">当 `#define` *Program* top 的预处理器指令设置为时，示例应用使用应用程序 ID 和 x.509 `Certificate` 证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-161">The sample app uses an Application ID and X.509 certificate when the `#define` preprocessor directive at the top of *Program.cs* is set to `Certificate`.</span></span>

1. <span data-ttu-id="cf0e3-162">创建 PKCS # 12 存档 (*.pfx*) 证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-162">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="cf0e3-163">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-163">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="cf0e3-164">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-164">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="cf0e3-165">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-165">Marking the key as exportable is optional.</span></span> <span data-ttu-id="cf0e3-166">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-166">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="cf0e3-167">将 PKCS # 12 存档 (*.pfx*) 证书导 () *.cer* 编码的证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-167">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="cf0e3-168">将应用注册 Azure AD (**应用注册**) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-168">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="cf0e3-169">将 DER 编码的证书 (*.cer*) 上传到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-169">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="cf0e3-170">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-170">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="cf0e3-171">导航到 " **证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-171">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="cf0e3-172">选择 " **上传证书** "，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-172">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="cf0e3-173">*.Cer*、 *pem* 或 *.crt* 证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-173">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="cf0e3-174">在应用的文件中存储密钥保管库名称、应用程序 ID 和证书指纹 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-174">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="cf0e3-175">导航到 Azure 门户中的 **密钥保管库** 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-175">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="cf0e3-176">在 Azure Key Vault 部分中，选择在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) 中创建的密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-176">Select the key vault you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="cf0e3-177">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-177">Select **Access policies**.</span></span>
1. <span data-ttu-id="cf0e3-178">选择“添加访问策略”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-178">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="cf0e3-179">打开 **机密权限** ，并为应用提供 **Get** 和 **List** 权限。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-179">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="cf0e3-180">选择 " **选择主体** "，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-180">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="cf0e3-181">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-181">Select the **Select** button.</span></span>
1. <span data-ttu-id="cf0e3-182">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-182">Select **OK**.</span></span>
1. <span data-ttu-id="cf0e3-183">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-183">Select **Save**.</span></span>
1. <span data-ttu-id="cf0e3-184">部署应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-184">Deploy the app.</span></span>

<span data-ttu-id="cf0e3-185">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-185">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="cf0e3-186">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-186">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="cf0e3-187">) 部分 (层次结构值：使用 `:` (冒号) 表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-187">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="cf0e3-188">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-188">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="cf0e3-189">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-189">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="cf0e3-190">应用 `AddAzureKeyVault` 使用文件提供的值调用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-190">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="cf0e3-191">示例值：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-191">Example values:</span></span>

* <span data-ttu-id="cf0e3-192">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-192">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="cf0e3-193">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-193">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="cf0e3-194">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-194">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="cf0e3-195">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf0e3-195">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="cf0e3-196">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-196">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cf0e3-197">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-197">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="cf0e3-198">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-198">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="cf0e3-199">将托管标识用于 Azure 资源</span><span class="sxs-lookup"><span data-stu-id="cf0e3-199">Use managed identities for Azure resources</span></span>

<span data-ttu-id="cf0e3-200">**部署到 azure 的应用** 可以利用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-200">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview).</span></span> <span data-ttu-id="cf0e3-201">托管标识允许应用使用 Azure AD 身份验证 Azure Key Vault 进行身份验证，而无需凭据 (应用程序 ID 和密码/客户) 端密码。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-201">A managed identity allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="cf0e3-202">当 `#define` *Program* 顶层的预处理器指令设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-202">The sample app uses managed identities for Azure resources when the `#define` preprocessor directive at the top of *Program.cs* is set to `Managed`.</span></span>

<span data-ttu-id="cf0e3-203">在应用的文件中输入保管库名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="cf0e3-204">当设置为版本时，示例应用不需要应用程序 ID 和密码 (的客户端机密) `Managed` ，因此你可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="cf0e3-205">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在文件中的保管库名称访问 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="cf0e3-206">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="cf0e3-207">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="cf0e3-208">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="cf0e3-209">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="cf0e3-210">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="cf0e3-211">使用 Azure CLI、PowerShell 或 Azure 门户 **重启应用**。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="cf0e3-212">示例应用：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-212">The sample app:</span></span>

* <span data-ttu-id="cf0e3-213">创建 <xref:Azure.Identity.DefaultAzureCredential> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-213">Creates an instance of the <xref:Azure.Identity.DefaultAzureCredential> class.</span></span> <span data-ttu-id="cf0e3-214">凭据尝试从 Azure 资源的环境中获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-214">The credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="cf0e3-215"><xref:Azure.Security.KeyVault.Secrets.SecretClient>使用实例创建新的 `DefaultAzureCredential` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-215">A new <xref:Azure.Security.KeyVault.Secrets.SecretClient> is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="cf0e3-216">`SecretClient`实例与实例一起使用 `KeyVaultSecretManager` ，后者加载机密值并将双引号 (`--`) 替换为 `:` 键名称 () 中的冒号。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-216">The `SecretClient` instance is used with a `KeyVaultSecretManager` instance, which loads secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="cf0e3-217">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-217">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="cf0e3-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf0e3-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="cf0e3-219">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cf0e3-220">在开发环境中，机密值具有 `_dev` 后缀，因为机密管理器提供了这些值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-220">In the Development environment, secret values have the `_dev` suffix because they're provided by Secret Manager.</span></span> <span data-ttu-id="cf0e3-221">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="cf0e3-222">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="cf0e3-223">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="cf0e3-224">有关将提供程序与托管标识和 Azure Pipelines 一起使用的信息，请参阅使用 [托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-224">For information on using the provider with a managed identity and Azure Pipelines, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="cf0e3-225">配置选项</span><span class="sxs-lookup"><span data-stu-id="cf0e3-225">Configuration options</span></span>

<span data-ttu-id="cf0e3-226">`AddAzureKeyVault` 可以接受 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> 对象：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-226">`AddAzureKeyVault` can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> object:</span></span>

```csharp
config.AddAzureKeyVault(
    new SecretClient(
        new Uri("Your Key Vault Endpoint"),
        new DefaultAzureCredential()),
        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

<span data-ttu-id="cf0e3-227">`AzureKeyVaultConfigurationOptions`对象包含以下属性。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-227">The `AzureKeyVaultConfigurationOptions` object contains the following properties.</span></span>

| <span data-ttu-id="cf0e3-228">属性</span><span class="sxs-lookup"><span data-stu-id="cf0e3-228">Property</span></span>         | <span data-ttu-id="cf0e3-229">说明</span><span class="sxs-lookup"><span data-stu-id="cf0e3-229">Description</span></span> |
| ---------------- | ----------- |
| <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions.Manager%2A>        | <span data-ttu-id="cf0e3-230"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 用于控制机密加载的实例。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-230"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions.ReloadInterval%2A> | <span data-ttu-id="cf0e3-231">`TimeSpan` 如果为，则在轮询密钥保管库以进行更改之间等待。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-231">`TimeSpan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="cf0e3-232">默认值为 `null` (不) 重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-232">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="cf0e3-233">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="cf0e3-233">Use a key name prefix</span></span>

<span data-ttu-id="cf0e3-234">`AddAzureKeyVault` 提供一个重载，该重载接受的实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-234">`AddAzureKeyVault` provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="cf0e3-235">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-235">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="cf0e3-236">例如，这种方法可让你根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-236">This technique allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="cf0e3-237">不要对 key vault 机密使用前缀来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-237">Don't use prefixes on key vault secrets to:</span></span>
>
> * <span data-ttu-id="cf0e3-238">将多个应用的机密放入同一个保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-238">Place secrets for multiple apps into the same vault.</span></span>
> * <span data-ttu-id="cf0e3-239">将环境机密 (例如， *开发* 与 *生产* 机密) 到同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-239">Place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span>
> 
> <span data-ttu-id="cf0e3-240">不同的应用程序和开发/生产环境应使用不同的密钥保管库来隔离应用程序环境，以实现最高的安全性级别。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-240">Different apps and development/production environments should use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="cf0e3-241">在下面的示例中，密钥保管库中建立了机密 (和使用开发环境的机密管理器) 对于 `5000-AppSecret` 密钥保管库机密名称) 不允许使用 (期间。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-241">In the following example, a secret is established in the key vault (and using Secret Manager for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="cf0e3-242">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-242">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="cf0e3-243">对于其他版本的应用，5.1.0.0 会将机密添加到密钥保管库 (并使用机密管理器) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-243">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using Secret Manager) for `5100-AppSecret`.</span></span> <span data-ttu-id="cf0e3-244">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时删除版本。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-244">Each app version loads its versioned secret value into its configuration as `AppSecret`, removing the version as it loads the secret.</span></span>

<span data-ttu-id="cf0e3-245">`AddAzureKeyVault` 使用自定义实现调用 `IKeyVaultSecretManager` ：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-245">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager` implementation:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="cf0e3-246">该实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-246">The implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="cf0e3-247">`Load` 当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-247">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="cf0e3-248">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-248">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="cf0e3-249">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="cf0e3-249">`GetKey`:</span></span>
  * <span data-ttu-id="cf0e3-250">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-250">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="cf0e3-251">将任何名称中的两个短划线替换为 `KeyDelimiter` ，它是在配置 (中使用的分隔符，通常为冒号) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-251">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="cf0e3-252">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-252">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/PrefixKeyVaultSecretManager.cs)]

<span data-ttu-id="cf0e3-253">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找版本前缀机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-253">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the version-prefixed secrets.</span></span> <span data-ttu-id="cf0e3-254">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-254">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="cf0e3-255">它从机密的名称中删除版本前缀。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-255">It removes the version prefix from the secret's name.</span></span> <span data-ttu-id="cf0e3-256">将返回密钥的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-256">The rest of the secret name is returned for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="cf0e3-257">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-257">When this approach is implemented:</span></span>

1. <span data-ttu-id="cf0e3-258">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-258">The app's version specified in the app's project file.</span></span> <span data-ttu-id="cf0e3-259">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-259">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="cf0e3-260">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-260">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="cf0e3-261">在 [密码管理器](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-261">Save the following secrets locally with [Secret Manager](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="cf0e3-262">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-262">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="cf0e3-263">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-263">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="cf0e3-264">的字符串机密与 `5000-AppSecret` 应用程序的项目文件中指定的应用版本匹配 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-264">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="cf0e3-265">`5000`用破折号)  (的版本将从键名称中去除。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-265">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="cf0e3-266">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-266">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="cf0e3-267">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-267">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="cf0e3-268">你还可以向提供自己的 <xref:Azure.Security.KeyVault.Secrets.SecretClient> 实现 `AddAzureKeyVault` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-268">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="cf0e3-269">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-269">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="cf0e3-270">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="cf0e3-270">Bind an array to a class</span></span>

<span data-ttu-id="cf0e3-271">提供程序可以将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-271">The provider can read configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="cf0e3-272">当从允许键包含冒号 () 分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组 (`:0:` 、) 的键 `:1:` &hellip; `:{n}:` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-272">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="cf0e3-273">有关详细信息，请参阅 [配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-273">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="cf0e3-274">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-274">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="cf0e3-275">本文档中所述的方法使用双短划线 (`--`) 作为层次结构值 (节) 的分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-275">The approach described in this document uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="cf0e3-276">数组键存储在 Azure Key Vault 中 `--0--` ， (，，) 为双短划线和数值段 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-276">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="cf0e3-277">检查 JSON 文件提供的以下 [Serilog](https://serilog.net/) 日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-277">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="cf0e3-278">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog *接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-278">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="cf0e3-279">前面的 JSON 文件中所示的配置将存储在 Azure Key Vault 中，使用双划线 (`--`) 表示法和数值段：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-279">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="cf0e3-280">密钥</span><span class="sxs-lookup"><span data-stu-id="cf0e3-280">Key</span></span> | <span data-ttu-id="cf0e3-281">值</span><span class="sxs-lookup"><span data-stu-id="cf0e3-281">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="cf0e3-282">重载机密</span><span class="sxs-lookup"><span data-stu-id="cf0e3-282">Reload secrets</span></span>

<span data-ttu-id="cf0e3-283">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-283">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="cf0e3-284">在执行之前，应用程序不会遵守密钥保管库中的已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-284">Expired, disabled, and updated secrets in the key vault aren't respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="cf0e3-285">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="cf0e3-285">Disabled and expired secrets</span></span>

<span data-ttu-id="cf0e3-286">禁用和过期的机密会引发 <xref:Azure.RequestFailedException> 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-286">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="cf0e3-287">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-287">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cf0e3-288">疑难解答</span><span class="sxs-lookup"><span data-stu-id="cf0e3-288">Troubleshoot</span></span>

<span data-ttu-id="cf0e3-289">当应用无法使用访问接口加载配置时，会将错误消息写入 [ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-289">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="cf0e3-290">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-290">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="cf0e3-291">Azure AD 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-291">The app or certificate isn't configured correctly in Azure AD.</span></span>
* <span data-ttu-id="cf0e3-292">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-292">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="cf0e3-293">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-293">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="cf0e3-294">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-294">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="cf0e3-295">在密钥保管库中，配置数据 (名称-值对) 错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-295">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="cf0e3-296">应用具有错误的密钥保管库名称 (`KeyVaultName`) 、Azure AD 应用程序 ID () 或 Azure AD () Azure AD (`AzureADApplicationId` `AzureADCertThumbprint` 目录 id `AzureADDirectoryId` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-296">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application ID (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD Directory ID (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="cf0e3-297">要尝试加载的值在应用中 (名称) 的配置密钥不正确。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-297">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="cf0e3-298">为应用添加密钥保管库访问策略时，已创建策略，但没有在 **访问策略** UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-298">When adding the key vault access policy for the app, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf0e3-299">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf0e3-299">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="cf0e3-300">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="cf0e3-300">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="cf0e3-301">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="cf0e3-301">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="cf0e3-302">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="cf0e3-302">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="cf0e3-303">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="cf0e3-303">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
* [<span data-ttu-id="cf0e3-304">通过 .NET 和 Azure Key Vault Secretless 应用</span><span class="sxs-lookup"><span data-stu-id="cf0e3-304">Secretless apps with .NET and Azure Key Vault</span></span>](https://channel9.msdn.com/Shows/On-NET/Secretless-apps-with-NET-and-Azure-Key-Vault)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf0e3-305">本文档说明如何使用 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-305">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) configuration provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="cf0e3-306">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-306">Azure Key Vault is a cloud-based service that helps safeguard cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="cf0e3-307">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-307">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="cf0e3-308">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-308">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="cf0e3-309">满足 FIPS 140-2 第2级验证的硬件安全模块在存储配置数据时 (Hsm) 的要求。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-309">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSMs) when storing configuration data.</span></span>

<span data-ttu-id="cf0e3-310">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cf0e3-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="cf0e3-311">包</span><span class="sxs-lookup"><span data-stu-id="cf0e3-311">Packages</span></span>

<span data-ttu-id="cf0e3-312">向Microsoft.Extensions.Configu 添加包引用 [ 。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 包。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-312">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="cf0e3-313">示例应用</span><span class="sxs-lookup"><span data-stu-id="cf0e3-313">Sample app</span></span>

<span data-ttu-id="cf0e3-314">该示例应用在 `#define` *程序 .cs* 顶部由预处理器指令确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-314">The sample app runs in either of two modes determined by the `#define` preprocessor directive at the top of *Program.cs*:</span></span>

* <span data-ttu-id="cf0e3-315">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-315">`Certificate`: Demonstrates using an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="cf0e3-316">可以从任何位置运行此示例，无论部署到 Azure App Service 还是任何可为 ASP.NET Core 应用提供服务的主机。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-316">This sample can be run from any location, whether deployed to Azure App Service or any host that can serve an ASP.NET Core app.</span></span>
* <span data-ttu-id="cf0e3-317">`Managed`：演示如何使用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview) 对应用进行身份验证，以便与 AZURE ACTIVE DIRECTORY (AD) 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-317">`Managed`: Demonstrates using [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure Active Directory (AD) authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="cf0e3-318">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码 (客户端密钥) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-318">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="cf0e3-319">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-319">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="cf0e3-320">按照 [使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-320">Follow the guidance in the [Use the managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="cf0e3-321">有关使用预处理器指令配置示例应用 () 的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-321">For more information configuring a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="cf0e3-322">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="cf0e3-322">Secret storage in the Development environment</span></span>

<span data-ttu-id="cf0e3-323">使用 [机密管理器](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-323">Set secrets locally using [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cf0e3-324">在开发环境中的本地计算机上运行示例应用时，会从本地用户机密存储区中加载机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-324">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="cf0e3-325">机密管理器需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-325">Secret Manager requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="cf0e3-326">将属性值 (`{GUID}`) 设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-326">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="cf0e3-327">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-327">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="cf0e3-328">配置节 (层次结构值) `:` 在 [ASP.NET Core 配置](xref:fundamentals/configuration/index) 项名称中将 (冒号) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-328">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="cf0e3-329">机密管理器用在打开的命令行界面 [中，其中](xref:fundamentals/index#content-root) `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-329">Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="cf0e3-330">从项目的 [内容根](xref:fundamentals/index#content-root) 在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-330">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="cf0e3-331">如果这些机密存储在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-331">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="cf0e3-332">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-332">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="cf0e3-333">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="cf0e3-333">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="cf0e3-334">[快速入门：使用 Azure CLI 设置和检索 Azure Key Vault 中](/azure/key-vault/quick-create-cli)的说明用于创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-334">The instructions provided by [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span>

1. <span data-ttu-id="cf0e3-335">使用 [Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud Shell：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-335">Open Azure Cloud Shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="cf0e3-336">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-336">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="cf0e3-337">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-337">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="cf0e3-338">在浏览器中打开 Cloud Shell，并提供 " **启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-338">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="cf0e3-339">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-339">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="cf0e3-340">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/) 和 [概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-340">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="cf0e3-341">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-341">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="cf0e3-342">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的名称， `{LOCATION}` 是 Azure 区域：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-342">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the new resource group's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cf0e3-343">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-343">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the new key vault's name and `{LOCATION}` is the Azure region:</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="cf0e3-344">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-344">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="cf0e3-345">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-345">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="cf0e3-346">配置节 (的分层值) 使用 `--` (两个短划线) 作为分隔符，因为密钥保管库机密名称中不允许使用冒号。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-346">Hierarchical values (configuration sections) use `--` (two dashes) as a delimiter, as colons aren't allowed in key vault secret names.</span></span> <span data-ttu-id="cf0e3-347">冒号在 [ASP.NET Core 配置](xref:fundamentals/configuration/index)中从子项分隔节。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-347">Colons delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="cf0e3-348">当机密加载到应用的配置中时，用冒号替换两个短划线序列。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-348">The two-dash sequence is replaced with a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="cf0e3-349">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-349">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="cf0e3-350">这些值包括一个 `_prod` 后缀，用于将它们与 `_dev` 从机密管理器中的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-350">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from Secret Manager.</span></span> <span data-ttu-id="cf0e3-351">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-351">Replace `{KEY VAULT NAME}` with the name of the key vault you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="cf0e3-352">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="cf0e3-352">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="cf0e3-353">配置 Azure AD、Azure Key Vault 和应用，以便在 **Azure 外托管应用时** 使用 Azure AD 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-353">Configure Azure AD, Azure Key Vault, and the app to use an Azure AD Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="cf0e3-354">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-354">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="cf0e3-355">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但不建议这样做。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-355">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, it's not recommended.</span></span> <span data-ttu-id="cf0e3-356">在 Azure 中托管应用时，请改用 [azure 资源的托管标识](#use-managed-identities-for-azure-resources) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-356">Instead, use [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="cf0e3-357">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-357">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="cf0e3-358">当 `#define` *Program .cs* 文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-358">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="cf0e3-359">创建 PKCS # 12 存档 (*.pfx*) 证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-359">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="cf0e3-360">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-360">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="cf0e3-361">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-361">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="cf0e3-362">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-362">Marking the key as exportable is optional.</span></span> <span data-ttu-id="cf0e3-363">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-363">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="cf0e3-364">将 PKCS # 12 存档 (*.pfx*) 证书导 () *.cer* 编码的证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-364">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="cf0e3-365">将应用注册 Azure AD (**应用注册**) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-365">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="cf0e3-366">将 DER 编码的证书 (*.cer*) 上传到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-366">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="cf0e3-367">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-367">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="cf0e3-368">导航到 " **证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-368">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="cf0e3-369">选择 " **上传证书** "，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-369">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="cf0e3-370">*.Cer*、 *pem* 或 *.crt* 证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-370">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="cf0e3-371">在应用的文件中存储密钥保管库名称、应用程序 ID 和证书指纹 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-371">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="cf0e3-372">导航到 Azure 门户中的 **密钥保管库** 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-372">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="cf0e3-373">在 Azure Key Vault 部分中，选择在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) 中创建的密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-373">Select the key vault you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="cf0e3-374">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-374">Select **Access policies**.</span></span>
1. <span data-ttu-id="cf0e3-375">选择“添加访问策略”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-375">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="cf0e3-376">打开 **机密权限** ，并为应用提供 **Get** 和 **List** 权限。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-376">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="cf0e3-377">选择 " **选择主体** "，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-377">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="cf0e3-378">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-378">Select the **Select** button.</span></span>
1. <span data-ttu-id="cf0e3-379">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-379">Select **OK**.</span></span>
1. <span data-ttu-id="cf0e3-380">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-380">Select **Save**.</span></span>
1. <span data-ttu-id="cf0e3-381">部署应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-381">Deploy the app.</span></span>

<span data-ttu-id="cf0e3-382">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-382">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="cf0e3-383">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-383">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="cf0e3-384">) 部分 (层次结构值：使用 `:` (冒号) 表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-384">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="cf0e3-385">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-385">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="cf0e3-386">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-386">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="cf0e3-387">应用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 使用文件提供的值调用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-387">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="cf0e3-388">示例值：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-388">Example values:</span></span>

* <span data-ttu-id="cf0e3-389">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-389">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="cf0e3-390">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-390">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="cf0e3-391">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-391">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="cf0e3-392">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf0e3-392">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="cf0e3-393">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-393">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cf0e3-394">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-394">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="cf0e3-395">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-395">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="cf0e3-396">将托管标识用于 Azure 资源</span><span class="sxs-lookup"><span data-stu-id="cf0e3-396">Use managed identities for Azure resources</span></span>

<span data-ttu-id="cf0e3-397">**部署到 azure 的应用** 可以利用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-397">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview).</span></span> <span data-ttu-id="cf0e3-398">托管标识允许应用使用 Azure AD 身份验证 Azure Key Vault 进行身份验证，而无需凭据 (应用程序 ID 和密码/客户) 端密码。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-398">A managed identity allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="cf0e3-399">当 `#define` *Program .cs* 文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-399">The sample app uses managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="cf0e3-400">在应用的文件中输入保管库名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-400">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="cf0e3-401">当设置为版本时，示例应用不需要应用程序 ID 和密码 (的客户端机密) `Managed` ，因此你可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-401">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="cf0e3-402">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在文件中的保管库名称访问 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-402">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="cf0e3-403">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-403">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="cf0e3-404">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-404">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="cf0e3-405">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-405">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="cf0e3-406">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-406">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="cf0e3-407">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-407">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="cf0e3-408">使用 Azure CLI、PowerShell 或 Azure 门户 **重启应用**。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-408">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="cf0e3-409">示例应用：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-409">The sample app:</span></span>

* <span data-ttu-id="cf0e3-410">创建 `AzureServiceTokenProvider` 不带连接字符串的类的实例。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-410">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="cf0e3-411">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-411">When a connection string isn't provided, the provider attempts to obtain an access token from managed identities for Azure resources.</span></span>
* <span data-ttu-id="cf0e3-412"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 实例标记回调创建新的。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-412">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="cf0e3-413"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>实例与加载所有机密值的的默认实现一起使用， <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 并将 `--` 冒号 () 替换为 `:` 键名称中 () 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-413">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="cf0e3-414">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="cf0e3-414">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="cf0e3-415">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cf0e3-415">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="cf0e3-416">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-416">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="cf0e3-417">在开发环境中，机密值具有 `_dev` 后缀，因为机密管理器提供了这些值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-417">In the Development environment, secret values have the `_dev` suffix because they're provided by Secret Manager.</span></span> <span data-ttu-id="cf0e3-418">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-418">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="cf0e3-419">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-419">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="cf0e3-420">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-420">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="cf0e3-421">有关将提供程序与托管标识和 Azure Pipelines 一起使用的信息，请参阅使用 [托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-421">For information on using the provider with a managed identity and Azure Pipelines, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="cf0e3-422">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="cf0e3-422">Use a key name prefix</span></span>

<span data-ttu-id="cf0e3-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 提供接受的实现的重载 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>.</span></span> <span data-ttu-id="cf0e3-424">此重载允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-424">This overload allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="cf0e3-425">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-425">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="cf0e3-426">例如，这种方法可让你根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-426">This technique allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="cf0e3-427">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或放置环境机密 (例如， *开发* 与 *生产* 机密) 到同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-427">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="cf0e3-428">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-428">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="cf0e3-429">在下面的示例中，密钥保管库中建立了机密 (和使用开发环境的机密管理器) 对于 `5000-AppSecret` 密钥保管库机密名称) 不允许使用 (期间。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-429">In the following example, a secret is established in the key vault (and using Secret Manager for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="cf0e3-430">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-430">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="cf0e3-431">对于其他版本的应用，5.1.0.0 会将机密添加到密钥保管库 (并使用机密管理器) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-431">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using Secret Manager) for `5100-AppSecret`.</span></span> <span data-ttu-id="cf0e3-432">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时删除版本。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-432">Each app version loads its versioned secret value into its configuration as `AppSecret`, removing the version as it loads the secret.</span></span>

<span data-ttu-id="cf0e3-433"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 使用自定义调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-433"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="cf0e3-434">该 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-434">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="cf0e3-435">`Load` 当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-435">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="cf0e3-436">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-436">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="cf0e3-437">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="cf0e3-437">`GetKey`:</span></span>
  * <span data-ttu-id="cf0e3-438">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-438">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="cf0e3-439">将任何名称中的两个短划线替换为 `KeyDelimiter` ，它是在配置 (中使用的分隔符，通常为冒号) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-439">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="cf0e3-440">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-440">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/PrefixKeyVaultSecretManager.cs)]

<span data-ttu-id="cf0e3-441">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-441">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="cf0e3-442">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-442">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="cf0e3-443">它从机密的名称中删除版本前缀。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-443">It removes the version prefix from the secret's name.</span></span> <span data-ttu-id="cf0e3-444">将返回密钥的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-444">The rest of the secret name is returned for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="cf0e3-445">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-445">When this approach is implemented:</span></span>

1. <span data-ttu-id="cf0e3-446">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-446">The app's version specified in the app's project file.</span></span> <span data-ttu-id="cf0e3-447">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-447">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="cf0e3-448">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-448">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="cf0e3-449">在 [密码管理器](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-449">Save the following secrets locally with [Secret Manager](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="cf0e3-450">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-450">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="cf0e3-451">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-451">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="cf0e3-452">的字符串机密与 `5000-AppSecret` 应用程序的项目文件中指定的应用版本匹配 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-452">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="cf0e3-453">`5000`用破折号)  (的版本将从键名称中去除。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-453">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="cf0e3-454">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-454">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="cf0e3-455">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-455">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="cf0e3-456">你还可以向提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A> 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-456">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault%2A>.</span></span> <span data-ttu-id="cf0e3-457">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-457">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="cf0e3-458">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="cf0e3-458">Bind an array to a class</span></span>

<span data-ttu-id="cf0e3-459">提供程序可以将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-459">The provider can read configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="cf0e3-460">当从允许键包含冒号 () 分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组 (`:0:` 、) 的键 `:1:` &hellip; `:{n}:` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-460">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="cf0e3-461">有关详细信息，请参阅 [配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-461">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="cf0e3-462">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-462">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="cf0e3-463">本文档中所述的方法使用双短划线 (`--`) 作为层次结构值 (节) 的分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-463">The approach described in this document uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="cf0e3-464">数组键存储在 Azure Key Vault 中 `--0--` ， (，，) 为双短划线和数值段 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-464">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="cf0e3-465">检查 JSON 文件提供的以下 [Serilog](https://serilog.net/) 日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-465">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="cf0e3-466">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog *接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-466">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="cf0e3-467">前面的 JSON 文件中所示的配置将存储在 Azure Key Vault 中，使用双划线 (`--`) 表示法和数值段：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-467">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="cf0e3-468">密钥</span><span class="sxs-lookup"><span data-stu-id="cf0e3-468">Key</span></span> | <span data-ttu-id="cf0e3-469">值</span><span class="sxs-lookup"><span data-stu-id="cf0e3-469">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="cf0e3-470">重载机密</span><span class="sxs-lookup"><span data-stu-id="cf0e3-470">Reload secrets</span></span>

<span data-ttu-id="cf0e3-471">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-471">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="cf0e3-472">在执行之前，应用程序不会遵守密钥保管库中的已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-472">Expired, disabled, and updated secrets in the key vault aren't respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="cf0e3-473">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="cf0e3-473">Disabled and expired secrets</span></span>

<span data-ttu-id="cf0e3-474">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-474">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="cf0e3-475">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-475">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cf0e3-476">疑难解答</span><span class="sxs-lookup"><span data-stu-id="cf0e3-476">Troubleshoot</span></span>

<span data-ttu-id="cf0e3-477">当应用无法使用访问接口加载配置时，会将错误消息写入 [ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-477">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="cf0e3-478">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="cf0e3-478">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="cf0e3-479">Azure AD 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-479">The app or certificate isn't configured correctly in Azure AD.</span></span>
* <span data-ttu-id="cf0e3-480">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-480">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="cf0e3-481">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-481">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="cf0e3-482">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-482">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="cf0e3-483">在密钥保管库中，配置数据 (名称-值对) 错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-483">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="cf0e3-484">应用具有错误的密钥保管库名称 (`KeyVaultName`) 、Azure AD 应用程序 ID (`AzureADApplicationId`) 或 Azure AD 证书指纹 (`AzureADCertThumbprint`) 。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-484">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application ID (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="cf0e3-485">要尝试加载的值在应用中 (名称) 的配置密钥不正确。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-485">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="cf0e3-486">向密钥保管库添加应用的访问策略时，策略已创建，但未在 **访问策略** UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="cf0e3-486">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf0e3-487">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf0e3-487">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="cf0e3-488">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="cf0e3-488">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="cf0e3-489">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="cf0e3-489">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="cf0e3-490">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="cf0e3-490">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="cf0e3-491">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="cf0e3-491">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
