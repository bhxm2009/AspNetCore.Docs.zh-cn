---
title: 第 2 部分，添加模型
author: rick-anderson
description: Razor 页面教程系列的第 2 部分。 在此部分，将添加模型类。
ms.author: riande
ms.date: 03/10/2021
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: defbc73d0c1d6aac30360cd7b83cc518a407bf98
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413439"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="d38e1-104">第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="d38e1-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="d38e1-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d38e1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="d38e1-106">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="d38e1-107">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="d38e1-108">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="d38e1-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="d38e1-109">首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="d38e1-110">模型类称为 POCO 类（源自“简单传统 CLR 对象”   ），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="d38e1-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="d38e1-111">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="d38e1-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="d38e1-113">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="d38e1-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d38e1-115">在“解决方案资源管理器”中，右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d38e1-116">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="d38e1-117">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="d38e1-118">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="d38e1-119">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="d38e1-120">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-121">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-122">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-123">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-124">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-125">用户无需在日期字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d38e1-128">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="d38e1-129">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="d38e1-130">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-131">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-132">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-133">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-134">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-134">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-135">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-136">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="d38e1-137">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="d38e1-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d38e1-139">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="d38e1-140">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="d38e1-141">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d38e1-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="d38e1-142">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="d38e1-143">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="d38e1-144">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="d38e1-145">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-146">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-147">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-148">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-149">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-149">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-150">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-151">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="d38e1-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="d38e1-153">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="d38e1-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d38e1-154">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="d38e1-154">Scaffold the movie model</span></span>

<span data-ttu-id="d38e1-155">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="d38e1-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d38e1-156">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="d38e1-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d38e1-158">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="d38e1-159">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="d38e1-160">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="d38e1-161">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![上述说明的图像。](model/_static/5/sca.png)

1. <span data-ttu-id="d38e1-163">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="d38e1-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffold.png)

1. <span data-ttu-id="d38e1-165">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="d38e1-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="d38e1-166">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="d38e1-167">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="d38e1-168">在“添加数据上下文”对话框中，生成类名 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="d38e1-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="d38e1-169">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-169">Select **Add**.</span></span>

   ![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="d38e1-171">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="d38e1-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d38e1-173">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="d38e1-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="d38e1-174">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d38e1-175">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="d38e1-176">下表详细说明了 ASP.NET Core 代码生成器选项。</span><span class="sxs-lookup"><span data-stu-id="d38e1-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="d38e1-177">选项</span><span class="sxs-lookup"><span data-stu-id="d38e1-177">Option</span></span>               | <span data-ttu-id="d38e1-178">说明</span><span class="sxs-lookup"><span data-stu-id="d38e1-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="d38e1-179">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="d38e1-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="d38e1-180">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="d38e1-181">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="d38e1-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="d38e1-182">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="d38e1-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="d38e1-183">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="d38e1-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="d38e1-184">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="d38e1-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d38e1-185">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d38e1-186">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="d38e1-186">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d38e1-187">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="d38e1-187">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d38e1-188">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup` 中。</span><span class="sxs-lookup"><span data-stu-id="d38e1-188">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="d38e1-189">注入 `IWebHostEnvironment`，以便应用可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="d38e1-189">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d38e1-191">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-191">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="d38e1-192">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-192">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="d38e1-193">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-193">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="d38e1-194">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-194">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![上述说明的图像。](model/_static/scaMac.png)

1. <span data-ttu-id="d38e1-196">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="d38e1-196">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="d38e1-198">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="d38e1-198">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="d38e1-199">在“要使用的 DbContext 类:”行中，将类命名为 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="d38e1-199">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="d38e1-200">选择“完成”  。</span><span class="sxs-lookup"><span data-stu-id="d38e1-200">Select **Finish**.</span></span>

   ![上述说明的图像。](model/_static/5/arpMac.png)

<span data-ttu-id="d38e1-202">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="d38e1-202">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d38e1-203">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="d38e1-203">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d38e1-204">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="d38e1-204">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d38e1-205">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup` 中。</span><span class="sxs-lookup"><span data-stu-id="d38e1-205">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="d38e1-206">注入 `IWebHostEnvironment`，以便应用可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="d38e1-206">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="d38e1-207">创建的文件</span><span class="sxs-lookup"><span data-stu-id="d38e1-207">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-208">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-209">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-209">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d38e1-210">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-210">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d38e1-211">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="d38e1-211">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d38e1-212">已更新</span><span class="sxs-lookup"><span data-stu-id="d38e1-212">Updated</span></span>

* <span data-ttu-id="d38e1-213">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d38e1-213">*Startup.cs*</span></span>

<span data-ttu-id="d38e1-214">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-214">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-215">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d38e1-216">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-216">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="d38e1-217">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-217">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="d38e1-218">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-218">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-219">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d38e1-220">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d38e1-221">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d38e1-222">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="d38e1-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d38e1-223">已更新</span><span class="sxs-lookup"><span data-stu-id="d38e1-223">Updated</span></span>

* <span data-ttu-id="d38e1-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d38e1-224">*Startup.cs*</span></span>

<span data-ttu-id="d38e1-225">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-225">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="d38e1-226">使用 EF 的迁移功能创建初始数据库架构</span><span class="sxs-lookup"><span data-stu-id="d38e1-226">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="d38e1-227">Entity Framework Core 中的迁移功能提供了一种方法来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d38e1-227">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="d38e1-228">创建初始数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d38e1-228">Create the initial database schema.</span></span>
* <span data-ttu-id="d38e1-229">以增量的方式更新数据库架构，使其与应用程序的数据模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="d38e1-229">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="d38e1-230">保存数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="d38e1-230">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-232">在此部分中，程序包管理器控制台 (PMC) 窗口用于：</span><span class="sxs-lookup"><span data-stu-id="d38e1-232">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="d38e1-233">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="d38e1-233">Add an initial migration.</span></span>
* <span data-ttu-id="d38e1-234">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-234">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="d38e1-235">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-235">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 菜单](model/_static/5/pmc.png)

1. <span data-ttu-id="d38e1-237">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-237">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-238">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="d38e1-239">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-239">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="d38e1-240">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="d38e1-240">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d38e1-241">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="d38e1-241">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d38e1-242">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="d38e1-242">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d38e1-243">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="d38e1-243">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="d38e1-244">`migrations` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="d38e1-244">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d38e1-245">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="d38e1-245">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="d38e1-246">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="d38e1-246">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="d38e1-247">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="d38e1-247">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="d38e1-248">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-248">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="d38e1-249">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-249">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-250">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d38e1-251">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="d38e1-251">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d38e1-252">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="d38e1-252">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d38e1-253">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-253">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d38e1-254">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="d38e1-254">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d38e1-255">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="d38e1-255">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d38e1-256">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="d38e1-256">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d38e1-257">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-257">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d38e1-258">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="d38e1-258">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d38e1-259">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-259">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="d38e1-260">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-260">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="d38e1-261">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="d38e1-261">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d38e1-262">前面的代码为实体集创建 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-262">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="d38e1-263">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="d38e1-263">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d38e1-264">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="d38e1-264">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d38e1-265">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="d38e1-265">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="d38e1-266">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d38e1-266">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-267">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-267">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d38e1-268">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-268">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="d38e1-269">测试应用</span><span class="sxs-lookup"><span data-stu-id="d38e1-269">Test the app</span></span>

1. <span data-ttu-id="d38e1-270">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-270">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="d38e1-271">如果收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="d38e1-271">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="d38e1-272">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-272">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="d38e1-273">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="d38e1-273">Test the **Create** link.</span></span>

   ![创建页面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="d38e1-275">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="d38e1-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d38e1-276">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="d38e1-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d38e1-277">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-277">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="d38e1-278">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="d38e1-278">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d38e1-279">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="d38e1-279">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d38e1-280">其他资源</span><span class="sxs-lookup"><span data-stu-id="d38e1-280">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d38e1-281">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="d38e1-281">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="d38e1-282">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="d38e1-282">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="d38e1-283">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-283">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="d38e1-284">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="d38e1-284">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="d38e1-285">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="d38e1-285">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d38e1-286">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-286">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="d38e1-287">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-287">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="d38e1-288">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="d38e1-288">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-289">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-289">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-290">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="d38e1-290">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d38e1-291">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-291">Name the folder *Models*.</span></span>

<span data-ttu-id="d38e1-292">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-292">Right-click the *Models* folder.</span></span> <span data-ttu-id="d38e1-293">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-293">Select **Add** > **Class**.</span></span> <span data-ttu-id="d38e1-294">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-294">Name the class **Movie**.</span></span>

<span data-ttu-id="d38e1-295">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-295">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-296">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-296">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-297">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-297">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-298">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-298">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-299">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-299">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-300">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-300">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-301">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-301">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d38e1-302">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-302">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-303">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-303">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d38e1-304">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-304">Add a folder named *Models*.</span></span>
* <span data-ttu-id="d38e1-305">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-305">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="d38e1-306">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-307">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-308">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-309">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-310">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-310">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-311">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-312">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d38e1-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="d38e1-314">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="d38e1-314">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="d38e1-315">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="d38e1-315">Add a database context class</span></span>

* <span data-ttu-id="d38e1-316">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-316">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="d38e1-317">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-317">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d38e1-318">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-318">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="d38e1-319">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="d38e1-319">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="d38e1-320">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="d38e1-320">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="d38e1-321">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="d38e1-321">Add a database connection string</span></span>

<span data-ttu-id="d38e1-322">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="d38e1-322">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="d38e1-323">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="d38e1-323">Register the database context</span></span>

<span data-ttu-id="d38e1-324">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="d38e1-324">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d38e1-325">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="d38e1-325">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-326">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d38e1-327">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-327">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="d38e1-328">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-328">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="d38e1-329">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d38e1-329">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d38e1-330">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-330">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="d38e1-331">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-331">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="d38e1-332">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-332">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="d38e1-333">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-333">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-334">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-334">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-335">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-335">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-336">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-336">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-337">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-337">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-338">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-338">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-339">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-339">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="d38e1-340">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-340">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="d38e1-341">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="d38e1-341">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d38e1-342">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="d38e1-342">Scaffold the movie model</span></span>

<span data-ttu-id="d38e1-343">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="d38e1-343">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d38e1-344">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="d38e1-344">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-345">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-345">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-346">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-346">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d38e1-347">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-347">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d38e1-348">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-348">Name the folder *Movies*.</span></span>

<span data-ttu-id="d38e1-349">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-349">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="d38e1-351">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="d38e1-351">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="d38e1-353">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="d38e1-353">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d38e1-354">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-354">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d38e1-355">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="d38e1-355">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="d38e1-356">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-356">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="d38e1-357">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-357">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="d38e1-358">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-358">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="d38e1-360">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="d38e1-360">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-361">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-361">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d38e1-362">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="d38e1-362">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="d38e1-363">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-363">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d38e1-364">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-364">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="d38e1-365">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="d38e1-365">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="d38e1-366">选项</span><span class="sxs-lookup"><span data-stu-id="d38e1-366">Option</span></span>               | <span data-ttu-id="d38e1-367">说明</span><span class="sxs-lookup"><span data-stu-id="d38e1-367">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="d38e1-368">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="d38e1-368">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="d38e1-369">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-369">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="d38e1-370">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="d38e1-370">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="d38e1-371">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="d38e1-371">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="d38e1-372">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="d38e1-372">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="d38e1-373">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="d38e1-373">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d38e1-374">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-374">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d38e1-375">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="d38e1-375">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d38e1-376">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="d38e1-376">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d38e1-377">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="d38e1-377">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d38e1-378">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="d38e1-378">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-379">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-379">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d38e1-380">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-380">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d38e1-381">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-381">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d38e1-382">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-382">Name the folder *Movies*.</span></span>

<span data-ttu-id="d38e1-383">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-383">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="d38e1-385">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="d38e1-385">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="d38e1-387">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="d38e1-387">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d38e1-388">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-388">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d38e1-389">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="d38e1-389">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="d38e1-390">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-390">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="d38e1-391">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-391">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="d38e1-392">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-392">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="d38e1-394">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="d38e1-394">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="d38e1-395">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="d38e1-395">Add EF tools</span></span>

<span data-ttu-id="d38e1-396">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-396">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="d38e1-397">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="d38e1-397">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="d38e1-398">有关详细信息，请参阅 [Entity Framework Core 工具参考 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-398">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d38e1-399">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="d38e1-399">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d38e1-400">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="d38e1-400">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d38e1-401">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="d38e1-401">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d38e1-402">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="d38e1-402">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="d38e1-403">创建的文件</span><span class="sxs-lookup"><span data-stu-id="d38e1-403">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-405">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-405">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d38e1-406">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-406">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d38e1-407">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="d38e1-407">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d38e1-408">已更新</span><span class="sxs-lookup"><span data-stu-id="d38e1-408">Updated</span></span>

* <span data-ttu-id="d38e1-409">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d38e1-409">*Startup.cs*</span></span>

<span data-ttu-id="d38e1-410">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-410">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-411">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-411">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d38e1-412">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-412">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d38e1-413">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-413">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d38e1-414">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="d38e1-414">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d38e1-415">已更新</span><span class="sxs-lookup"><span data-stu-id="d38e1-415">Updated</span></span>

* <span data-ttu-id="d38e1-416">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d38e1-416">*Startup.cs*</span></span>

<span data-ttu-id="d38e1-417">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-417">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-418">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-418">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d38e1-419">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-419">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="d38e1-420">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-420">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="d38e1-421">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-421">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d38e1-422">初始迁移</span><span class="sxs-lookup"><span data-stu-id="d38e1-422">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-424">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="d38e1-424">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="d38e1-425">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="d38e1-425">Add an initial migration.</span></span>
* <span data-ttu-id="d38e1-426">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-426">Update the database with the initial migration.</span></span>

<span data-ttu-id="d38e1-427">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-427">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d38e1-429">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-429">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-430">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-430">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d38e1-431">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-431">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="d38e1-432">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="d38e1-432">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d38e1-433">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="d38e1-433">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d38e1-434">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="d38e1-434">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d38e1-435">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="d38e1-435">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="d38e1-436">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="d38e1-436">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="d38e1-437">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="d38e1-437">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="d38e1-438">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="d38e1-438">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="d38e1-439">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="d38e1-439">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="d38e1-440">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-440">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="d38e1-441">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-441">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-442">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d38e1-443">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="d38e1-443">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d38e1-444">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="d38e1-444">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d38e1-445">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-445">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d38e1-446">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="d38e1-446">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="d38e1-447">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="d38e1-447">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d38e1-448">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="d38e1-448">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d38e1-449">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-449">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d38e1-450">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="d38e1-450">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d38e1-451">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-451">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="d38e1-452">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-452">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="d38e1-453">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="d38e1-453">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d38e1-454">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-454">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d38e1-455">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="d38e1-455">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d38e1-456">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="d38e1-456">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d38e1-457">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="d38e1-457">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="d38e1-458">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d38e1-458">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-459">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-459">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d38e1-460">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-460">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d38e1-461">测试应用</span><span class="sxs-lookup"><span data-stu-id="d38e1-461">Test the app</span></span>

* <span data-ttu-id="d38e1-462">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-462">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d38e1-463">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="d38e1-463">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d38e1-464">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-464">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d38e1-465">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="d38e1-465">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="d38e1-467">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="d38e1-467">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d38e1-468">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="d38e1-468">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d38e1-469">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-469">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d38e1-470">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="d38e1-470">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d38e1-471">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="d38e1-471">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d38e1-472">其他资源</span><span class="sxs-lookup"><span data-stu-id="d38e1-472">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d38e1-473">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="d38e1-473">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d38e1-474">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-474">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="d38e1-475">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-475">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="d38e1-476">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-476">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="d38e1-477">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="d38e1-477">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="d38e1-478">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="d38e1-478">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d38e1-479">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-479">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="d38e1-480">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-480">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="d38e1-481">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="d38e1-481">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-482">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-482">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-483">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="d38e1-483">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d38e1-484">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-484">Name the folder *Models*.</span></span>

<span data-ttu-id="d38e1-485">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-485">Right-click the *Models* folder.</span></span> <span data-ttu-id="d38e1-486">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-486">Select **Add** > **Class**.</span></span> <span data-ttu-id="d38e1-487">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-487">Name the class **Movie**.</span></span>

<span data-ttu-id="d38e1-488">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-488">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-489">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-489">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-490">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-490">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-491">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-491">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-492">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-492">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-493">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-493">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-494">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-494">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d38e1-495">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-495">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d38e1-497">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-497">Add a folder named *Models*.</span></span>
* <span data-ttu-id="d38e1-498">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-498">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="d38e1-499">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-500">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-501">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-502">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-503">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-503">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-504">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-505">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d38e1-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="d38e1-507">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="d38e1-507">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="d38e1-508">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="d38e1-508">Add a database context class</span></span>

<span data-ttu-id="d38e1-509">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="d38e1-509">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="d38e1-510">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-510">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d38e1-511">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-511">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="d38e1-512">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="d38e1-512">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="d38e1-513">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="d38e1-513">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="d38e1-514">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="d38e1-514">Add a database connection string</span></span>

<span data-ttu-id="d38e1-515">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="d38e1-515">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="d38e1-516">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="d38e1-516">Add required NuGet packages</span></span>

<span data-ttu-id="d38e1-517">运行以下 .NET Core CLI 命令，将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="d38e1-517">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="d38e1-518">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="d38e1-518">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="d38e1-519">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="d38e1-519">Register the database context</span></span>

<span data-ttu-id="d38e1-520">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="d38e1-520">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d38e1-521">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="d38e1-521">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="d38e1-522">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="d38e1-522">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-523">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-523">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d38e1-524">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-524">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="d38e1-525">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-525">Name the folder *Models*.</span></span>
* <span data-ttu-id="d38e1-526">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-526">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="d38e1-527">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d38e1-527">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d38e1-528">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-528">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="d38e1-529">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-529">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="d38e1-530">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-530">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="d38e1-531">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-531">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d38e1-532">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="d38e1-532">The `Movie` class contains:</span></span>

* <span data-ttu-id="d38e1-533">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="d38e1-533">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d38e1-534">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-534">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d38e1-535">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="d38e1-535">With this attribute:</span></span>

  * <span data-ttu-id="d38e1-536">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-536">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d38e1-537">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="d38e1-537">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d38e1-538">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="d38e1-538">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="d38e1-539">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="d38e1-539">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d38e1-540">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="d38e1-540">Scaffold the movie model</span></span>

<span data-ttu-id="d38e1-541">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="d38e1-541">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d38e1-542">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="d38e1-542">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-543">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-544">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-544">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d38e1-545">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-545">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d38e1-546">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-546">Name the folder *Movies*.</span></span>

<span data-ttu-id="d38e1-547">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-547">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="d38e1-549">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="d38e1-549">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="d38e1-551">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="d38e1-551">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="d38e1-552">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-552">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d38e1-553">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="d38e1-553">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d38e1-554">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-554">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="d38e1-556">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="d38e1-556">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d38e1-557">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d38e1-557">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d38e1-558">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="d38e1-558">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="d38e1-559">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-559">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d38e1-560">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-560">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="d38e1-561">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="d38e1-561">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="d38e1-562">选项</span><span class="sxs-lookup"><span data-stu-id="d38e1-562">Option</span></span>               | <span data-ttu-id="d38e1-563">说明</span><span class="sxs-lookup"><span data-stu-id="d38e1-563">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="d38e1-564">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="d38e1-564">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="d38e1-565">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-565">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="d38e1-566">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="d38e1-566">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="d38e1-567">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="d38e1-567">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="d38e1-568">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="d38e1-568">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="d38e1-569">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="d38e1-569">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d38e1-570">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-570">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-571">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-571">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d38e1-572">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="d38e1-572">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d38e1-573">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-573">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d38e1-574">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-574">Name the folder *Movies*.</span></span>

<span data-ttu-id="d38e1-575">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-575">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="d38e1-577">在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="d38e1-577">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="d38e1-579">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="d38e1-579">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d38e1-580">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-580">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="d38e1-581">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="d38e1-581">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="d38e1-582">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-582">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d38e1-583">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-583">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="d38e1-585">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="d38e1-585">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="d38e1-586">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="d38e1-586">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="d38e1-587">创建的文件</span><span class="sxs-lookup"><span data-stu-id="d38e1-587">Files created</span></span>

* <span data-ttu-id="d38e1-588">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="d38e1-588">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d38e1-589">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="d38e1-589">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="d38e1-590">文件已更新</span><span class="sxs-lookup"><span data-stu-id="d38e1-590">File updated</span></span>

* <span data-ttu-id="d38e1-591">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d38e1-591">*Startup.cs*</span></span>

<span data-ttu-id="d38e1-592">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="d38e1-592">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d38e1-593">初始迁移</span><span class="sxs-lookup"><span data-stu-id="d38e1-593">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-594">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-594">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d38e1-595">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="d38e1-595">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="d38e1-596">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="d38e1-596">Add an initial migration.</span></span>
* <span data-ttu-id="d38e1-597">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-597">Update the database with the initial migration.</span></span>

<span data-ttu-id="d38e1-598">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="d38e1-598">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d38e1-600">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-600">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="d38e1-601">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="d38e1-601">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d38e1-602">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-602">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="d38e1-603">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="d38e1-603">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="d38e1-604">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="d38e1-604">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="d38e1-605">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="d38e1-605">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="d38e1-606">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-606">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="d38e1-607">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d38e1-607">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-608">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-608">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d38e1-609">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d38e1-609">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="d38e1-610">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="d38e1-610">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d38e1-611">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="d38e1-611">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d38e1-612">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="d38e1-612">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d38e1-613">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="d38e1-613">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d38e1-614">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d38e1-614">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d38e1-615">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="d38e1-615">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d38e1-616">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="d38e1-616">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d38e1-617">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="d38e1-617">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d38e1-618">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="d38e1-618">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d38e1-619">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="d38e1-619">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d38e1-620">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="d38e1-620">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d38e1-621">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-621">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d38e1-622">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="d38e1-622">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="d38e1-623">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="d38e1-623">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="d38e1-624">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-624">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="d38e1-625">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="d38e1-625">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d38e1-626">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="d38e1-626">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d38e1-627">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="d38e1-627">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d38e1-628">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="d38e1-628">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d38e1-629">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="d38e1-629">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="d38e1-630">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d38e1-630">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d38e1-631">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d38e1-631">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d38e1-632">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="d38e1-632">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d38e1-633">测试应用</span><span class="sxs-lookup"><span data-stu-id="d38e1-633">Test the app</span></span>

* <span data-ttu-id="d38e1-634">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-634">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d38e1-635">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="d38e1-635">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d38e1-636">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-636">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d38e1-637">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="d38e1-637">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="d38e1-639">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="d38e1-639">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d38e1-640">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="d38e1-640">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d38e1-641">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="d38e1-641">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d38e1-642">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="d38e1-642">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d38e1-643">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="d38e1-643">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d38e1-644">其他资源</span><span class="sxs-lookup"><span data-stu-id="d38e1-644">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d38e1-645">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="d38e1-645">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
