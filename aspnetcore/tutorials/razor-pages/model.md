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
ms.openlocfilehash: 1173113b8bb035212cb9b84e7763970b9d5092a3
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711604"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="7d6b0-104">第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="7d6b0-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="7d6b0-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7d6b0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="7d6b0-106">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="7d6b0-107">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="7d6b0-108">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="7d6b0-109">首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="7d6b0-110">模型类称为 POCO 类（源自“简单传统 CLR 对象”   ），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="7d6b0-111">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="7d6b0-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="7d6b0-113">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="7d6b0-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d6b0-115">在“解决方案资源管理器”中，右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7d6b0-116">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="7d6b0-117">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="7d6b0-118">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="7d6b0-119">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="7d6b0-120">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-121">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-122">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-123">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-124">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-125">用户无需在日期字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7d6b0-128">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="7d6b0-129">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="7d6b0-130">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-131">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-132">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-133">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-134">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-134">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-135">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-136">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="7d6b0-137">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="7d6b0-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7d6b0-139">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="7d6b0-140">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="7d6b0-141">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="7d6b0-142">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="7d6b0-143">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="7d6b0-144">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="7d6b0-145">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-146">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-147">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-148">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-149">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-149">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-150">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-151">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="7d6b0-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="7d6b0-153">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7d6b0-154">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="7d6b0-154">Scaffold the movie model</span></span>

<span data-ttu-id="7d6b0-155">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7d6b0-156">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d6b0-158">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="7d6b0-159">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="7d6b0-160">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="7d6b0-161">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![上述说明的图像。](model/_static/5/sca.png)

1. <span data-ttu-id="7d6b0-163">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="7d6b0-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffold.png)

1. <span data-ttu-id="7d6b0-165">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="7d6b0-166">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="7d6b0-167">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="7d6b0-168">在“添加数据上下文”对话框中，生成类名 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="7d6b0-169">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-169">Select **Add**.</span></span>

   ![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="7d6b0-171">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7d6b0-173">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="7d6b0-174">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7d6b0-175">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="7d6b0-176">下表详细说明了 ASP.NET Core 代码生成器选项。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="7d6b0-177">选项</span><span class="sxs-lookup"><span data-stu-id="7d6b0-177">Option</span></span>               | <span data-ttu-id="7d6b0-178">说明</span><span class="sxs-lookup"><span data-stu-id="7d6b0-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="7d6b0-179">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="7d6b0-180">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="7d6b0-181">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="7d6b0-182">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="7d6b0-183">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="7d6b0-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="7d6b0-184">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="7d6b0-185">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7d6b0-187">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-187">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="7d6b0-188">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-188">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="7d6b0-189">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-189">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="7d6b0-190">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-190">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![上述说明的图像。](model/_static/scaMac.png)

1. <span data-ttu-id="7d6b0-192">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="7d6b0-192">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="7d6b0-194">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-194">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="7d6b0-195">在“要使用的 DbContext 类:”行中，将类命名为 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-195">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="7d6b0-196">选择“完成”  。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-196">Select **Finish**.</span></span>

   ![上述说明的图像。](model/_static/5/arpMac.png)

<span data-ttu-id="7d6b0-198">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-198">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="7d6b0-199">创建的文件</span><span class="sxs-lookup"><span data-stu-id="7d6b0-199">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-200">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-201">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-201">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7d6b0-202">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-202">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7d6b0-203">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="7d6b0-203">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7d6b0-204">已更新</span><span class="sxs-lookup"><span data-stu-id="7d6b0-204">Updated</span></span>

* <span data-ttu-id="7d6b0-205">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7d6b0-205">*Startup.cs*</span></span>

<span data-ttu-id="7d6b0-206">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-206">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7d6b0-208">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-208">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="7d6b0-209">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-209">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="7d6b0-210">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-210">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7d6b0-212">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-212">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7d6b0-213">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-213">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7d6b0-214">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="7d6b0-214">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7d6b0-215">已更新</span><span class="sxs-lookup"><span data-stu-id="7d6b0-215">Updated</span></span>

* <span data-ttu-id="7d6b0-216">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7d6b0-216">*Startup.cs*</span></span>

<span data-ttu-id="7d6b0-217">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-217">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="7d6b0-218">使用 EF 的迁移功能创建初始数据库架构</span><span class="sxs-lookup"><span data-stu-id="7d6b0-218">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="7d6b0-219">Entity Framework Core 中的迁移功能提供了一种方法来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-219">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="7d6b0-220">创建初始数据库架构。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-220">Create the initial database schema.</span></span>
* <span data-ttu-id="7d6b0-221">以增量的方式更新数据库架构，使其与应用程序的数据模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-221">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="7d6b0-222">保存数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-222">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-223">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-224">在此部分中，程序包管理器控制台 (PMC) 窗口用于：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-224">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="7d6b0-225">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-225">Add an initial migration.</span></span>
* <span data-ttu-id="7d6b0-226">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-226">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="7d6b0-227">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-227">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 菜单](model/_static/5/pmc.png)

1. <span data-ttu-id="7d6b0-229">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-229">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="7d6b0-231">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-231">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="7d6b0-232">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="7d6b0-232">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7d6b0-233">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="7d6b0-233">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7d6b0-234">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="7d6b0-234">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="7d6b0-235">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-235">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="7d6b0-236">`migrations` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-236">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="7d6b0-237">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-237">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="7d6b0-238">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-238">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="7d6b0-239">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-239">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="7d6b0-240">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-240">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="7d6b0-241">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-241">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-242">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7d6b0-243">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="7d6b0-243">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7d6b0-244">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-244">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7d6b0-245">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-245">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7d6b0-246">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-246">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7d6b0-247">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-247">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7d6b0-248">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-248">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7d6b0-249">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-249">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7d6b0-250">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-250">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7d6b0-251">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-251">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="7d6b0-252">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-252">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="7d6b0-253">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-253">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7d6b0-254">前面的代码为实体集创建 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-254">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="7d6b0-255">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-255">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7d6b0-256">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-256">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7d6b0-257">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-257">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="7d6b0-258">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-258">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-259">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7d6b0-260">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-260">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="7d6b0-261">测试应用</span><span class="sxs-lookup"><span data-stu-id="7d6b0-261">Test the app</span></span>

1. <span data-ttu-id="7d6b0-262">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-262">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="7d6b0-263">如果收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-263">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="7d6b0-264">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-264">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="7d6b0-265">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-265">Test the **Create** link.</span></span>

   ![创建页面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="7d6b0-267">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-267">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7d6b0-268">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-268">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7d6b0-269">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-269">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="7d6b0-270">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-270">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7d6b0-271">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-271">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d6b0-272">其他资源</span><span class="sxs-lookup"><span data-stu-id="7d6b0-272">Additional resources</span></span>


> [!div class="step-by-step"]
> <span data-ttu-id="7d6b0-273">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7d6b0-273">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="7d6b0-274">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-274">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="7d6b0-275">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-275">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="7d6b0-276">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-276">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="7d6b0-277">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-277">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7d6b0-278">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-278">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="7d6b0-279">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-279">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="7d6b0-280">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="7d6b0-280">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-282">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-282">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7d6b0-283">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-283">Name the folder *Models*.</span></span>

<span data-ttu-id="7d6b0-284">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-284">Right-click the *Models* folder.</span></span> <span data-ttu-id="7d6b0-285">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-285">Select **Add** > **Class**.</span></span> <span data-ttu-id="7d6b0-286">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-286">Name the class **Movie**.</span></span>

<span data-ttu-id="7d6b0-287">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-287">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-288">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-288">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-289">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-289">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-290">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-290">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-291">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-291">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-292">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-292">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-293">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-293">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7d6b0-294">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-294">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7d6b0-296">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-296">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7d6b0-297">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-297">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="7d6b0-298">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-298">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-299">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-299">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-300">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-300">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-301">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-301">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-302">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-302">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-303">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-303">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-304">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-304">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7d6b0-305">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-305">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="7d6b0-306">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="7d6b0-306">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="7d6b0-307">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="7d6b0-307">Add a database context class</span></span>

* <span data-ttu-id="7d6b0-308">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-308">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="7d6b0-309">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-309">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7d6b0-310">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-310">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="7d6b0-311">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-311">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="7d6b0-312">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-312">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="7d6b0-313">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="7d6b0-313">Add a database connection string</span></span>

<span data-ttu-id="7d6b0-314">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-314">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="7d6b0-315">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="7d6b0-315">Register the database context</span></span>

<span data-ttu-id="7d6b0-316">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-316">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="7d6b0-317">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-317">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-318">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7d6b0-319">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-319">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="7d6b0-320">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-320">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="7d6b0-321">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-321">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7d6b0-322">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-322">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7d6b0-323">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-323">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7d6b0-324">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-324">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="7d6b0-325">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-325">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-326">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-326">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-327">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-327">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-328">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-328">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-329">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-329">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-330">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-330">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-331">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-331">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="7d6b0-332">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-332">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="7d6b0-333">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-333">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7d6b0-334">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="7d6b0-334">Scaffold the movie model</span></span>

<span data-ttu-id="7d6b0-335">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-335">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7d6b0-336">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-336">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-337">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-337">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-338">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-338">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7d6b0-339">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-339">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7d6b0-340">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-340">Name the folder *Movies*.</span></span>

<span data-ttu-id="7d6b0-341">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-341">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="7d6b0-343">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="7d6b0-343">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="7d6b0-345">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-345">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7d6b0-346">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-346">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7d6b0-347">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-347">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7d6b0-348">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-348">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7d6b0-349">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-349">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7d6b0-350">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-350">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="7d6b0-352">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-352">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-353">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-353">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7d6b0-354">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-354">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="7d6b0-355">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-355">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7d6b0-356">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-356">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="7d6b0-357">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-357">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="7d6b0-358">选项</span><span class="sxs-lookup"><span data-stu-id="7d6b0-358">Option</span></span>               | <span data-ttu-id="7d6b0-359">说明</span><span class="sxs-lookup"><span data-stu-id="7d6b0-359">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="7d6b0-360">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-360">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="7d6b0-361">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-361">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="7d6b0-362">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-362">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="7d6b0-363">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-363">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="7d6b0-364">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="7d6b0-364">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="7d6b0-365">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-365">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="7d6b0-366">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-366">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="7d6b0-367">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="7d6b0-367">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="7d6b0-368">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-368">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="7d6b0-369">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-369">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="7d6b0-370">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-370">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-371">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7d6b0-372">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-372">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7d6b0-373">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-373">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7d6b0-374">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-374">Name the folder *Movies*.</span></span>

<span data-ttu-id="7d6b0-375">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-375">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="7d6b0-377">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="7d6b0-377">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="7d6b0-379">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-379">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7d6b0-380">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-380">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7d6b0-381">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="7d6b0-381">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7d6b0-382">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-382">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7d6b0-383">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-383">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7d6b0-384">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-384">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="7d6b0-386">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-386">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="7d6b0-387">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="7d6b0-387">Add EF tools</span></span>

<span data-ttu-id="7d6b0-388">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-388">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="7d6b0-389">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-389">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="7d6b0-390">有关详细信息，请参阅 [Entity Framework Core 工具参考 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-390">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="7d6b0-391">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="7d6b0-391">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="7d6b0-392">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-392">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="7d6b0-393">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-393">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="7d6b0-394">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-394">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="7d6b0-395">创建的文件</span><span class="sxs-lookup"><span data-stu-id="7d6b0-395">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-396">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-397">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-397">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7d6b0-398">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-398">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7d6b0-399">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="7d6b0-399">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7d6b0-400">已更新</span><span class="sxs-lookup"><span data-stu-id="7d6b0-400">Updated</span></span>

* <span data-ttu-id="7d6b0-401">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7d6b0-401">*Startup.cs*</span></span>

<span data-ttu-id="7d6b0-402">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-402">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-403">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7d6b0-404">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-404">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7d6b0-405">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-405">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7d6b0-406">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="7d6b0-406">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7d6b0-407">已更新</span><span class="sxs-lookup"><span data-stu-id="7d6b0-407">Updated</span></span>

* <span data-ttu-id="7d6b0-408">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7d6b0-408">*Startup.cs*</span></span>

<span data-ttu-id="7d6b0-409">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-409">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7d6b0-411">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-411">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="7d6b0-412">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-412">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="7d6b0-413">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-413">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7d6b0-414">初始迁移</span><span class="sxs-lookup"><span data-stu-id="7d6b0-414">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-416">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-416">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7d6b0-417">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-417">Add an initial migration.</span></span>
* <span data-ttu-id="7d6b0-418">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-418">Update the database with the initial migration.</span></span>

<span data-ttu-id="7d6b0-419">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-419">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7d6b0-421">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-421">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-422">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-422">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="7d6b0-423">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-423">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="7d6b0-424">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="7d6b0-424">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7d6b0-425">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="7d6b0-425">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7d6b0-426">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="7d6b0-426">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="7d6b0-427">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-427">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="7d6b0-428">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-428">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="7d6b0-429">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-429">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="7d6b0-430">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-430">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="7d6b0-431">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-431">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="7d6b0-432">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-432">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="7d6b0-433">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-433">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-434">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7d6b0-435">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="7d6b0-435">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7d6b0-436">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-436">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7d6b0-437">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-437">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7d6b0-438">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-438">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="7d6b0-439">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-439">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7d6b0-440">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-440">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7d6b0-441">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-441">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7d6b0-442">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-442">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7d6b0-443">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-443">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="7d6b0-444">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-444">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="7d6b0-445">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-445">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7d6b0-446">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-446">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7d6b0-447">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-447">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7d6b0-448">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-448">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7d6b0-449">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-449">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="7d6b0-450">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-450">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-451">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-451">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7d6b0-452">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-452">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7d6b0-453">测试应用</span><span class="sxs-lookup"><span data-stu-id="7d6b0-453">Test the app</span></span>

* <span data-ttu-id="7d6b0-454">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-454">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7d6b0-455">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-455">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7d6b0-456">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-456">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7d6b0-457">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-457">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="7d6b0-459">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-459">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7d6b0-460">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-460">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7d6b0-461">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-461">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7d6b0-462">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-462">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7d6b0-463">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-463">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d6b0-464">其他资源</span><span class="sxs-lookup"><span data-stu-id="7d6b0-464">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7d6b0-465">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7d6b0-465">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7d6b0-466">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-466">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7d6b0-467">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-467">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7d6b0-468">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-468">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7d6b0-469">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-469">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7d6b0-470">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-470">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7d6b0-471">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-471">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="7d6b0-472">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-472">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="7d6b0-473">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="7d6b0-473">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-474">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-474">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-475">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-475">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7d6b0-476">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-476">Name the folder *Models*.</span></span>

<span data-ttu-id="7d6b0-477">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-477">Right-click the *Models* folder.</span></span> <span data-ttu-id="7d6b0-478">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-478">Select **Add** > **Class**.</span></span> <span data-ttu-id="7d6b0-479">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-479">Name the class **Movie**.</span></span>

<span data-ttu-id="7d6b0-480">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-480">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-481">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-481">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-482">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-482">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-483">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-483">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-484">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-484">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-485">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-485">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-486">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-486">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7d6b0-487">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-487">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-488">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-488">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7d6b0-489">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-489">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7d6b0-490">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-490">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="7d6b0-491">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-491">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-492">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-492">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-493">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-493">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-494">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-494">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-495">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-495">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-496">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-496">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-497">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-497">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7d6b0-498">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-498">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="7d6b0-499">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="7d6b0-499">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="7d6b0-500">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="7d6b0-500">Add a database context class</span></span>

<span data-ttu-id="7d6b0-501">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-501">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="7d6b0-502">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-502">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7d6b0-503">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-503">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="7d6b0-504">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-504">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="7d6b0-505">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-505">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="7d6b0-506">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="7d6b0-506">Add a database connection string</span></span>

<span data-ttu-id="7d6b0-507">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-507">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="7d6b0-508">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="7d6b0-508">Add required NuGet packages</span></span>

<span data-ttu-id="7d6b0-509">运行以下 .NET Core CLI 命令，将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-509">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="7d6b0-510">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-510">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="7d6b0-511">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="7d6b0-511">Register the database context</span></span>

<span data-ttu-id="7d6b0-512">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-512">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="7d6b0-513">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-513">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="7d6b0-514">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-514">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-515">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-515">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7d6b0-516">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-516">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="7d6b0-517">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-517">Name the folder *Models*.</span></span>
* <span data-ttu-id="7d6b0-518">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-518">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="7d6b0-519">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-519">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7d6b0-520">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-520">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7d6b0-521">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-521">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7d6b0-522">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-522">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="7d6b0-523">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-523">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="7d6b0-524">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-524">The `Movie` class contains:</span></span>

* <span data-ttu-id="7d6b0-525">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-525">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="7d6b0-526">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-526">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="7d6b0-527">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-527">With this attribute:</span></span>

  * <span data-ttu-id="7d6b0-528">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-528">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="7d6b0-529">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-529">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="7d6b0-530">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-530">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="7d6b0-531">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-531">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7d6b0-532">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="7d6b0-532">Scaffold the movie model</span></span>

<span data-ttu-id="7d6b0-533">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-533">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7d6b0-534">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-534">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-535">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-535">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-536">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-536">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7d6b0-537">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-537">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7d6b0-538">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-538">Name the folder *Movies*.</span></span>

<span data-ttu-id="7d6b0-539">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-539">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="7d6b0-541">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="7d6b0-541">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="7d6b0-543">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-543">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="7d6b0-544">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-544">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7d6b0-545">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-545">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7d6b0-546">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-546">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="7d6b0-548">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-548">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d6b0-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d6b0-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7d6b0-550">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-550">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="7d6b0-551">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-551">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7d6b0-552">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-552">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="7d6b0-553">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-553">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="7d6b0-554">选项</span><span class="sxs-lookup"><span data-stu-id="7d6b0-554">Option</span></span>               | <span data-ttu-id="7d6b0-555">说明</span><span class="sxs-lookup"><span data-stu-id="7d6b0-555">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="7d6b0-556">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-556">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="7d6b0-557">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-557">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="7d6b0-558">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-558">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="7d6b0-559">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-559">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="7d6b0-560">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="7d6b0-560">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="7d6b0-561">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-561">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="7d6b0-562">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-562">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-563">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-563">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7d6b0-564">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-564">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7d6b0-565">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-565">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7d6b0-566">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-566">Name the folder *Movies*.</span></span>

<span data-ttu-id="7d6b0-567">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-567">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="7d6b0-569">在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="7d6b0-569">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="7d6b0-571">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-571">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7d6b0-572">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-572">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="7d6b0-573">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-573">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="7d6b0-574">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-574">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7d6b0-575">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-575">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="7d6b0-577">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-577">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="7d6b0-578">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-578">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7d6b0-579">创建的文件</span><span class="sxs-lookup"><span data-stu-id="7d6b0-579">Files created</span></span>

* <span data-ttu-id="7d6b0-580">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-580">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7d6b0-581">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="7d6b0-581">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="7d6b0-582">文件已更新</span><span class="sxs-lookup"><span data-stu-id="7d6b0-582">File updated</span></span>

* <span data-ttu-id="7d6b0-583">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7d6b0-583">*Startup.cs*</span></span>

<span data-ttu-id="7d6b0-584">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-584">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7d6b0-585">初始迁移</span><span class="sxs-lookup"><span data-stu-id="7d6b0-585">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-586">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7d6b0-587">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-587">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7d6b0-588">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-588">Add an initial migration.</span></span>
* <span data-ttu-id="7d6b0-589">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-589">Update the database with the initial migration.</span></span>

<span data-ttu-id="7d6b0-590">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-590">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7d6b0-592">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-592">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="7d6b0-593">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-593">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="7d6b0-594">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-594">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="7d6b0-595">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-595">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="7d6b0-596">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-596">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="7d6b0-597">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-597">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="7d6b0-598">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-598">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="7d6b0-599">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-599">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-600">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-600">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="7d6b0-601">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-601">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="7d6b0-602">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="7d6b0-602">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7d6b0-603">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="7d6b0-603">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7d6b0-604">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="7d6b0-604">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="7d6b0-605">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-605">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d6b0-606">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d6b0-606">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7d6b0-607">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="7d6b0-607">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7d6b0-608">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-608">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7d6b0-609">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-609">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7d6b0-610">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-610">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7d6b0-611">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-611">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7d6b0-612">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-612">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7d6b0-613">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-613">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7d6b0-614">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-614">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="7d6b0-615">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-615">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="7d6b0-616">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-616">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="7d6b0-617">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-617">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7d6b0-618">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-618">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7d6b0-619">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-619">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7d6b0-620">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-620">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7d6b0-621">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-621">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="7d6b0-622">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-622">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7d6b0-623">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d6b0-623">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7d6b0-624">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-624">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7d6b0-625">测试应用</span><span class="sxs-lookup"><span data-stu-id="7d6b0-625">Test the app</span></span>

* <span data-ttu-id="7d6b0-626">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-626">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7d6b0-627">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="7d6b0-627">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7d6b0-628">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-628">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7d6b0-629">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-629">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7d6b0-631">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-631">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7d6b0-632">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-632">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7d6b0-633">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-633">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7d6b0-634">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-634">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7d6b0-635">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="7d6b0-635">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d6b0-636">其他资源</span><span class="sxs-lookup"><span data-stu-id="7d6b0-636">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7d6b0-637">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7d6b0-637">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
