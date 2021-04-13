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
ms.openlocfilehash: 28d1a99e57a07a8d63cbbb7f0ecc7f84d1adf446
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564297"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="06035-104">第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="06035-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="06035-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="06035-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="06035-106">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="06035-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="06035-107">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="06035-108">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="06035-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="06035-109">首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="06035-110">模型类称为 POCO 类（源自“简单传统 CLR 对象”   ），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="06035-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="06035-111">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="06035-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="06035-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="06035-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="06035-113">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="06035-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="06035-115">在“解决方案资源管理器”中，右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="06035-116">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="06035-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="06035-117">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="06035-118">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="06035-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="06035-119">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="06035-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="06035-120">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-121">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-122">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-123">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-124">With this attribute:</span></span>

  * <span data-ttu-id="06035-125">用户无需在日期字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="06035-128">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="06035-129">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="06035-130">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-131">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-132">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-133">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-134">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-134">With this attribute:</span></span>

  * <span data-ttu-id="06035-135">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-136">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="06035-137">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="06035-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="06035-139">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="06035-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="06035-140">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="06035-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="06035-141">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="06035-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="06035-142">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="06035-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="06035-143">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="06035-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="06035-144">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="06035-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="06035-145">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-146">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-147">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-148">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-149">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-149">With this attribute:</span></span>

  * <span data-ttu-id="06035-150">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-151">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="06035-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="06035-153">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="06035-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="06035-154">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="06035-154">Scaffold the movie model</span></span>

<span data-ttu-id="06035-155">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="06035-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="06035-156">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="06035-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="06035-158">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="06035-159">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="06035-160">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="06035-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="06035-161">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="06035-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![上述说明的图像。](model/_static/5/sca.png)

1. <span data-ttu-id="06035-163">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="06035-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffold.png)

1. <span data-ttu-id="06035-165">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="06035-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="06035-166">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="06035-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="06035-167">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="06035-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="06035-168">在“添加数据上下文”对话框中，生成类名 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="06035-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="06035-169">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="06035-169">Select **Add**.</span></span>

   ![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="06035-171">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="06035-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="06035-173">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="06035-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="06035-174">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="06035-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="06035-175">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="06035-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="06035-176">下表详细说明了 ASP.NET Core 代码生成器选项。</span><span class="sxs-lookup"><span data-stu-id="06035-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="06035-177">选项</span><span class="sxs-lookup"><span data-stu-id="06035-177">Option</span></span>               | <span data-ttu-id="06035-178">说明</span><span class="sxs-lookup"><span data-stu-id="06035-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="06035-179">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="06035-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="06035-180">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="06035-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="06035-181">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="06035-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="06035-182">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="06035-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="06035-183">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="06035-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="06035-184">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="06035-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="06035-185">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="06035-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="06035-187">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-187">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="06035-188">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-188">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="06035-189">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="06035-189">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="06035-190">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="06035-190">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![上述说明的图像。](model/_static/scaMac.png)

1. <span data-ttu-id="06035-192">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="06035-192">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="06035-194">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="06035-194">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="06035-195">在“要使用的 DbContext 类:”行中，将类命名为 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="06035-195">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="06035-196">选择“完成”  。</span><span class="sxs-lookup"><span data-stu-id="06035-196">Select **Finish**.</span></span>

   ![上述说明的图像。](model/_static/5/arpMac.png)

<span data-ttu-id="06035-198">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="06035-198">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="06035-199">创建的文件</span><span class="sxs-lookup"><span data-stu-id="06035-199">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-200">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-201">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-201">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="06035-202">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-202">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="06035-203">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="06035-203">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="06035-204">已更新</span><span class="sxs-lookup"><span data-stu-id="06035-204">Updated</span></span>

* <span data-ttu-id="06035-205">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="06035-205">*Startup.cs*</span></span>

<span data-ttu-id="06035-206">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-206">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="06035-208">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-208">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="06035-209">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-209">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="06035-210">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-210">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="06035-212">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-212">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="06035-213">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-213">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="06035-214">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="06035-214">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="06035-215">已更新</span><span class="sxs-lookup"><span data-stu-id="06035-215">Updated</span></span>

* <span data-ttu-id="06035-216">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="06035-216">*Startup.cs*</span></span>

<span data-ttu-id="06035-217">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-217">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="06035-218">使用 EF 的迁移功能创建初始数据库架构</span><span class="sxs-lookup"><span data-stu-id="06035-218">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="06035-219">Entity Framework Core 中的迁移功能提供了一种方法来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="06035-219">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="06035-220">创建初始数据库架构。</span><span class="sxs-lookup"><span data-stu-id="06035-220">Create the initial database schema.</span></span>
* <span data-ttu-id="06035-221">以增量的方式更新数据库架构，使其与应用程序的数据模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="06035-221">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="06035-222">保存数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="06035-222">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-223">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-224">在此部分中，程序包管理器控制台 (PMC) 窗口用于：</span><span class="sxs-lookup"><span data-stu-id="06035-224">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="06035-225">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="06035-225">Add an initial migration.</span></span>
* <span data-ttu-id="06035-226">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-226">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="06035-227">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="06035-227">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 菜单](model/_static/5/pmc.png)

1. <span data-ttu-id="06035-229">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="06035-229">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="06035-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="06035-231">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="06035-231">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="06035-232">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="06035-232">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="06035-233">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="06035-233">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="06035-234">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="06035-234">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="06035-235">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="06035-235">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="06035-236">`migrations` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="06035-236">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="06035-237">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="06035-237">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="06035-238">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="06035-238">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="06035-239">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="06035-239">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="06035-240">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-240">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="06035-241">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-241">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-242">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="06035-243">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="06035-243">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="06035-244">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="06035-244">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="06035-245">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="06035-245">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="06035-246">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="06035-246">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="06035-247">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="06035-247">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="06035-248">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="06035-248">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="06035-249">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-249">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="06035-250">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="06035-250">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="06035-251">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="06035-251">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="06035-252">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="06035-252">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="06035-253">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="06035-253">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="06035-254">前面的代码为实体集创建 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 属性。</span><span class="sxs-lookup"><span data-stu-id="06035-254">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="06035-255">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="06035-255">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="06035-256">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="06035-256">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="06035-257">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="06035-257">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="06035-258">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="06035-258">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="06035-259">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="06035-260">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-260">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="06035-261">测试应用</span><span class="sxs-lookup"><span data-stu-id="06035-261">Test the app</span></span>

1. <span data-ttu-id="06035-262">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="06035-262">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="06035-263">如果收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="06035-263">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="06035-264">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="06035-264">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="06035-265">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="06035-265">Test the **Create** link.</span></span>

   ![创建页面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="06035-267">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="06035-267">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="06035-268">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="06035-268">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="06035-269">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="06035-269">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="06035-270">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="06035-270">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="06035-271">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="06035-271">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06035-272">其他资源</span><span class="sxs-lookup"><span data-stu-id="06035-272">Additional resources</span></span>


> [!div class="step-by-step"]
> <span data-ttu-id="06035-273">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="06035-273">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="06035-274">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="06035-274">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="06035-275">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-275">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="06035-276">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="06035-276">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="06035-277">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="06035-277">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="06035-278">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="06035-278">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="06035-279">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="06035-279">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="06035-280">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="06035-280">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-282">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="06035-282">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="06035-283">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="06035-283">Name the folder *Models*.</span></span>

<span data-ttu-id="06035-284">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-284">Right-click the *Models* folder.</span></span> <span data-ttu-id="06035-285">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="06035-285">Select **Add** > **Class**.</span></span> <span data-ttu-id="06035-286">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="06035-286">Name the class **Movie**.</span></span>

<span data-ttu-id="06035-287">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-287">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-288">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-288">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-289">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-289">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-290">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-290">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-291">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-291">With this attribute:</span></span>

  * <span data-ttu-id="06035-292">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-292">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-293">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-293">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="06035-294">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-294">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="06035-296">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-296">Add a folder named *Models*.</span></span>
* <span data-ttu-id="06035-297">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-297">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="06035-298">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-298">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-299">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-299">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-300">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-300">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-301">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-301">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-302">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-302">With this attribute:</span></span>

  * <span data-ttu-id="06035-303">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-303">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-304">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-304">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="06035-305">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-305">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="06035-306">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="06035-306">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="06035-307">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="06035-307">Add a database context class</span></span>

* <span data-ttu-id="06035-308">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-308">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="06035-309">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-309">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="06035-310">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="06035-310">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="06035-311">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="06035-311">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="06035-312">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="06035-312">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="06035-313">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="06035-313">Add a database connection string</span></span>

<span data-ttu-id="06035-314">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="06035-314">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="06035-315">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="06035-315">Register the database context</span></span>

<span data-ttu-id="06035-316">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="06035-316">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="06035-317">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="06035-317">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-318">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="06035-319">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="06035-319">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="06035-320">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="06035-320">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="06035-321">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="06035-321">In the **New File** dialog:</span></span>

  * <span data-ttu-id="06035-322">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="06035-322">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="06035-323">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="06035-323">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="06035-324">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="06035-324">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="06035-325">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-325">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-326">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-326">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-327">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-327">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-328">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-328">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-329">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-329">With this attribute:</span></span>

  * <span data-ttu-id="06035-330">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-330">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-331">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-331">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="06035-332">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-332">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="06035-333">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="06035-333">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="06035-334">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="06035-334">Scaffold the movie model</span></span>

<span data-ttu-id="06035-335">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="06035-335">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="06035-336">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="06035-336">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-337">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-337">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-338">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-338">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="06035-339">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-339">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="06035-340">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="06035-340">Name the folder *Movies*.</span></span>

<span data-ttu-id="06035-341">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="06035-341">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="06035-343">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="06035-343">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="06035-345">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="06035-345">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="06035-346">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="06035-346">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="06035-347">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="06035-347">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="06035-348">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="06035-348">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="06035-349">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="06035-349">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="06035-350">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="06035-350">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="06035-352">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="06035-352">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-353">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-353">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="06035-354">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="06035-354">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="06035-355">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="06035-355">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="06035-356">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="06035-356">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="06035-357">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="06035-357">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="06035-358">选项</span><span class="sxs-lookup"><span data-stu-id="06035-358">Option</span></span>               | <span data-ttu-id="06035-359">说明</span><span class="sxs-lookup"><span data-stu-id="06035-359">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="06035-360">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="06035-360">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="06035-361">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="06035-361">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="06035-362">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="06035-362">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="06035-363">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="06035-363">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="06035-364">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="06035-364">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="06035-365">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="06035-365">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="06035-366">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="06035-366">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="06035-367">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="06035-367">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="06035-368">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="06035-368">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="06035-369">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="06035-369">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="06035-370">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="06035-370">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-371">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="06035-372">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-372">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="06035-373">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-373">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="06035-374">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="06035-374">Name the folder *Movies*.</span></span>

<span data-ttu-id="06035-375">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="06035-375">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="06035-377">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="06035-377">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="06035-379">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="06035-379">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="06035-380">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="06035-380">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="06035-381">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="06035-381">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="06035-382">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="06035-382">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="06035-383">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="06035-383">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="06035-384">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="06035-384">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="06035-386">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="06035-386">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="06035-387">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="06035-387">Add EF tools</span></span>

<span data-ttu-id="06035-388">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="06035-388">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="06035-389">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="06035-389">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="06035-390">有关详细信息，请参阅 [Entity Framework Core 工具参考 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="06035-390">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="06035-391">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="06035-391">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="06035-392">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="06035-392">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="06035-393">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="06035-393">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="06035-394">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="06035-394">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="06035-395">创建的文件</span><span class="sxs-lookup"><span data-stu-id="06035-395">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-396">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-397">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-397">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="06035-398">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-398">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="06035-399">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="06035-399">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="06035-400">已更新</span><span class="sxs-lookup"><span data-stu-id="06035-400">Updated</span></span>

* <span data-ttu-id="06035-401">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="06035-401">*Startup.cs*</span></span>

<span data-ttu-id="06035-402">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-402">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-403">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="06035-404">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-404">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="06035-405">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-405">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="06035-406">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="06035-406">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="06035-407">已更新</span><span class="sxs-lookup"><span data-stu-id="06035-407">Updated</span></span>

* <span data-ttu-id="06035-408">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="06035-408">*Startup.cs*</span></span>

<span data-ttu-id="06035-409">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-409">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="06035-411">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-411">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="06035-412">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-412">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="06035-413">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-413">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="06035-414">初始迁移</span><span class="sxs-lookup"><span data-stu-id="06035-414">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-416">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="06035-416">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="06035-417">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="06035-417">Add an initial migration.</span></span>
* <span data-ttu-id="06035-418">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-418">Update the database with the initial migration.</span></span>

<span data-ttu-id="06035-419">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="06035-419">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="06035-421">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="06035-421">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="06035-422">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-422">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="06035-423">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="06035-423">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="06035-424">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="06035-424">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="06035-425">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="06035-425">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="06035-426">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="06035-426">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="06035-427">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="06035-427">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="06035-428">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="06035-428">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="06035-429">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="06035-429">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="06035-430">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="06035-430">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="06035-431">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="06035-431">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="06035-432">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-432">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="06035-433">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-433">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-434">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="06035-435">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="06035-435">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="06035-436">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="06035-436">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="06035-437">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="06035-437">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="06035-438">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="06035-438">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="06035-439">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="06035-439">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="06035-440">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="06035-440">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="06035-441">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-441">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="06035-442">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="06035-442">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="06035-443">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="06035-443">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="06035-444">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="06035-444">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="06035-445">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="06035-445">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="06035-446">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="06035-446">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="06035-447">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="06035-447">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="06035-448">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="06035-448">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="06035-449">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="06035-449">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="06035-450">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="06035-450">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="06035-451">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-451">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="06035-452">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-452">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="06035-453">测试应用</span><span class="sxs-lookup"><span data-stu-id="06035-453">Test the app</span></span>

* <span data-ttu-id="06035-454">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="06035-454">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="06035-455">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="06035-455">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="06035-456">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="06035-456">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="06035-457">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="06035-457">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="06035-459">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="06035-459">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="06035-460">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="06035-460">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="06035-461">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="06035-461">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="06035-462">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="06035-462">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="06035-463">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="06035-463">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06035-464">其他资源</span><span class="sxs-lookup"><span data-stu-id="06035-464">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="06035-465">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="06035-465">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06035-466">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="06035-466">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="06035-467">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-467">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="06035-468">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-468">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="06035-469">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="06035-469">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="06035-470">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="06035-470">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="06035-471">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="06035-471">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="06035-472">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="06035-472">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="06035-473">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="06035-473">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-474">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-474">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-475">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="06035-475">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="06035-476">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="06035-476">Name the folder *Models*.</span></span>

<span data-ttu-id="06035-477">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-477">Right-click the *Models* folder.</span></span> <span data-ttu-id="06035-478">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="06035-478">Select **Add** > **Class**.</span></span> <span data-ttu-id="06035-479">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="06035-479">Name the class **Movie**.</span></span>

<span data-ttu-id="06035-480">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-480">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-481">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-481">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-482">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-482">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-483">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-483">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-484">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-484">With this attribute:</span></span>

  * <span data-ttu-id="06035-485">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-485">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-486">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-486">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="06035-487">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-487">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-488">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-488">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="06035-489">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-489">Add a folder named *Models*.</span></span>
* <span data-ttu-id="06035-490">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-490">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="06035-491">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-491">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-492">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-492">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-493">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-493">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-494">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-494">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-495">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-495">With this attribute:</span></span>

  * <span data-ttu-id="06035-496">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-496">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-497">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-497">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="06035-498">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-498">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="06035-499">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="06035-499">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="06035-500">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="06035-500">Add a database context class</span></span>

<span data-ttu-id="06035-501">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="06035-501">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="06035-502">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-502">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="06035-503">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="06035-503">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="06035-504">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="06035-504">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="06035-505">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="06035-505">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="06035-506">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="06035-506">Add a database connection string</span></span>

<span data-ttu-id="06035-507">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="06035-507">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="06035-508">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="06035-508">Add required NuGet packages</span></span>

<span data-ttu-id="06035-509">运行以下 .NET Core CLI 命令，将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="06035-509">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="06035-510">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="06035-510">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="06035-511">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="06035-511">Register the database context</span></span>

<span data-ttu-id="06035-512">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="06035-512">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="06035-513">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="06035-513">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="06035-514">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="06035-514">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-515">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-515">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="06035-516">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-516">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="06035-517">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="06035-517">Name the folder *Models*.</span></span>
* <span data-ttu-id="06035-518">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="06035-518">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="06035-519">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="06035-519">In the **New File** dialog:</span></span>

  * <span data-ttu-id="06035-520">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="06035-520">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="06035-521">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="06035-521">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="06035-522">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="06035-522">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="06035-523">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="06035-523">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="06035-524">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="06035-524">The `Movie` class contains:</span></span>

* <span data-ttu-id="06035-525">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="06035-525">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="06035-526">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="06035-526">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="06035-527">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="06035-527">With this attribute:</span></span>

  * <span data-ttu-id="06035-528">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-528">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="06035-529">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="06035-529">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="06035-530">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="06035-530">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="06035-531">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="06035-531">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="06035-532">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="06035-532">Scaffold the movie model</span></span>

<span data-ttu-id="06035-533">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="06035-533">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="06035-534">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="06035-534">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-535">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-535">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-536">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-536">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="06035-537">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-537">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="06035-538">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="06035-538">Name the folder *Movies*.</span></span>

<span data-ttu-id="06035-539">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="06035-539">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="06035-541">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="06035-541">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="06035-543">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="06035-543">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="06035-544">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="06035-544">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="06035-545">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="06035-545">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="06035-546">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="06035-546">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="06035-548">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="06035-548">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="06035-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="06035-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="06035-550">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="06035-550">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="06035-551">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="06035-551">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="06035-552">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="06035-552">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="06035-553">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="06035-553">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="06035-554">选项</span><span class="sxs-lookup"><span data-stu-id="06035-554">Option</span></span>               | <span data-ttu-id="06035-555">说明</span><span class="sxs-lookup"><span data-stu-id="06035-555">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="06035-556">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="06035-556">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="06035-557">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="06035-557">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="06035-558">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="06035-558">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="06035-559">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="06035-559">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="06035-560">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="06035-560">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="06035-561">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="06035-561">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="06035-562">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="06035-562">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="06035-563">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-563">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="06035-564">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="06035-564">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="06035-565">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="06035-565">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="06035-566">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="06035-566">Name the folder *Movies*.</span></span>

<span data-ttu-id="06035-567">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="06035-567">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="06035-569">在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="06035-569">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="06035-571">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="06035-571">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="06035-572">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="06035-572">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="06035-573">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="06035-573">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="06035-574">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="06035-574">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="06035-575">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="06035-575">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="06035-577">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="06035-577">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="06035-578">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="06035-578">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="06035-579">创建的文件</span><span class="sxs-lookup"><span data-stu-id="06035-579">Files created</span></span>

* <span data-ttu-id="06035-580">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="06035-580">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="06035-581">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="06035-581">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="06035-582">文件已更新</span><span class="sxs-lookup"><span data-stu-id="06035-582">File updated</span></span>

* <span data-ttu-id="06035-583">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="06035-583">*Startup.cs*</span></span>

<span data-ttu-id="06035-584">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="06035-584">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="06035-585">初始迁移</span><span class="sxs-lookup"><span data-stu-id="06035-585">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-586">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06035-587">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="06035-587">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="06035-588">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="06035-588">Add an initial migration.</span></span>
* <span data-ttu-id="06035-589">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-589">Update the database with the initial migration.</span></span>

<span data-ttu-id="06035-590">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="06035-590">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="06035-592">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="06035-592">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="06035-593">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="06035-593">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="06035-594">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="06035-594">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="06035-595">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="06035-595">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="06035-596">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="06035-596">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="06035-597">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="06035-597">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="06035-598">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-598">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="06035-599">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="06035-599">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="06035-600">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-600">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="06035-601">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="06035-601">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="06035-602">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="06035-602">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="06035-603">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="06035-603">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="06035-604">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="06035-604">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="06035-605">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="06035-605">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06035-606">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06035-606">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="06035-607">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="06035-607">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="06035-608">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="06035-608">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="06035-609">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="06035-609">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="06035-610">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="06035-610">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="06035-611">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="06035-611">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="06035-612">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="06035-612">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="06035-613">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-613">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="06035-614">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="06035-614">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="06035-615">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="06035-615">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="06035-616">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="06035-616">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="06035-617">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="06035-617">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="06035-618">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="06035-618">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="06035-619">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="06035-619">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="06035-620">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="06035-620">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="06035-621">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="06035-621">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="06035-622">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="06035-622">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="06035-623">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="06035-623">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="06035-624">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="06035-624">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="06035-625">测试应用</span><span class="sxs-lookup"><span data-stu-id="06035-625">Test the app</span></span>

* <span data-ttu-id="06035-626">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="06035-626">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="06035-627">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="06035-627">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="06035-628">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="06035-628">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="06035-629">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="06035-629">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="06035-631">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="06035-631">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="06035-632">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="06035-632">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="06035-633">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="06035-633">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="06035-634">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="06035-634">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="06035-635">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="06035-635">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06035-636">其他资源</span><span class="sxs-lookup"><span data-stu-id="06035-636">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="06035-637">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="06035-637">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
