---
title: ASP.NET Core Blazor 文件上传
author: guardrex
description: 了解如何使用 InputFile 组件在 Blazor 中上传文件。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 02/18/2021
uid: blazor/file-uploads
ms.openlocfilehash: a31821f03efd39d774a4a3c61d027983a1783e2d
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395118"
---
# <a name="aspnet-core-blazor-file-uploads"></a>ASP.NET Core Blazor 文件上传

使用 `InputFile` 组件将浏览器文件数据（包括文件上传）读入 .NET 代码。

> [!WARNING]
> 请始终遵循文件上传安全最佳做法。 有关详细信息，请参阅 <xref:mvc/models/file-uploads#security-considerations>。

## <a name="inputfile-component"></a>`InputFile` 组件

`InputFile` 组件呈现为 `file` 类型的 HTML 输入。

默认情况下，用户选择单个文件。 可添加 `multiple` 属性以允许用户一次上传多个文件。 当用户选择了一个或多个文件时，`InputFile` 组件将触发 `OnChange` 事件并传入 `InputFileChangeEventArgs`，以允许访问所选文件列表和各文件的详细信息。

从用户选择的文件中读取数据：

* 对文件调用 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`，并从返回的流中进行读取。 有关详细信息，请参阅[文件流](#file-streams)部分。
* `OpenReadStream` 返回的 <xref:System.IO.Stream> 强制采用正在读取的 `Stream` 的最大大小（以字节为单位）。 默认情况下，允许读取不大于 512,000 字节 (500 KB) 的文件，任何进一步读取都将导致异常。 此限制旨在防止开发人员意外地将大型文件读入到内存中。 如果需要，可以使用 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` 上的 `maxAllowedSize` 参数指定更大的大小。
* 避免将传入的文件流直接读入到内存中。 例如，不要将文件字节复制到 <xref:System.IO.MemoryStream>，也不要以字节数组的形式进行读取。 这些方法可能会导致性能和安全问题，尤其是在 Blazor Server 中。 请考虑将文件字节复制到外部存储（如 blob 或磁盘上的文件）。

接收图像文件的组件可以对文件调用 `RequestImageFileAsync` 便利方法，在图像流式传入应用之前，在浏览器的 JavaScript 运行时内调整图像数据的大小。

以下示例演示在组件中上传多个文件。 通过 `InputFileChangeEventArgs.GetMultipleFiles`，可以读取多个文件。 请指定希望读取的最大文件数，以防止恶意用户上传的文件数超过应用的预期值。 如果文件上传不支持多个文件，则可以通过 `InputFileChangeEventArgs.File` 读取第一个文件，并且只能读取此文件。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> 位于 <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 命名空间，后者通常是应用的 `_Imports.razor` 文件中的一个命名空间。

`Pages/UploadFiles.razor`:

```razor
@page "/upload-files"
@using System.IO

<h3>Upload Files</h3>

<p>
    <label>
        Max file size:
        <input type="number" @bind="maxFileSize" />
    </label>
</p>

<p>
    <label>
        Max allowed files:
        <input type="number" @bind="maxAllowedFiles" />
    </label>
</p>

<p>
    <label>
        Upload up to @maxAllowedFiles files of up to @maxFileSize bytes each:
        <InputFile OnChange="@LoadFiles" multiple />
    </label>
</p>

<p>@exceptionMessage</p>

@if (isLoading)
{
    <p>Loading...</p>
}

<ul>
    @foreach (var (file, content) in loadedFiles)
    {
        <li>
            <ul>
                <li>Name: @file.Name</li>
                <li>Last modified: @file.LastModified.ToString()</li>
                <li>Size (bytes): @file.Size</li>
                <li>Content type: @file.ContentType</li>
                <li>Content: @content</li>
            </ul>
        </li>
    }
</ul>

@code {
    private Dictionary<IBrowserFile, string> loadedFiles =
        new Dictionary<IBrowserFile, string>();
    private long maxFileSize = 1024 * 15;
    private int maxAllowedFiles = 3;
    private bool isLoading;
    string exceptionMessage;

    async Task LoadFiles(InputFileChangeEventArgs e)
    {
        isLoading = true;
        loadedFiles.Clear();
        exceptionMessage = string.Empty;

        try
        {
            foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
            {
                using var reader = 
                    new StreamReader(file.OpenReadStream(maxFileSize));

                loadedFiles.Add(file, await reader.ReadToEndAsync());
            }
        }
        catch (Exception ex)
        {
            exceptionMessage = ex.Message;
        }

        isLoading = false;
    }
}
```

`IBrowserFile` 会以属性形式返回[浏览器公开的元数据](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)。 此元数据可用于进行初步验证。

## <a name="upload-files-to-a-server"></a>将文件上传到服务器

以下示例演示了如何使用托管 Blazor WebAssembly 解决方案将文件上传到服务器。

> [!WARNING]
> 向用户提供向服务器上传文件的功能时，必须格外小心。 有关详细信息，请参阅 <xref:mvc/models/file-uploads#security-considerations>。

**`Shared`** 项目中的以下 `UploadResult` 类维护已上传文件的结果。 如果文件无法上传到服务器上，`ErrorCode` 中会返回错误代码以向用户显示。 服务器上会针对每个文件生成安全存储的文件名，并在 `StoredFileName` 中返回给客户端以供显示。 客户端和服务器之间会使用 `FileName` 中的不安全/不受信任的文件名对文件进行键控。

`UploadResult.cs`:

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

**`Client`** 项目中的以下 `UploadFiles` 组件：

* 允许用户从客户端上传文件。
* 在用户界面中显示由客户端提供的不受信任/不安全的文件名，因为 Razor 自动对字符串进行 HTML 编码。 **在其他情况下，不要信任客户端提供的文件名。**

`Pages/UploadFiles.razor`:

```razor
@page "/upload-files"
@using System.Linq
@using Microsoft.Extensions.Logging
@inject HttpClient Http
@inject ILogger<UploadFiles> logger

<h1>Upload Files</h1>

<p>
    <label>
        Upload up to @maxAllowedFiles files:
        <InputFile OnChange="@OnInputFileChange" multiple />
    </label>
</p>

@if (files.Count > 0)
{
    <div class="card">
        <div class="card-body">
            <ul>
                @foreach (var file in files)
                {
                    <li>
                        File: @file.Name
                        <br>
                        @if (FileUpload(uploadResults, file.Name, logger,
                            out var result))
                        {
                            <span>
                                Stored File Name: @result.StoredFileName
                            </span>
                        }
                        else
                        {
                            <span>
                                There was an error uploading the file
                                (Error: @result.ErrorCode).
                            </span>
                        }
                    </li>
                }
            </ul>
        </div>
    </div>
}

@code {
    private IList<File> files = new List<File>();
    private IList<UploadResult> uploadResults = new List<UploadResult>();
    private int maxAllowedFiles = 3;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        shouldRender = false;
        long maxFileSize = 1024 * 1024 * 15;
        var upload = false;

        using var content = new MultipartFormDataContent();

        foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
        {
            if (uploadResults.SingleOrDefault(
                f => f.FileName == file.Name) is null)
            {
                var fileContent = new StreamContent(file.OpenReadStream());

                files.Add(
                    new File()
                    {
                        Name = file.Name,
                    });

                if (file.Size < maxFileSize)
                {
                    content.Add(
                        content: fileContent,
                        name: "\"files\"",
                        fileName: file.Name);

                    upload = true;
                }
                else
                {
                    logger.LogInformation("{FileName} not uploaded", file.Name);

                    uploadResults.Add(
                        new UploadResult()
                        {
                            FileName = file.Name,
                            ErrorCode = 6,
                            Uploaded = false,
                        });
                }
            }
        }

        if (upload)
        {
            var response = await Http.PostAsync("/Filesave", content);

            var newUploadResults = await response.Content
                .ReadFromJsonAsync<IList<UploadResult>>();

            uploadResults = uploadResults.Concat(newUploadResults).ToList();
        }

        shouldRender = true;
    }

    private static bool FileUpload(IList<UploadResult> uploadResults,
        string fileName, ILogger<UploadFiles> logger, out UploadResult result)
    {
        result = uploadResults.SingleOrDefault(f => f.FileName == fileName);

        if (result is null)
        {
            logger.LogInformation("{FileName} not uploaded", fileName);
            result = new UploadResult();
            result.ErrorCode = 5;
        }

        return result.Uploaded;
    }

    private class File
    {
        public string Name { get; set; }
    }
}
```

要使用下面的代码，请在 **`Server`** 项目的根目录下创建一个 `Development/unsafe_uploads` 文件夹。

**`Server`** 项目中的以下 `FilesaveController` 控制器保存从客户端上传的文件。

> [!WARNING]
> 该示例直接保存文件而没有扫描其内容。 在大多数生产情景中，会对文件中使用防病毒/反恶意软件扫描程序 API，然后文件才可供下载或供其他系统使用。 有关将文件上传到服务器时的安全注意事项的更多信息，请参阅“<xref:mvc/models/file-uploads#security-considerations>”。

`Controllers/FilesaveController.cs`:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

[ApiController]
[Route("[controller]")]
public class FilesaveController : ControllerBase
{
    private readonly IWebHostEnvironment env;
    private readonly ILogger<FilesaveController> logger;

    public FilesaveController(IWebHostEnvironment env, 
        ILogger<FilesaveController> logger)
    {
        this.env = env;
        this.logger = logger;
    }

    [HttpPost]
    public async Task<ActionResult<IList<UploadResult>>> PostFile(
        [FromForm] IEnumerable<IFormFile> files)
    {
        var maxAllowedFiles = 3;
        long maxFileSize = 1024 * 1024 * 15;
        var filesProcessed = 0;
        var resourcePath = new Uri($"{Request.Scheme}://{Request.Host}/");
        IList<UploadResult> uploadResults = new List<UploadResult>();

        foreach (var file in files)
        {
            var uploadResult = new UploadResult();
            string trustedFileNameForFileStorage;
            var untrustedFileName = file.FileName;
            uploadResult.FileName = untrustedFileName;
            var trustedFileNameForDisplay = 
                WebUtility.HtmlEncode(untrustedFileName);

            if (filesProcessed < maxAllowedFiles)
            {
                if (file.Length == 0)
                {
                    logger.LogInformation("{FileName} length is 0", 
                        trustedFileNameForDisplay);
                    uploadResult.ErrorCode = 1;
                }
                else if (file.Length > maxFileSize)
                {
                    logger.LogInformation("{FileName} of {Length} bytes is " +
                        "larger than the limit of {Limit} bytes", 
                        trustedFileNameForDisplay, file.Length, maxFileSize);
                    uploadResult.ErrorCode = 2;
                }
                else
                {
                    try
                    {
                        trustedFileNameForFileStorage = Path.GetRandomFileName();
                        var path = Path.Combine(env.ContentRootPath, 
                            env.EnvironmentName, "unsafe_uploads", 
                            trustedFileNameForFileStorage);
                        using MemoryStream ms = new();
                        await file.CopyToAsync(ms);
                        await System.IO.File.WriteAllBytesAsync(path, ms.ToArray());
                        logger.LogInformation("{FileName} saved at {Path}", 
                            trustedFileNameForDisplay, path);
                        uploadResult.Uploaded = true;
                        uploadResult.StoredFileName = trustedFileNameForFileStorage;
                    }
                    catch (IOException ex)
                    {
                        logger.LogError("{FileName} error on upload: {Message}", 
                            trustedFileNameForDisplay, ex.Message);
                        uploadResult.ErrorCode = 3;
                    }
                }

                filesProcessed++;
            }
            else
            {
                logger.LogInformation("{FileName} not uploaded because the " +
                    "request exceeded the allowed {Count} of files", 
                    trustedFileNameForDisplay, maxAllowedFiles);
                uploadResult.ErrorCode = 4;
            }

            uploadResults.Add(uploadResult);
        }

        return new CreatedResult(resourcePath, uploadResults);
    }
}
```

## <a name="file-streams"></a>文件流

在 Blazor WebAssembly 中，文件数据直接流式传入浏览器中的 .NET 代码。

在 Blazor Server 中，从流中读取文件时，文件数据通过 SignalR 连接流式传入服务器上的 .NET 代码。 通过 <xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions>，可以配置 Blazor Server 的文件上传特性。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
