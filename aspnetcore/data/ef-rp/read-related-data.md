---
title: 第 6 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 读取相关数据
author: rick-anderson
description: Razor 页面和实体框架教程系列第 6 部分。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 6e9933b7e4745bb59313b939f6499d6732460730
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957764"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="8ad9e-103">第 6 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 读取相关数据</span><span class="sxs-lookup"><span data-stu-id="8ad9e-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="8ad9e-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8ad9e-105">本教程介绍如何读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="8ad9e-106">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="8ad9e-107">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-107">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

![“讲师索引”页](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="8ad9e-110">预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="8ad9e-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="8ad9e-111">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8ad9e-112">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="8ad9e-113">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="8ad9e-114">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="8ad9e-115">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8ad9e-116">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="8ad9e-117">发布多个查询可能比发布大型的单个查询更为有效。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-117">Issuing multiple queries can be more efficient than a large single query.</span></span> <span data-ttu-id="8ad9e-118">预先加载通过 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Include%2A> 和 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.ThenInclude%2A> 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-118">Eager loading is specified with the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Include%2A> and <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.ThenInclude%2A> methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="8ad9e-120">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="8ad9e-121">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="8ad9e-121">One query for the main query</span></span> 
  * <span data-ttu-id="8ad9e-122">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="8ad9e-123">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="8ad9e-124">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="8ad9e-125">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="8ad9e-127">**注意：** EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8ad9e-128">即使导航属性的数据非显式包含在内，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="8ad9e-129">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="8ad9e-130">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ad9e-131">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="8ad9e-132">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="8ad9e-133">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8ad9e-134">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8ad9e-135">例如：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-135">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8ad9e-137">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ad9e-138">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ad9e-139">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8ad9e-140">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="8ad9e-141">延迟加载会损害性能，例如当开发人员使用 [N+1 查询](https://www.bing.com/search?q=N%2B1+queries)时。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-141">Lazy loading can hurt performance, for example when developers use [N+1 queries](https://www.bing.com/search?q=N%2B1+queries).</span></span> <span data-ttu-id="8ad9e-142">N+1 查询加载父级并枚举子级。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-142">N+1 queries load a parent and enumerate through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="8ad9e-143">创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-143">Create Course pages</span></span>

<span data-ttu-id="8ad9e-144">`Course` 实体包括一个带相关 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-144">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="8ad9e-146">若要显示课程的已分配院系的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-146">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="8ad9e-147">将相关的 `Department` 实体加载到 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-147">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="8ad9e-148">获取 `Department` 实体的 `Name` 属性中的名称。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-148">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="8ad9e-149">搭建“课程”页的基架</span><span class="sxs-lookup"><span data-stu-id="8ad9e-149">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ad9e-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ad9e-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ad9e-151">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-151">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="8ad9e-152">创建“Pages/Courses”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-152">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="8ad9e-153">将 `Course` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-153">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="8ad9e-154">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-154">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ad9e-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ad9e-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8ad9e-156">创建“Pages/Courses”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-156">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="8ad9e-157">运行以下命令，搭建“课程”页的基架。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-157">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="8ad9e-158">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-158">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="8ad9e-159">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="8ad9e-159">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="8ad9e-160">打开 Pages/Courses/Index.cshtml.cs 并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-160">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="8ad9e-161">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-161">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="8ad9e-162">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-162">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="8ad9e-163">运行应用并选择“课程”链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-163">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="8ad9e-164">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-164">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="8ad9e-165">显示院系名称</span><span class="sxs-lookup"><span data-stu-id="8ad9e-165">Display the department name</span></span>

<span data-ttu-id="8ad9e-166">使用以下代码更新 Pages/Courses/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-166">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="8ad9e-167">上述代码将 `Course` 属性更改为 `Courses`，然后添加 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-167">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="8ad9e-168">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-168">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="8ad9e-169">无需跟踪实体，因为未在当前的上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-169">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="8ad9e-170">使用以下代码更新 Pages/Courses/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-170">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="8ad9e-171">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-171">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="8ad9e-172">将 `Course` 属性名称更改为了 `Courses`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-172">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="8ad9e-173">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-173">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8ad9e-174">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-174">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8ad9e-175">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-175">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="8ad9e-176">更改“院系”列，显示院系名称。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-176">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8ad9e-177">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-177">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8ad9e-178">运行应用并选择“课程”选项卡，查看包含系名称的列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-178">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="8ad9e-180">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="8ad9e-180">Loading related data with Select</span></span>

<span data-ttu-id="8ad9e-181">`OnGetAsync` 方法使用 `Include` 方法加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-181">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="8ad9e-182">`Select` 方法是只加载所需相关数据的替代方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-182">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="8ad9e-183">对于单个项（如 `Department.Name`），它使用 `SQL INNER JOIN`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-183">For single items, like the `Department.Name` it uses a `SQL INNER JOIN`.</span></span> <span data-ttu-id="8ad9e-184">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-184">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="8ad9e-185">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-185">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/IndexSelectModel.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="8ad9e-186">上述代码不会返回任何实体类型，因此不进行任何跟踪。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-186">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="8ad9e-187">有关 EF 跟踪的详细信息，请参阅 [跟踪查询与非跟踪查询](/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-187">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="8ad9e-188">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-188">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/CourseViewModel.cs)]

<span data-ttu-id="8ad9e-189">有关完整的 Razor Pages，请参阅 [IndexSelectModel](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu50/Pages/Courses)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-189">See [IndexSelectModel](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu50/Pages/Courses) for the complete Razor Pages.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="8ad9e-190">创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-190">Create Instructor pages</span></span>

<span data-ttu-id="8ad9e-191">本节搭建“讲师”页的基架，并向讲师“索引”页添加相关“课程”和“注册”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-191">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="8ad9e-192">![“讲师索引”页](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-192">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="8ad9e-193">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-193">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8ad9e-194">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-194">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="8ad9e-195">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-195">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8ad9e-196">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-196">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="8ad9e-197">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-197">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="8ad9e-198">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-198">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="8ad9e-199">用户选择一名讲师时，显示相关 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-199">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="8ad9e-200">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-200">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8ad9e-201">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-201">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="8ad9e-202">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-202">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="8ad9e-203">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-203">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="8ad9e-204">用户选择一门课程时，会显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-204">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="8ad9e-205">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-205">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="8ad9e-206">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-206">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="8ad9e-207">创建视图模型</span><span class="sxs-lookup"><span data-stu-id="8ad9e-207">Create a view model</span></span>

<span data-ttu-id="8ad9e-208">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-208">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="8ad9e-209">需要一个视图模型，该模型中包含表示三个表格的三个属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-209">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="8ad9e-210">使用以下代码创建 SchoolViewModels/InstructorIndexData.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-210">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="8ad9e-211">搭建“讲师”页的基架</span><span class="sxs-lookup"><span data-stu-id="8ad9e-211">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ad9e-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ad9e-212">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ad9e-213">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-213">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="8ad9e-214">创建“Pages/Instructors”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-214">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="8ad9e-215">将 `Instructor` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-215">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="8ad9e-216">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-216">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ad9e-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ad9e-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8ad9e-218">创建“Pages/Instructors”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-218">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="8ad9e-219">运行以下命令，搭建“讲师”页的基架。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-219">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="8ad9e-220">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-220">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="8ad9e-221">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="8ad9e-221">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ad9e-222">运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-222">Run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="8ad9e-223">使用以下代码更新 Pages/Instructors/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-223">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_all)]

<span data-ttu-id="8ad9e-224">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-224">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="8ad9e-225">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-225">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_query)]

<span data-ttu-id="8ad9e-226">代码指定以下导航属性的预先加载：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-226">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.Course`
    * `Course.Department`

<span data-ttu-id="8ad9e-227">选择讲师时（即 `id != null`），将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-227">The following code executes when an instructor is selected, that is, `id != null`.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_id)]

<span data-ttu-id="8ad9e-228">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-228">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8ad9e-229">向视图模型的 `Courses` 属性加载来自所选讲师 `Courses` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-229">The view model's `Courses` property is loaded with the `Course` entities from the selected instructor's `Courses` navigation property.</span></span>

<span data-ttu-id="8ad9e-230">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-230">The `Where` method returns a collection.</span></span> <span data-ttu-id="8ad9e-231">在这种情况下，筛选器将选择单个实体，因此会调用 `Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-231">In this case, the filter select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="8ad9e-232">`Instructor` 实体提供对 `Course` 导航属性的访问。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-232">The `Instructor` entity provides access to the `Course` navigation property.</span></span>

<span data-ttu-id="8ad9e-233">当集合仅包含一个项时，集合使用 <xref:System.Linq.Enumerable.Single%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-233">The <xref:System.Linq.Enumerable.Single%2A> method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="8ad9e-234">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-234">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="8ad9e-235">还可使用 <xref:System.Linq.Enumerable.SingleOrDefault%2A>，该方式在集合为空时返回默认值。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-235">An alternative is <xref:System.Linq.Enumerable.SingleOrDefault%2A>, which returns a default value if the collection is empty.</span></span> <span data-ttu-id="8ad9e-236">对于此查询，默认返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-236">For this query, `null` in the default returned.</span></span>

<span data-ttu-id="8ad9e-237">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-237">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_enrollment)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="8ad9e-238">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-238">Update the instructors Index page</span></span>

<span data-ttu-id="8ad9e-239">使用以下代码更新 Pages/Instructors/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-239">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Index.cshtml?highlight=1)]

<span data-ttu-id="8ad9e-240">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-240">The preceding code makes the following changes:</span></span>

  * <span data-ttu-id="8ad9e-241">将 `page` 指令更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-241">Updates the `page` directive to `@page "{id:int?}"`.</span></span> <span data-ttu-id="8ad9e-242">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-242">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="8ad9e-243">[路由模板](xref:fundamentals/routing#route-template-reference)将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-243">The [route template](xref:fundamentals/routing#route-template-reference) changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="8ad9e-244">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-244">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

    `https://localhost:5001/Instructors?id=2`

    <span data-ttu-id="8ad9e-245">如果页面指令为 `@page "{id:int?}"`，则 URL 为：`https://localhost:5001/Instructors/2`</span><span class="sxs-lookup"><span data-stu-id="8ad9e-245">When the page directive is `@page "{id:int?}"`, the URL is: `https://localhost:5001/Instructors/2`</span></span>

  * <span data-ttu-id="8ad9e-246">添加仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-246">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8ad9e-247">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-247">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

    ```html
    @if (item.OfficeAssignment != null)
    {
        @item.OfficeAssignment.Location
    }
    ```

  * <span data-ttu-id="8ad9e-248">添加显示每位讲师所授课程的“课程”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-248">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8ad9e-249">有关此 razor 语法的详细信息，请参阅[显式行转换](xref:mvc/views/razor#explicit-line-transition)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-249">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>
  * <span data-ttu-id="8ad9e-250">添加向所选讲师和课程的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-250">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="8ad9e-251">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-251">This sets a background color for the selected row using a Bootstrap class.</span></span>

    ```html
    string selectedRow = "";
    if (item.CourseID == Model.CourseID)
    {
        selectedRow = "success";
    }
    <tr class="@selectedRow">
    ```

  * <span data-ttu-id="8ad9e-252">添加标记为“选择”的新的超链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-252">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="8ad9e-253">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-253">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

    ```html
    <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
    ```

  * <span data-ttu-id="8ad9e-254">添加所选讲师的课程表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-254">Adds a table of courses for the selected Instructor.</span></span>
  * <span data-ttu-id="8ad9e-255">添加所选课程的学生注册表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-255">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="8ad9e-256">运行应用并选择“讲师”选项卡。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-256">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="8ad9e-257">如果 `OfficeAssignment` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-257">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="8ad9e-258">单击“选择”链接，选择讲师。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-258">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="8ad9e-259">显示行样式更改和分配给该讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-259">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="8ad9e-260">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-260">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index30.png)

## <a name="next-steps"></a><span data-ttu-id="8ad9e-262">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8ad9e-262">Next steps</span></span>

<span data-ttu-id="8ad9e-263">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-263">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="8ad9e-264">[上一个教程](xref:data/ef-rp/complex-data-model)
>[下一个教程](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-264">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="8ad9e-265">本教程介绍如何读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-265">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="8ad9e-266">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-266">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="8ad9e-267">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-267">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

![“讲师索引”页](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="8ad9e-270">预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="8ad9e-270">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="8ad9e-271">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-271">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8ad9e-272">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-272">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="8ad9e-273">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-273">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="8ad9e-274">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-274">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="8ad9e-275">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-275">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8ad9e-276">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-276">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="8ad9e-277">发布多个查询可能比发布大型的单个查询更为有效。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-277">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="8ad9e-278">预先加载通过 `Include` 和 `ThenInclude` 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-278">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="8ad9e-280">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-280">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="8ad9e-281">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="8ad9e-281">One query for the main query</span></span> 
  * <span data-ttu-id="8ad9e-282">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-282">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="8ad9e-283">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-283">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="8ad9e-284">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-284">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="8ad9e-285">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-285">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="8ad9e-287">**注意：** EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-287">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8ad9e-288">即使导航属性的数据非显式包含在内，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-288">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="8ad9e-289">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-289">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="8ad9e-290">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-290">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ad9e-291">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-291">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="8ad9e-292">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-292">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="8ad9e-293">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-293">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8ad9e-294">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-294">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8ad9e-295">例如：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-295">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8ad9e-297">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-297">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ad9e-298">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-298">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ad9e-299">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-299">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8ad9e-300">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-300">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="8ad9e-301">延迟加载可能会影响性能，例如，当开发人员使用 N+1 模式、加载父级和枚举子级时。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-301">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="8ad9e-302">创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-302">Create Course pages</span></span>

<span data-ttu-id="8ad9e-303">`Course` 实体包括一个带相关 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-303">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="8ad9e-305">若要显示课程的已分配院系的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-305">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="8ad9e-306">将相关的 `Department` 实体加载到 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-306">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="8ad9e-307">获取 `Department` 实体的 `Name` 属性中的名称。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-307">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="8ad9e-308">搭建“课程”页的基架</span><span class="sxs-lookup"><span data-stu-id="8ad9e-308">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ad9e-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ad9e-309">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ad9e-310">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-310">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="8ad9e-311">创建“Pages/Courses”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-311">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="8ad9e-312">将 `Course` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-312">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="8ad9e-313">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-313">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ad9e-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ad9e-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8ad9e-315">创建“Pages/Courses”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-315">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="8ad9e-316">运行以下命令，搭建“课程”页的基架。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-316">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="8ad9e-317">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-317">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="8ad9e-318">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="8ad9e-318">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="8ad9e-319">打开 Pages/Courses/Index.cshtml.cs 并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-319">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="8ad9e-320">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-320">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="8ad9e-321">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-321">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="8ad9e-322">运行应用并选择“课程”链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-322">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="8ad9e-323">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-323">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="8ad9e-324">显示院系名称</span><span class="sxs-lookup"><span data-stu-id="8ad9e-324">Display the department name</span></span>

<span data-ttu-id="8ad9e-325">使用以下代码更新 Pages/Courses/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-325">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="8ad9e-326">上述代码将 `Course` 属性更改为 `Courses`，然后添加 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-326">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="8ad9e-327">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-327">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="8ad9e-328">无需跟踪实体，因为未在当前的上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-328">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="8ad9e-329">使用以下代码更新 Pages/Courses/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-329">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="8ad9e-330">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-330">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="8ad9e-331">将 `Course` 属性名称更改为了 `Courses`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-331">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="8ad9e-332">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-332">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8ad9e-333">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-333">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8ad9e-334">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-334">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="8ad9e-335">更改“院系”列，显示院系名称。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-335">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8ad9e-336">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-336">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8ad9e-337">运行应用并选择“课程”选项卡，查看包含系名称的列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-337">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="8ad9e-339">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="8ad9e-339">Loading related data with Select</span></span>

<span data-ttu-id="8ad9e-340">`OnGetAsync` 方法使用 `Include` 方法加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-340">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="8ad9e-341">`Select` 方法是只加载所需相关数据的替代方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-341">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="8ad9e-342">对于单个项（如 `Department.Name`），它使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-342">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="8ad9e-343">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-343">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="8ad9e-344">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-344">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="8ad9e-345">上述代码不会返回任何实体类型，因此不进行任何跟踪。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-345">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="8ad9e-346">有关 EF 跟踪的详细信息，请参阅 [跟踪查询与非跟踪查询](/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-346">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="8ad9e-347">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-347">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="8ad9e-348">有关完整示例的信息，请参阅 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-348">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="8ad9e-349">创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-349">Create Instructor pages</span></span>

<span data-ttu-id="8ad9e-350">本节搭建“讲师”页的基架，并向讲师“索引”页添加相关“课程”和“注册”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-350">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="8ad9e-351">![“讲师索引”页](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-351">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="8ad9e-352">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-352">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8ad9e-353">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-353">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="8ad9e-354">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-354">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8ad9e-355">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-355">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="8ad9e-356">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-356">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="8ad9e-357">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-357">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="8ad9e-358">用户选择一名讲师时，显示相关 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-358">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="8ad9e-359">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-359">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8ad9e-360">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-360">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="8ad9e-361">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-361">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="8ad9e-362">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-362">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="8ad9e-363">用户选择一门课程时，会显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-363">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="8ad9e-364">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-364">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="8ad9e-365">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-365">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="8ad9e-366">创建视图模型</span><span class="sxs-lookup"><span data-stu-id="8ad9e-366">Create a view model</span></span>

<span data-ttu-id="8ad9e-367">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-367">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="8ad9e-368">需要一个视图模型，该模型中包含表示三个表格的三个属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-368">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="8ad9e-369">使用以下代码创建 SchoolViewModels/InstructorIndexData.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-369">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="8ad9e-370">搭建“讲师”页的基架</span><span class="sxs-lookup"><span data-stu-id="8ad9e-370">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ad9e-371">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ad9e-371">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ad9e-372">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-372">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="8ad9e-373">创建“Pages/Instructors”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-373">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="8ad9e-374">将 `Instructor` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-374">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="8ad9e-375">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-375">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ad9e-376">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ad9e-376">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8ad9e-377">创建“Pages/Instructors”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-377">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="8ad9e-378">运行以下命令，搭建“讲师”页的基架。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-378">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="8ad9e-379">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-379">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="8ad9e-380">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="8ad9e-380">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ad9e-381">若要在更新之前查看已搭建基架的页面的外观，则运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-381">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="8ad9e-382">使用以下代码更新 Pages/Instructors/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-382">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="8ad9e-383">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-383">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="8ad9e-384">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-384">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="8ad9e-385">代码指定以下导航属性的预先加载：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-385">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="8ad9e-386">注意 `CourseAssignments` 和 `Course` 对 `Include` 和 `ThenInclude` 方法的重复使用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-386">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="8ad9e-387">若要指定 `Course` 实体的两个导航属性的预先加载，则这种重复使用是必要的。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-387">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="8ad9e-388">选择讲师时 (`id != null`)，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-388">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="8ad9e-389">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-389">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8ad9e-390">向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-390">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="8ad9e-391">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-391">The `Where` method returns a collection.</span></span> <span data-ttu-id="8ad9e-392">但在这种情况下，筛选器将选择单个实体，因此会调用 `Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-392">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="8ad9e-393">`Instructor` 实体提供对 `CourseAssignments` 属性的访问。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-393">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="8ad9e-394">`CourseAssignments` 提供对相关 `Course` 实体的访问。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-394">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="8ad9e-396">当集合仅包含一个项时，集合使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-396">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="8ad9e-397">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-397">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="8ad9e-398">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-398">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="8ad9e-399">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-399">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="8ad9e-400">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-400">Update the instructors Index page</span></span>

<span data-ttu-id="8ad9e-401">使用以下代码更新 Pages/Instructors/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-401">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="8ad9e-402">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-402">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="8ad9e-403">将 `page` 指令从 `@page` 更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="8ad9e-404">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="8ad9e-405">路由模板将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="8ad9e-406">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="8ad9e-407">如果页面指令为 `@page "{id:int?}"` 时，则 URL 为：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-407">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="8ad9e-408">添加仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-408">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8ad9e-409">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="8ad9e-410">添加显示每位讲师所授课程的“课程”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-410">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8ad9e-411">有关此 razor 语法的详细信息，请参阅[显式行转换](xref:mvc/views/razor#explicit-line-transition)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-411">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="8ad9e-412">添加向所选讲师和课程的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-412">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="8ad9e-413">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="8ad9e-414">添加标记为“选择”的新的超链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-414">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="8ad9e-415">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="8ad9e-416">添加所选讲师的课程表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-416">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="8ad9e-417">添加所选课程的学生注册表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-417">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="8ad9e-418">运行应用并选择“讲师”选项卡。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="8ad9e-419">如果 `OfficeAssignment` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-419">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="8ad9e-420">单击“选择”链接，选择讲师。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-420">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="8ad9e-421">显示行样式更改和分配给该讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-421">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="8ad9e-422">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-422">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="8ad9e-424">使用 Single 方法</span><span class="sxs-lookup"><span data-stu-id="8ad9e-424">Using Single</span></span>

<span data-ttu-id="8ad9e-425">`Single` 方法可在 `Where` 条件中进行传递，无需分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-425">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="8ad9e-426">`Single` 与 Where 条件的配合使用与个人偏好相关。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-426">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="8ad9e-427">相较于使用 `Where` 方法，它没有提供任何优势。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-427">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="8ad9e-428">显式加载</span><span class="sxs-lookup"><span data-stu-id="8ad9e-428">Explicit loading</span></span>

<span data-ttu-id="8ad9e-429">当前代码为 `Enrollments` 和 `Students` 指定预先加载：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-429">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="8ad9e-430">假设用户几乎不希望课程中显示注册情况。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-430">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="8ad9e-431">在此情况下，可仅在请求时加载注册数据进行优化。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-431">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="8ad9e-432">在本部分中，会更新 `OnGetAsync` 以使用 `Enrollments` 和 `Students` 的显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-432">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="8ad9e-433">使用以下代码更新 Pages/Instructors/Index.cshtml.cs。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-433">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="8ad9e-434">上述代码取消针对注册和学生数据的 ThenInclude 方法调用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-434">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="8ad9e-435">如果已选中课程，则显式加载的代码会检索：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-435">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="8ad9e-436">所选课程的 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-436">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="8ad9e-437">每个 `Enrollment` 的 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-437">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="8ad9e-438">注意，上述代码注释掉了 `.AsNoTracking()`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-438">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="8ad9e-439">对于跟踪的实体，仅可显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-439">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="8ad9e-440">测试应用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-440">Test the app.</span></span> <span data-ttu-id="8ad9e-441">对用户而言，该应用的行为与上一版本相同。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-441">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ad9e-442">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8ad9e-442">Next steps</span></span>

<span data-ttu-id="8ad9e-443">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-443">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="8ad9e-444">[上一个教程](xref:data/ef-rp/complex-data-model)
>[下一个教程](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-444">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8ad9e-445">在本教程中，将读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-445">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="8ad9e-446">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-446">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="8ad9e-447">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-447">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="8ad9e-448">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-448">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8ad9e-449">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-449">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

![“讲师索引”页](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="8ad9e-452">相关数据的预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="8ad9e-452">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="8ad9e-453">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-453">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8ad9e-454">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-454">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="8ad9e-455">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-455">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="8ad9e-456">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-456">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="8ad9e-457">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-457">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8ad9e-458">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-458">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="8ad9e-459">与存在单一查询的 EF6 中的某些查询相比，发出多个查询可能更有效。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-459">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="8ad9e-460">预先加载通过 `Include` 和 `ThenInclude` 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-460">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="8ad9e-462">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-462">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="8ad9e-463">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="8ad9e-463">One query for the main query</span></span> 
  * <span data-ttu-id="8ad9e-464">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-464">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="8ad9e-465">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-465">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="8ad9e-466">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-466">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="8ad9e-467">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-467">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="8ad9e-469">注意：EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-469">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8ad9e-470">即使导航属性的数据非显式包含在内，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-470">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="8ad9e-471">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-471">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="8ad9e-472">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-472">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ad9e-473">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-473">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="8ad9e-474">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-474">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="8ad9e-475">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-475">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8ad9e-476">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-476">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8ad9e-477">例如：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-477">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8ad9e-479">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-479">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ad9e-480">[延迟加载已添加到版本 2.1 中的 EF Core](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-480">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="8ad9e-481">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-481">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8ad9e-482">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-482">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8ad9e-483">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-483">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="8ad9e-484">`Select` 运算符仅加载所需的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-484">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="8ad9e-485">创建显示院系名称的“课程”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-485">Create a Course page that displays department name</span></span>

<span data-ttu-id="8ad9e-486">课程实体包括一个带 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-486">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="8ad9e-487">`Department` 实体包含要分配课程的院系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-487">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="8ad9e-488">要在课程列表中显示已分配院系的名称：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-488">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="8ad9e-489">从 `Department` 实体中获取 `Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-489">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="8ad9e-490">`Department` 实体来自于 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-490">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="8ad9e-492">为课程模型创建基架</span><span class="sxs-lookup"><span data-stu-id="8ad9e-492">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ad9e-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ad9e-493">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="8ad9e-494">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-the-student-model)中的说明操作，并对模型类使用 `Course`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-494">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ad9e-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ad9e-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="8ad9e-496">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-496">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ad9e-497">上述命令为 `Course` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-497">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="8ad9e-498">在 Visual Studio 中打开项目。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-498">Open the project in Visual Studio.</span></span>

<span data-ttu-id="8ad9e-499">打开 Pages/Courses/Index.cshtml.cs 并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-499">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="8ad9e-500">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-500">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="8ad9e-501">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-501">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="8ad9e-502">运行应用并选择“课程”链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-502">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="8ad9e-503">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-503">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="8ad9e-504">使用以下代码更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-504">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="8ad9e-505">上述代码添加了 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-505">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="8ad9e-506">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-506">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="8ad9e-507">未跟踪实体，因为未在当前上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-507">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="8ad9e-508">使用以下突出显示的标记更新 Pages/Courses/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-508">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="8ad9e-509">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-509">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="8ad9e-510">将标题从“索引”更改为“课程”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-510">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="8ad9e-511">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-511">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8ad9e-512">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-512">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8ad9e-513">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-513">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="8ad9e-514">更改“院系”列，显示院系名称。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-514">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8ad9e-515">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-515">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8ad9e-516">运行应用并选择“课程”选项卡，查看包含系名称的列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-516">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="8ad9e-518">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="8ad9e-518">Loading related data with Select</span></span>

<span data-ttu-id="8ad9e-519">`OnGetAsync` 方法使用 `Include` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-519">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="8ad9e-520">`Select` 运算符仅加载所需的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-520">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="8ad9e-521">对于单个项（如 `Department.Name`），它使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-521">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="8ad9e-522">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-522">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="8ad9e-523">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-523">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="8ad9e-524">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-524">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="8ad9e-525">有关完整示例的信息，请参阅 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-525">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="8ad9e-526">创建显示“课程”和“注册”的“讲师”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-526">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="8ad9e-527">在本部分中，将创建“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-527">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="8ad9e-528">![“讲师索引”页](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-528">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="8ad9e-529">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-529">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8ad9e-530">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-530">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="8ad9e-531">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-531">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8ad9e-532">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-532">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="8ad9e-533">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-533">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="8ad9e-534">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-534">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="8ad9e-535">当用户选择一名讲师（上图中的 Harui）时，显示相关的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-535">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="8ad9e-536">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-536">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8ad9e-537">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-537">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="8ad9e-538">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-538">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="8ad9e-539">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-539">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="8ad9e-540">当用户选择一门课程（上图中的化学）时，显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-540">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="8ad9e-541">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-541">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="8ad9e-542">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-542">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="8ad9e-543">创建“讲师索引”视图的视图模型</span><span class="sxs-lookup"><span data-stu-id="8ad9e-543">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="8ad9e-544">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-544">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="8ad9e-545">创建一个视图模型，该模型中包含表示三个表格的三个实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-545">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="8ad9e-546">在 SchoolViewModels 文件夹中，使用以下代码创建 InstructorIndexData.cs：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-546">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="8ad9e-547">为讲师模型创建基架</span><span class="sxs-lookup"><span data-stu-id="8ad9e-547">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ad9e-548">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ad9e-548">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="8ad9e-549">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-the-student-model)中的说明操作，并对模型类使用 `Instructor`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-549">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8ad9e-550">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ad9e-550">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="8ad9e-551">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-551">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="8ad9e-552">上述命令为 `Instructor` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-552">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="8ad9e-553">运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-553">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="8ad9e-554">将 Pages/Instructors/Index.cshtml.cs 替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-554">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="8ad9e-555">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-555">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="8ad9e-556">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-556">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="8ad9e-557">查询包括两项内容：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-557">The query has two includes:</span></span>

* <span data-ttu-id="8ad9e-558">`OfficeAssignment`：在[讲师视图](#IP)中显示。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-558">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="8ad9e-559">`CourseAssignments`：课程的教学内容。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-559">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="8ad9e-560">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="8ad9e-560">Update the instructors Index page</span></span>

<span data-ttu-id="8ad9e-561">使用以下标记更新 Pages/Instructors/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-561">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="8ad9e-562">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-562">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="8ad9e-563">将 `page` 指令从 `@page` 更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-563">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="8ad9e-564">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-564">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="8ad9e-565">路由模板将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-565">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="8ad9e-566">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-566">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="8ad9e-567">当页面指令是 `@page "{id:int?}"` 时，之前的 URL 为：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-567">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="8ad9e-568">页标题为“讲师”。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-568">Page title is **Instructors**.</span></span>
* <span data-ttu-id="8ad9e-569">添加了仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-569">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8ad9e-570">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-570">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="8ad9e-571">添加了显示每位讲师所授课程的“课程”列。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-571">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8ad9e-572">有关此 razor 语法的详细信息，请参阅[显式行转换](xref:mvc/views/razor#explicit-line-transition)。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-572">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="8ad9e-573">添加了向所选讲师的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-573">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="8ad9e-574">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-574">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="8ad9e-575">添加了标记为“选择”的新的超链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-575">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="8ad9e-576">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-576">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="8ad9e-577">运行应用并选择“讲师”选项卡。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-577">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="8ad9e-578">如果 OfficeAssignment\` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-578">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="8ad9e-579">单击“选择”链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-579">Click on the **Select** link.</span></span> <span data-ttu-id="8ad9e-580">随即更改行样式。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-580">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="8ad9e-581">添加由所选讲师教授的课程</span><span class="sxs-lookup"><span data-stu-id="8ad9e-581">Add courses taught by selected instructor</span></span>

<span data-ttu-id="8ad9e-582">将 Pages/Instructors/Index.cshtml.cs 中的 `OnGetAsync` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-582">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="8ad9e-583">添加 `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="8ad9e-583">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="8ad9e-584">检查更新后的查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-584">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="8ad9e-585">先前查询添加了 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-585">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="8ad9e-586">选择讲师时 (`id != null`)，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-586">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="8ad9e-587">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-587">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8ad9e-588">向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-588">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="8ad9e-589">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-589">The `Where` method returns a collection.</span></span> <span data-ttu-id="8ad9e-590">在前面的 `Where` 方法中，仅返回单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-590">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="8ad9e-591">`Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-591">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="8ad9e-592">`Instructor` 实体提供对 `CourseAssignments` 属性的访问。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-592">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="8ad9e-593">`CourseAssignments` 提供对相关 `Course` 实体的访问。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-593">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="8ad9e-595">当集合仅包含一个项时，集合使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-595">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="8ad9e-596">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-596">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="8ad9e-597">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-597">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="8ad9e-598">在空集合上使用 `SingleOrDefault`：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-598">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="8ad9e-599">引发异常（因为尝试在空引用上找到 `Courses` 属性）。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-599">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="8ad9e-600">异常信息不太能清楚指出问题原因。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-600">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="8ad9e-601">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-601">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="8ad9e-602">在 Pages/Instructors/Index.cshtml Razor 页面末尾添加以下标记：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-602">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="8ad9e-603">上述标记显示选中某讲师时与该讲师相关的课程列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-603">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="8ad9e-604">测试应用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-604">Test the app.</span></span> <span data-ttu-id="8ad9e-605">单击讲师页面上的“选择”链接。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-605">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="8ad9e-606">显示学生数据</span><span class="sxs-lookup"><span data-stu-id="8ad9e-606">Show student data</span></span>

<span data-ttu-id="8ad9e-607">在本部分中，更新应用以显示所选课程的学生数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-607">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="8ad9e-608">使用以下代码在 Pages/Instructors/Index.cshtml.cs 中更新 `OnGetAsync` 方法中的查询：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-608">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="8ad9e-609">更新 Pages/Instructors/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-609">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="8ad9e-610">在文件末尾添加以下标记：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-610">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="8ad9e-611">上述标记显示已注册所选课程的学生列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-611">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="8ad9e-612">刷新页面并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-612">Refresh the page and select an instructor.</span></span> <span data-ttu-id="8ad9e-613">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-613">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="8ad9e-615">使用 Single 方法</span><span class="sxs-lookup"><span data-stu-id="8ad9e-615">Using Single</span></span>

<span data-ttu-id="8ad9e-616">`Single` 方法可在 `Where` 条件中进行传递，无需分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-616">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="8ad9e-617">使用 `Where` 时，前面的 `Single` 方法不适用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-617">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="8ad9e-618">一些开发人员更喜欢 `Single` 方法样式。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-618">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="8ad9e-619">显式加载</span><span class="sxs-lookup"><span data-stu-id="8ad9e-619">Explicit loading</span></span>

<span data-ttu-id="8ad9e-620">当前代码为 `Enrollments` 和 `Students` 指定预先加载：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-620">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="8ad9e-621">假设用户几乎不希望课程中显示注册情况。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-621">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="8ad9e-622">在此情况下，可仅在请求时加载注册数据进行优化。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-622">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="8ad9e-623">在本部分中，会更新 `OnGetAsync` 以使用 `Enrollments` 和 `Students` 的显式加载。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-623">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="8ad9e-624">使用以下代码更新 `OnGetAsync`：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-624">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="8ad9e-625">上述代码取消针对注册和学生数据的 ThenInclude 方法调用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-625">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="8ad9e-626">如果已选中课程，则突出显示的代码会检索：</span><span class="sxs-lookup"><span data-stu-id="8ad9e-626">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="8ad9e-627">所选课程的 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-627">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="8ad9e-628">每个 `Enrollment` 的 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-628">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="8ad9e-629">请注意，上述代码为 `.AsNoTracking()` 加上注释。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-629">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="8ad9e-630">对于跟踪的实体，仅可显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-630">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="8ad9e-631">测试应用。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-631">Test the app.</span></span> <span data-ttu-id="8ad9e-632">对用户而言，该应用的行为与上一版本相同。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-632">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="8ad9e-633">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="8ad9e-633">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8ad9e-634">其他资源</span><span class="sxs-lookup"><span data-stu-id="8ad9e-634">Additional resources</span></span>

* [<span data-ttu-id="8ad9e-635">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="8ad9e-635">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="8ad9e-636">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="8ad9e-636">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="8ad9e-637">[上一页](xref:data/ef-rp/complex-data-model)
>[下一页](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="8ad9e-637">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end