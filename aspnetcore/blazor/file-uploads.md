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
# <a name="aspnet-core-blazor-file-uploads"></a><span data-ttu-id="b5192-103">ASP.NET Core Blazor 文件上传</span><span class="sxs-lookup"><span data-stu-id="b5192-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="b5192-104">使用 `InputFile` 组件将浏览器文件数据（包括文件上传）读入 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="b5192-104">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="b5192-105">请始终遵循文件上传安全最佳做法。</span><span class="sxs-lookup"><span data-stu-id="b5192-105">Always follow file upload security best practices.</span></span> <span data-ttu-id="b5192-106">有关详细信息，请参阅 <xref:mvc/models/file-uploads#security-considerations>。</span><span class="sxs-lookup"><span data-stu-id="b5192-106">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="b5192-107">`InputFile` 组件</span><span class="sxs-lookup"><span data-stu-id="b5192-107">`InputFile` component</span></span>

<span data-ttu-id="b5192-108">`InputFile` 组件呈现为 `file` 类型的 HTML 输入。</span><span class="sxs-lookup"><span data-stu-id="b5192-108">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="b5192-109">默认情况下，用户选择单个文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-109">By default, the user selects single files.</span></span> <span data-ttu-id="b5192-110">可添加 `multiple` 属性以允许用户一次上传多个文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-110">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="b5192-111">当用户选择了一个或多个文件时，`InputFile` 组件将触发 `OnChange` 事件并传入 `InputFileChangeEventArgs`，以允许访问所选文件列表和各文件的详细信息。</span><span class="sxs-lookup"><span data-stu-id="b5192-111">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="b5192-112">从用户选择的文件中读取数据：</span><span class="sxs-lookup"><span data-stu-id="b5192-112">To read data from a user-selected file:</span></span>

* <span data-ttu-id="b5192-113">对文件调用 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`，并从返回的流中进行读取。</span><span class="sxs-lookup"><span data-stu-id="b5192-113">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="b5192-114">有关详细信息，请参阅[文件流](#file-streams)部分。</span><span class="sxs-lookup"><span data-stu-id="b5192-114">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="b5192-115">`OpenReadStream` 返回的 <xref:System.IO.Stream> 强制采用正在读取的 `Stream` 的最大大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="b5192-115">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="b5192-116">默认情况下，允许读取不大于 512,000 字节 (500 KB) 的文件，任何进一步读取都将导致异常。</span><span class="sxs-lookup"><span data-stu-id="b5192-116">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="b5192-117">此限制旨在防止开发人员意外地将大型文件读入到内存中。</span><span class="sxs-lookup"><span data-stu-id="b5192-117">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="b5192-118">如果需要，可以使用 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` 上的 `maxAllowedSize` 参数指定更大的大小。</span><span class="sxs-lookup"><span data-stu-id="b5192-118">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="b5192-119">避免将传入的文件流直接读入到内存中。</span><span class="sxs-lookup"><span data-stu-id="b5192-119">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="b5192-120">例如，不要将文件字节复制到 <xref:System.IO.MemoryStream>，也不要以字节数组的形式进行读取。</span><span class="sxs-lookup"><span data-stu-id="b5192-120">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="b5192-121">这些方法可能会导致性能和安全问题，尤其是在 Blazor Server 中。</span><span class="sxs-lookup"><span data-stu-id="b5192-121">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="b5192-122">请考虑将文件字节复制到外部存储（如 blob 或磁盘上的文件）。</span><span class="sxs-lookup"><span data-stu-id="b5192-122">Instead, consider copying file bytes to an external store, such as a blob or a file on disk.</span></span>

<span data-ttu-id="b5192-123">接收图像文件的组件可以对文件调用 `RequestImageFileAsync` 便利方法，在图像流式传入应用之前，在浏览器的 JavaScript 运行时内调整图像数据的大小。</span><span class="sxs-lookup"><span data-stu-id="b5192-123">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="b5192-124">以下示例演示在组件中上传多个文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-124">The following example demonstrates multiple file upload in a component.</span></span> <span data-ttu-id="b5192-125">通过 `InputFileChangeEventArgs.GetMultipleFiles`，可以读取多个文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-125">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="b5192-126">请指定希望读取的最大文件数，以防止恶意用户上传的文件数超过应用的预期值。</span><span class="sxs-lookup"><span data-stu-id="b5192-126">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="b5192-127">如果文件上传不支持多个文件，则可以通过 `InputFileChangeEventArgs.File` 读取第一个文件，并且只能读取此文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-127">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload doesn't support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="b5192-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> 位于 <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 命名空间，后者通常是应用的 `_Imports.razor` 文件中的一个命名空间。</span><span class="sxs-lookup"><span data-stu-id="b5192-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="b5192-129">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5192-129">`Pages/UploadFiles.razor`:</span></span>

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

<span data-ttu-id="b5192-130">`IBrowserFile` 会以属性形式返回[浏览器公开的元数据](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)。</span><span class="sxs-lookup"><span data-stu-id="b5192-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="b5192-131">此元数据可用于进行初步验证。</span><span class="sxs-lookup"><span data-stu-id="b5192-131">This metadata can be useful for preliminary validation.</span></span>

## <a name="upload-files-to-a-server"></a><span data-ttu-id="b5192-132">将文件上传到服务器</span><span class="sxs-lookup"><span data-stu-id="b5192-132">Upload files to a server</span></span>

<span data-ttu-id="b5192-133">以下示例演示了如何使用托管 Blazor WebAssembly 解决方案将文件上传到服务器。</span><span class="sxs-lookup"><span data-stu-id="b5192-133">The following example demonstrates uploading files to a server using a hosted Blazor WebAssembly solution.</span></span>

> [!WARNING]
> <span data-ttu-id="b5192-134">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="b5192-134">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="b5192-135">有关详细信息，请参阅 <xref:mvc/models/file-uploads#security-considerations>。</span><span class="sxs-lookup"><span data-stu-id="b5192-135">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="b5192-136">**`Shared`** 项目中的以下 `UploadResult` 类维护已上传文件的结果。</span><span class="sxs-lookup"><span data-stu-id="b5192-136">The following `UploadResult` class in the **`Shared`** project maintains the result of an uploaded file.</span></span> <span data-ttu-id="b5192-137">如果文件无法上传到服务器上，`ErrorCode` 中会返回错误代码以向用户显示。</span><span class="sxs-lookup"><span data-stu-id="b5192-137">When a file fails to upload on the server, an error code is returned in `ErrorCode` for display to the user.</span></span> <span data-ttu-id="b5192-138">服务器上会针对每个文件生成安全存储的文件名，并在 `StoredFileName` 中返回给客户端以供显示。</span><span class="sxs-lookup"><span data-stu-id="b5192-138">A safe stored file name is generated on the server for each file and returned to the client in `StoredFileName` for display.</span></span> <span data-ttu-id="b5192-139">客户端和服务器之间会使用 `FileName` 中的不安全/不受信任的文件名对文件进行键控。</span><span class="sxs-lookup"><span data-stu-id="b5192-139">Files are keyed between the client and server using the unsafe/untrusted file name in `FileName`.</span></span>

<span data-ttu-id="b5192-140">`UploadResult.cs`:</span><span class="sxs-lookup"><span data-stu-id="b5192-140">`UploadResult.cs`:</span></span>

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

<span data-ttu-id="b5192-141">**`Client`** 项目中的以下 `UploadFiles` 组件：</span><span class="sxs-lookup"><span data-stu-id="b5192-141">The following `UploadFiles` component in the **`Client`** project:</span></span>

* <span data-ttu-id="b5192-142">允许用户从客户端上传文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-142">Permits users to upload files from the client.</span></span>
* <span data-ttu-id="b5192-143">在用户界面中显示由客户端提供的不受信任/不安全的文件名，因为 Razor 自动对字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="b5192-143">Displays the untrusted/unsafe file name provided by the client in the UI because Razor automatically HTML-encodes strings.</span></span> <span data-ttu-id="b5192-144">**在其他情况下，不要信任客户端提供的文件名。**</span><span class="sxs-lookup"><span data-stu-id="b5192-144">**Don't trust file names supplied by clients in other scenarios.**</span></span>

<span data-ttu-id="b5192-145">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5192-145">`Pages/UploadFiles.razor`:</span></span>

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

<span data-ttu-id="b5192-146">要使用下面的代码，请在 **`Server`** 项目的根目录下创建一个 `Development/unsafe_uploads` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b5192-146">To use the following code, create a `Development/unsafe_uploads` folder at the root of the **`Server`** project.</span></span>

<span data-ttu-id="b5192-147">**`Server`** 项目中的以下 `FilesaveController` 控制器保存从客户端上传的文件。</span><span class="sxs-lookup"><span data-stu-id="b5192-147">The following `FilesaveController` controller in the **`Server`** project saves uploaded files from the client.</span></span>

> [!WARNING]
> <span data-ttu-id="b5192-148">该示例直接保存文件而没有扫描其内容。</span><span class="sxs-lookup"><span data-stu-id="b5192-148">The example saves files without scanning their contents.</span></span> <span data-ttu-id="b5192-149">在大多数生产情景中，会对文件中使用防病毒/反恶意软件扫描程序 API，然后文件才可供下载或供其他系统使用。</span><span class="sxs-lookup"><span data-stu-id="b5192-149">In most production scenarios, an anti-virus/anti-malware scanner API is used on files before making them available for download or for use by other systems.</span></span> <span data-ttu-id="b5192-150">有关将文件上传到服务器时的安全注意事项的更多信息，请参阅“<xref:mvc/models/file-uploads#security-considerations>”。</span><span class="sxs-lookup"><span data-stu-id="b5192-150">For more information on security considerations when uploading files to a server, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="b5192-151">`Controllers/FilesaveController.cs`:</span><span class="sxs-lookup"><span data-stu-id="b5192-151">`Controllers/FilesaveController.cs`:</span></span>

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

## <a name="file-streams"></a><span data-ttu-id="b5192-152">文件流</span><span class="sxs-lookup"><span data-stu-id="b5192-152">File streams</span></span>

<span data-ttu-id="b5192-153">在 Blazor WebAssembly 中，文件数据直接流式传入浏览器中的 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="b5192-153">In Blazor WebAssembly, file data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="b5192-154">在 Blazor Server 中，从流中读取文件时，文件数据通过 SignalR 连接流式传入服务器上的 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="b5192-154">In Blazor Server, file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="b5192-155">通过 <xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions>，可以配置 Blazor Server 的文件上传特性。</span><span class="sxs-lookup"><span data-stu-id="b5192-155"><xref:Microsoft.AspNetCore.Components.Forms.RemoteBrowserFileStreamOptions> allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5192-156">其他资源</span><span class="sxs-lookup"><span data-stu-id="b5192-156">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
