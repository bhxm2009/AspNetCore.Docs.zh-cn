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
ms.openlocfilehash: 366b2cbfc18a8666040ff9e3c5f1b2bb5a6a9f84
ms.sourcegitcommit: 32203a42260e70d007c17a2b748b1bd5b9b662c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107298607"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="6c2b6-104">第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="6c2b6-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="6c2b6-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6c2b6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="6c2b6-106">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="6c2b6-107">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="6c2b6-108">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="6c2b6-109">首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="6c2b6-110">模型类称为 POCO 类（源自“简单传统 CLR 对象”   ），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="6c2b6-111">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="6c2b6-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="6c2b6-113">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="6c2b6-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6c2b6-115">在“解决方案资源管理器”中，右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="6c2b6-116">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="6c2b6-117">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="6c2b6-118">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="6c2b6-119">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="6c2b6-120">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-121">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-122">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-123">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-124">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-125">用户无需在日期字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6c2b6-128">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="6c2b6-129">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="6c2b6-130">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-131">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-132">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-133">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-134">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-134">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-135">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-136">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="6c2b6-137">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="6c2b6-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6c2b6-139">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="6c2b6-140">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="6c2b6-141">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="6c2b6-142">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="6c2b6-143">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="6c2b6-144">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="6c2b6-145">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-146">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-147">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-148">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-149">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-149">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-150">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-151">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="6c2b6-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="6c2b6-153">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6c2b6-154">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="6c2b6-154">Scaffold the movie model</span></span>

<span data-ttu-id="6c2b6-155">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6c2b6-156">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6c2b6-158">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="6c2b6-159">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="6c2b6-160">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="6c2b6-161">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![上述说明的图像。](model/_static/5/sca.png)

1. <span data-ttu-id="6c2b6-163">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="6c2b6-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffold.png)

1. <span data-ttu-id="6c2b6-165">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="6c2b6-166">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="6c2b6-167">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="6c2b6-168">在“添加数据上下文”对话框中，生成类名 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="6c2b6-169">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-169">Select **Add**.</span></span>

   ![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="6c2b6-171">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c2b6-173">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span> <span data-ttu-id="6c2b6-174">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-174">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries -sqlite
  ```

<a name="codegenerator"></a> <span data-ttu-id="6c2b6-175">下表详细说明了 ASP.NET Core 代码生成器选项。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-175">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="6c2b6-176">选项</span><span class="sxs-lookup"><span data-stu-id="6c2b6-176">Option</span></span>               | <span data-ttu-id="6c2b6-177">说明</span><span class="sxs-lookup"><span data-stu-id="6c2b6-177">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="6c2b6-178">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-178">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="6c2b6-179">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-179">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="6c2b6-180">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-180">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="6c2b6-181">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-181">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="6c2b6-182">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="6c2b6-182">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="6c2b6-183">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-183">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="6c2b6-184">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-184">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6c2b6-186">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-186">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="6c2b6-187">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-187">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="6c2b6-188">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-188">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="6c2b6-189">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-189">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![上述说明的图像。](model/_static/scaMac.png)

1. <span data-ttu-id="6c2b6-191">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="6c2b6-191">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="6c2b6-193">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-193">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="6c2b6-194">在“要使用的 DbContext 类:”行中，将类命名为 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-194">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="6c2b6-195">选择“完成”  。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-195">Select **Finish**.</span></span>

   ![上述说明的图像。](model/_static/5/arpMac.png)

<span data-ttu-id="6c2b6-197">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-197">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="6c2b6-198">创建的文件</span><span class="sxs-lookup"><span data-stu-id="6c2b6-198">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-199">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-199">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-200">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-200">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="6c2b6-201">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-201">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6c2b6-202">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="6c2b6-202">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="6c2b6-203">已更新</span><span class="sxs-lookup"><span data-stu-id="6c2b6-203">Updated</span></span>

* <span data-ttu-id="6c2b6-204">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6c2b6-204">*Startup.cs*</span></span>

<span data-ttu-id="6c2b6-205">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-205">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6c2b6-207">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-207">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="6c2b6-208">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-208">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="6c2b6-209">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-209">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-210">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-210">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c2b6-211">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-211">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="6c2b6-212">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-212">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6c2b6-213">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="6c2b6-213">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="6c2b6-214">已更新</span><span class="sxs-lookup"><span data-stu-id="6c2b6-214">Updated</span></span>

* <span data-ttu-id="6c2b6-215">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6c2b6-215">*Startup.cs*</span></span>

<span data-ttu-id="6c2b6-216">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-216">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="6c2b6-217">使用 EF 的迁移功能创建初始数据库架构</span><span class="sxs-lookup"><span data-stu-id="6c2b6-217">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="6c2b6-218">Entity Framework Core 中的迁移功能提供了一种方法来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-218">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="6c2b6-219">创建初始数据库架构。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-219">Create the initial database schema.</span></span>
* <span data-ttu-id="6c2b6-220">以增量的方式更新数据库架构，使其与应用程序的数据模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-220">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="6c2b6-221">保存数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-221">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-222">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-223">在此部分中，程序包管理器控制台 (PMC) 窗口用于：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-223">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="6c2b6-224">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-224">Add an initial migration.</span></span>
* <span data-ttu-id="6c2b6-225">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-225">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="6c2b6-226">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-226">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 菜单](model/_static/5/pmc.png)

1. <span data-ttu-id="6c2b6-228">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-228">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="6c2b6-230">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-230">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="6c2b6-231">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="6c2b6-231">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6c2b6-232">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="6c2b6-232">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6c2b6-233">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="6c2b6-233">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="6c2b6-234">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-234">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="6c2b6-235">`migrations` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-235">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="6c2b6-236">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-236">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="6c2b6-237">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-237">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="6c2b6-238">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-238">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="6c2b6-239">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-239">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="6c2b6-240">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-240">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-241">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6c2b6-242">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="6c2b6-242">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6c2b6-243">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-243">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6c2b6-244">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-244">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6c2b6-245">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-245">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6c2b6-246">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-246">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6c2b6-247">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-247">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6c2b6-248">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-248">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6c2b6-249">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-249">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="6c2b6-250">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-250">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="6c2b6-251">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-251">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="6c2b6-252">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-252">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6c2b6-253">前面的代码为实体集创建 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-253">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="6c2b6-254">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-254">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6c2b6-255">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-255">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6c2b6-256">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-256">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="6c2b6-257">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-257">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-258">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-258">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6c2b6-259">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-259">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="6c2b6-260">测试应用</span><span class="sxs-lookup"><span data-stu-id="6c2b6-260">Test the app</span></span>

1. <span data-ttu-id="6c2b6-261">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-261">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="6c2b6-262">如果收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-262">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="6c2b6-263">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-263">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="6c2b6-264">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-264">Test the **Create** link.</span></span>

   ![创建页面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="6c2b6-266">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-266">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6c2b6-267">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-267">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6c2b6-268">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-268">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="6c2b6-269">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-269">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6c2b6-270">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-270">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c2b6-271">其他资源</span><span class="sxs-lookup"><span data-stu-id="6c2b6-271">Additional resources</span></span>


> [!div class="step-by-step"]
> <span data-ttu-id="6c2b6-272">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="6c2b6-272">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="6c2b6-273">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-273">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="6c2b6-274">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-274">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="6c2b6-275">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-275">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="6c2b6-276">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-276">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6c2b6-277">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-277">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="6c2b6-278">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="6c2b6-279">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="6c2b6-279">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-280">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-281">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-281">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="6c2b6-282">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-282">Name the folder *Models*.</span></span>

<span data-ttu-id="6c2b6-283">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-283">Right-click the *Models* folder.</span></span> <span data-ttu-id="6c2b6-284">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-284">Select **Add** > **Class**.</span></span> <span data-ttu-id="6c2b6-285">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-285">Name the class **Movie**.</span></span>

<span data-ttu-id="6c2b6-286">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-286">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-287">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-287">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-288">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-288">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-289">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-289">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-290">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-290">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-291">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-291">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-292">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-292">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6c2b6-293">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-293">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c2b6-295">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-295">Add a folder named *Models*.</span></span>
* <span data-ttu-id="6c2b6-296">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-296">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="6c2b6-297">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-297">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-298">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-298">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-299">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-299">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-300">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-300">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-301">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-301">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-302">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-302">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-303">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6c2b6-304">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-304">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="6c2b6-305">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="6c2b6-305">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="6c2b6-306">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="6c2b6-306">Add a database context class</span></span>

* <span data-ttu-id="6c2b6-307">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-307">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="6c2b6-308">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-308">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6c2b6-309">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-309">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="6c2b6-310">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-310">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="6c2b6-311">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-311">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="6c2b6-312">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="6c2b6-312">Add a database connection string</span></span>

<span data-ttu-id="6c2b6-313">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-313">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="6c2b6-314">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="6c2b6-314">Register the database context</span></span>

<span data-ttu-id="6c2b6-315">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-315">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6c2b6-316">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-316">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-317">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c2b6-318">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-318">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="6c2b6-319">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-319">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="6c2b6-320">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-320">In the **New File** dialog:</span></span>

  * <span data-ttu-id="6c2b6-321">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-321">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="6c2b6-322">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-322">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="6c2b6-323">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-323">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="6c2b6-324">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-324">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-325">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-325">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-326">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-326">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-327">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-327">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-328">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-328">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-329">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-329">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-330">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-330">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="6c2b6-331">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-331">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="6c2b6-332">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-332">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6c2b6-333">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="6c2b6-333">Scaffold the movie model</span></span>

<span data-ttu-id="6c2b6-334">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-334">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6c2b6-335">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-335">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-336">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-336">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-337">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-337">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="6c2b6-338">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-338">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="6c2b6-339">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-339">Name the folder *Movies*.</span></span>

<span data-ttu-id="6c2b6-340">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-340">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="6c2b6-342">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="6c2b6-342">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="6c2b6-344">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-344">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="6c2b6-345">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-345">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="6c2b6-346">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-346">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="6c2b6-347">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-347">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="6c2b6-348">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-348">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="6c2b6-349">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-349">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="6c2b6-351">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-351">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-352">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-352">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6c2b6-353">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-353">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="6c2b6-354">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-354">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="6c2b6-355">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-355">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="6c2b6-356">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-356">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="6c2b6-357">选项</span><span class="sxs-lookup"><span data-stu-id="6c2b6-357">Option</span></span>               | <span data-ttu-id="6c2b6-358">说明</span><span class="sxs-lookup"><span data-stu-id="6c2b6-358">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="6c2b6-359">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-359">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="6c2b6-360">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-360">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="6c2b6-361">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-361">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="6c2b6-362">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-362">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="6c2b6-363">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="6c2b6-363">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="6c2b6-364">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-364">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="6c2b6-365">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-365">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="6c2b6-366">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="6c2b6-366">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="6c2b6-367">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-367">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="6c2b6-368">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-368">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="6c2b6-369">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-369">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-370">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-370">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c2b6-371">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-371">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="6c2b6-372">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-372">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="6c2b6-373">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-373">Name the folder *Movies*.</span></span>

<span data-ttu-id="6c2b6-374">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-374">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="6c2b6-376">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="6c2b6-376">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="6c2b6-378">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-378">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="6c2b6-379">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-379">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="6c2b6-380">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="6c2b6-380">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="6c2b6-381">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-381">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="6c2b6-382">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-382">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="6c2b6-383">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-383">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="6c2b6-385">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-385">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="6c2b6-386">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="6c2b6-386">Add EF tools</span></span>

<span data-ttu-id="6c2b6-387">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-387">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="6c2b6-388">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-388">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="6c2b6-389">有关详细信息，请参阅 [Entity Framework Core 工具参考 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-389">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="6c2b6-390">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="6c2b6-390">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="6c2b6-391">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-391">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="6c2b6-392">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-392">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="6c2b6-393">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-393">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="6c2b6-394">创建的文件</span><span class="sxs-lookup"><span data-stu-id="6c2b6-394">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-395">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-395">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-396">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-396">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="6c2b6-397">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-397">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6c2b6-398">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="6c2b6-398">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="6c2b6-399">已更新</span><span class="sxs-lookup"><span data-stu-id="6c2b6-399">Updated</span></span>

* <span data-ttu-id="6c2b6-400">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6c2b6-400">*Startup.cs*</span></span>

<span data-ttu-id="6c2b6-401">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-401">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-402">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-402">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c2b6-403">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-403">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="6c2b6-404">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-404">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6c2b6-405">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="6c2b6-405">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="6c2b6-406">已更新</span><span class="sxs-lookup"><span data-stu-id="6c2b6-406">Updated</span></span>

* <span data-ttu-id="6c2b6-407">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6c2b6-407">*Startup.cs*</span></span>

<span data-ttu-id="6c2b6-408">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-408">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6c2b6-410">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-410">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="6c2b6-411">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-411">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="6c2b6-412">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-412">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6c2b6-413">初始迁移</span><span class="sxs-lookup"><span data-stu-id="6c2b6-413">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-414">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-414">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-415">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-415">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="6c2b6-416">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-416">Add an initial migration.</span></span>
* <span data-ttu-id="6c2b6-417">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-417">Update the database with the initial migration.</span></span>

<span data-ttu-id="6c2b6-418">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-418">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6c2b6-420">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-420">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-421">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-421">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="6c2b6-422">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-422">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="6c2b6-423">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="6c2b6-423">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6c2b6-424">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="6c2b6-424">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6c2b6-425">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="6c2b6-425">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="6c2b6-426">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-426">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="6c2b6-427">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-427">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="6c2b6-428">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-428">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="6c2b6-429">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-429">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="6c2b6-430">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-430">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="6c2b6-431">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-431">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="6c2b6-432">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-432">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-433">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6c2b6-434">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="6c2b6-434">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6c2b6-435">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-435">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6c2b6-436">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-436">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6c2b6-437">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-437">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="6c2b6-438">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-438">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6c2b6-439">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-439">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6c2b6-440">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-440">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6c2b6-441">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-441">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="6c2b6-442">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-442">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="6c2b6-443">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-443">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="6c2b6-444">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-444">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6c2b6-445">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-445">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6c2b6-446">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-446">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6c2b6-447">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-447">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6c2b6-448">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-448">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="6c2b6-449">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-449">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-450">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-450">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6c2b6-451">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-451">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6c2b6-452">测试应用</span><span class="sxs-lookup"><span data-stu-id="6c2b6-452">Test the app</span></span>

* <span data-ttu-id="6c2b6-453">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-453">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6c2b6-454">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-454">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6c2b6-455">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-455">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6c2b6-456">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-456">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="6c2b6-458">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-458">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6c2b6-459">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-459">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6c2b6-460">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-460">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6c2b6-461">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-461">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6c2b6-462">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-462">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c2b6-463">其他资源</span><span class="sxs-lookup"><span data-stu-id="6c2b6-463">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6c2b6-464">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="6c2b6-464">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6c2b6-465">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-465">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="6c2b6-466">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-466">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="6c2b6-467">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-467">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="6c2b6-468">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-468">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="6c2b6-469">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-469">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6c2b6-470">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-470">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="6c2b6-471">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-471">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="6c2b6-472">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="6c2b6-472">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-473">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-473">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-474">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-474">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="6c2b6-475">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-475">Name the folder *Models*.</span></span>

<span data-ttu-id="6c2b6-476">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-476">Right-click the *Models* folder.</span></span> <span data-ttu-id="6c2b6-477">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-477">Select **Add** > **Class**.</span></span> <span data-ttu-id="6c2b6-478">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-478">Name the class **Movie**.</span></span>

<span data-ttu-id="6c2b6-479">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-479">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-480">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-480">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-481">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-481">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-482">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-482">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-483">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-483">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-484">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-484">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-485">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-485">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6c2b6-486">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-486">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-487">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-487">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c2b6-488">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-488">Add a folder named *Models*.</span></span>
* <span data-ttu-id="6c2b6-489">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-489">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="6c2b6-490">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-490">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-491">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-491">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-492">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-492">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-493">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-493">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-494">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-494">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-495">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-495">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-496">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-496">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6c2b6-497">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-497">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="6c2b6-498">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="6c2b6-498">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="6c2b6-499">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="6c2b6-499">Add a database context class</span></span>

<span data-ttu-id="6c2b6-500">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-500">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="6c2b6-501">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-501">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6c2b6-502">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-502">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="6c2b6-503">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-503">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="6c2b6-504">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-504">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="6c2b6-505">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="6c2b6-505">Add a database connection string</span></span>

<span data-ttu-id="6c2b6-506">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-506">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="6c2b6-507">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="6c2b6-507">Add required NuGet packages</span></span>

<span data-ttu-id="6c2b6-508">运行以下 .NET Core CLI 命令，将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-508">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="6c2b6-509">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-509">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="6c2b6-510">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="6c2b6-510">Register the database context</span></span>

<span data-ttu-id="6c2b6-511">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-511">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6c2b6-512">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-512">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="6c2b6-513">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-513">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-514">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-514">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c2b6-515">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-515">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="6c2b6-516">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-516">Name the folder *Models*.</span></span>
* <span data-ttu-id="6c2b6-517">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-517">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="6c2b6-518">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-518">In the **New File** dialog:</span></span>

  * <span data-ttu-id="6c2b6-519">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-519">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="6c2b6-520">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-520">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="6c2b6-521">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-521">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="6c2b6-522">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-522">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="6c2b6-523">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-523">The `Movie` class contains:</span></span>

* <span data-ttu-id="6c2b6-524">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-524">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="6c2b6-525">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-525">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c2b6-526">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-526">With this attribute:</span></span>

  * <span data-ttu-id="6c2b6-527">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-527">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="6c2b6-528">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-528">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6c2b6-529">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-529">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="6c2b6-530">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-530">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6c2b6-531">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="6c2b6-531">Scaffold the movie model</span></span>

<span data-ttu-id="6c2b6-532">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-532">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6c2b6-533">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-533">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-535">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-535">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="6c2b6-536">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-536">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="6c2b6-537">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-537">Name the folder *Movies*.</span></span>

<span data-ttu-id="6c2b6-538">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-538">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="6c2b6-540">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="6c2b6-540">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="6c2b6-542">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-542">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="6c2b6-543">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-543">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="6c2b6-544">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-544">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="6c2b6-545">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-545">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="6c2b6-547">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-547">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6c2b6-548">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c2b6-548">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6c2b6-549">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-549">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="6c2b6-550">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-550">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="6c2b6-551">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-551">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="6c2b6-552">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-552">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="6c2b6-553">选项</span><span class="sxs-lookup"><span data-stu-id="6c2b6-553">Option</span></span>               | <span data-ttu-id="6c2b6-554">说明</span><span class="sxs-lookup"><span data-stu-id="6c2b6-554">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="6c2b6-555">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-555">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="6c2b6-556">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-556">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="6c2b6-557">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-557">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="6c2b6-558">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-558">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="6c2b6-559">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="6c2b6-559">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="6c2b6-560">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-560">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="6c2b6-561">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-561">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-562">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-562">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c2b6-563">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-563">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="6c2b6-564">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-564">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="6c2b6-565">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-565">Name the folder *Movies*.</span></span>

<span data-ttu-id="6c2b6-566">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-566">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="6c2b6-568">在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="6c2b6-568">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="6c2b6-570">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-570">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="6c2b6-571">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-571">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="6c2b6-572">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-572">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="6c2b6-573">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-573">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="6c2b6-574">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-574">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="6c2b6-576">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-576">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="6c2b6-577">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-577">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="6c2b6-578">创建的文件</span><span class="sxs-lookup"><span data-stu-id="6c2b6-578">Files created</span></span>

* <span data-ttu-id="6c2b6-579">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-579">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6c2b6-580">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="6c2b6-580">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="6c2b6-581">文件已更新</span><span class="sxs-lookup"><span data-stu-id="6c2b6-581">File updated</span></span>

* <span data-ttu-id="6c2b6-582">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6c2b6-582">*Startup.cs*</span></span>

<span data-ttu-id="6c2b6-583">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-583">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6c2b6-584">初始迁移</span><span class="sxs-lookup"><span data-stu-id="6c2b6-584">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c2b6-586">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-586">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="6c2b6-587">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-587">Add an initial migration.</span></span>
* <span data-ttu-id="6c2b6-588">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-588">Update the database with the initial migration.</span></span>

<span data-ttu-id="6c2b6-589">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-589">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6c2b6-591">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-591">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="6c2b6-592">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-592">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="6c2b6-593">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-593">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="6c2b6-594">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-594">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="6c2b6-595">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-595">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="6c2b6-596">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-596">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="6c2b6-597">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-597">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="6c2b6-598">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-598">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-599">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-599">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="6c2b6-600">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-600">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="6c2b6-601">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="6c2b6-601">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6c2b6-602">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="6c2b6-602">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6c2b6-603">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="6c2b6-603">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="6c2b6-604">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-604">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6c2b6-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c2b6-605">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6c2b6-606">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="6c2b6-606">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6c2b6-607">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-607">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6c2b6-608">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-608">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6c2b6-609">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-609">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6c2b6-610">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-610">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6c2b6-611">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-611">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6c2b6-612">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-612">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6c2b6-613">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-613">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="6c2b6-614">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-614">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="6c2b6-615">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-615">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="6c2b6-616">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-616">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6c2b6-617">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-617">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6c2b6-618">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-618">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6c2b6-619">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-619">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6c2b6-620">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-620">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="6c2b6-621">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-621">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6c2b6-622">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6c2b6-622">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6c2b6-623">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-623">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6c2b6-624">测试应用</span><span class="sxs-lookup"><span data-stu-id="6c2b6-624">Test the app</span></span>

* <span data-ttu-id="6c2b6-625">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-625">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6c2b6-626">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="6c2b6-626">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6c2b6-627">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-627">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6c2b6-628">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-628">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="6c2b6-630">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-630">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6c2b6-631">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-631">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6c2b6-632">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-632">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6c2b6-633">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-633">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6c2b6-634">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="6c2b6-634">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c2b6-635">其他资源</span><span class="sxs-lookup"><span data-stu-id="6c2b6-635">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6c2b6-636">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="6c2b6-636">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
