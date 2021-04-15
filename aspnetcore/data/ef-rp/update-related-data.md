---
title: 第 7 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 更新相关数据
author: rick-anderson
description: Razor 页面和实体框架教程系列的第 7 部分。
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 14080174d35326aa4f412fa0ecd106b5b1bac7c4
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957582"
---
# <a name="part-7-razor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="e164b-103">第 7 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 更新相关数据</span><span class="sxs-lookup"><span data-stu-id="e164b-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="e164b-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e164b-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e164b-105">本教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="e164b-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="e164b-106">下图显示了部分已完成页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="e164b-107">![课程“编辑”页](update-related-data/_static/course-edit30.png)
![讲师”编辑”页](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="e164b-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="e164b-108">更新课程“创建”和“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e164b-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="e164b-109">课程“创建”和“编辑”页的基架搭建代码具有显示 `DepartmentID` (`int`) 的“院系”下拉列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows `DepartmentID`, an `int`.</span></span> <span data-ttu-id="e164b-110">下拉列表应显示院系名称，因此这两个页面都需要院系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="e164b-111">若要提供该列表，请使用“创建”和“编辑”页的基类。</span><span class="sxs-lookup"><span data-stu-id="e164b-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="e164b-112">创建课程“创建”和“编辑”的基类</span><span class="sxs-lookup"><span data-stu-id="e164b-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="e164b-113">使用以下代码创建 Pages/Courses/DepartmentNamePageModel.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="e164b-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="e164b-114">上面的代码创建 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) 以包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e164b-115">如果指定了 `selectedDepartment`，可在 `SelectList` 中选择该系。</span><span class="sxs-lookup"><span data-stu-id="e164b-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e164b-116">“创建”和“编辑”页模型类将派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e164b-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="e164b-117">更新课程“创建”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-117">Update the Course Create page model</span></span>

<span data-ttu-id="e164b-118">课程分配给院系。</span><span class="sxs-lookup"><span data-stu-id="e164b-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="e164b-119">“创建”和“编辑”页的基类提供 `SelectList`，用于选择院系。</span><span class="sxs-lookup"><span data-stu-id="e164b-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="e164b-120">采用 `SelectList` 的下拉列表设置 `Course.DepartmentID` 外键 (FK) 属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e164b-121">EF Core 使用 `Course.DepartmentID` FK 加载 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![创建课程](update-related-data/_static/ddl30.png)

<span data-ttu-id="e164b-123">使用以下代码更新 Pages/Courses/Create.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[loc comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e164b-124">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-124">The preceding code:</span></span>

* <span data-ttu-id="e164b-125">派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e164b-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e164b-126">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e164b-126">Uses <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e164b-127">删除 `ViewData["DepartmentID"]`。</span><span class="sxs-lookup"><span data-stu-id="e164b-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="e164b-128">`DepartmentNameSL` `SelectList` 是强类型模型，将用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-128">The `DepartmentNameSL` `SelectList` is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="e164b-129">建议使用强类型而非弱类型。</span><span class="sxs-lookup"><span data-stu-id="e164b-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e164b-130">有关详细信息，请参阅[弱类型数据（ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e164b-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="e164b-131">更新“课程创建”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="e164b-132">使用以下代码更新 Pages/Courses/Create.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e164b-133">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-134">将标题从“DepartmentID”更改为“Department” 。</span><span class="sxs-lookup"><span data-stu-id="e164b-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e164b-135">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e164b-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e164b-136">添加“选择系”选项。</span><span class="sxs-lookup"><span data-stu-id="e164b-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="e164b-137">如果尚未选择院系（而不是已选中首个院系），此更改将在下拉列表中显示“选择院系”。</span><span class="sxs-lookup"><span data-stu-id="e164b-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="e164b-138">在未选择系时添加验证消息。</span><span class="sxs-lookup"><span data-stu-id="e164b-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e164b-139">Razor 页面使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)：</span><span class="sxs-lookup"><span data-stu-id="e164b-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e164b-140">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-140">Test the Create page.</span></span> <span data-ttu-id="e164b-141">“创建”页显示系名称，而不是系 ID。</span><span class="sxs-lookup"><span data-stu-id="e164b-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="e164b-142">更新课程“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-142">Update the Course Edit page model</span></span>

<span data-ttu-id="e164b-143">使用以下代码更新 Pages/Courses/Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="e164b-144">这些更改与在“创建”页模型中所做的更改相似。</span><span class="sxs-lookup"><span data-stu-id="e164b-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e164b-145">在上面的代码中，`PopulateDepartmentsDropDownList` 在院系 ID 中传递并将在下拉列表中选择该院系。</span><span class="sxs-lookup"><span data-stu-id="e164b-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="e164b-146">更新“课程编辑”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="e164b-147">使用以下代码更新 Pages/Courses/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e164b-148">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-149">显示课程 ID。</span><span class="sxs-lookup"><span data-stu-id="e164b-149">Displays the course ID.</span></span> <span data-ttu-id="e164b-150">通常不显示实体的主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e164b-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e164b-151">PK 对用户不具有任何意义。</span><span class="sxs-lookup"><span data-stu-id="e164b-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e164b-152">在这种情况下，PK 就是课程编号。</span><span class="sxs-lookup"><span data-stu-id="e164b-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e164b-153">将“院系”下拉列表的标题从“DepartmentID”更改为“Department” 。</span><span class="sxs-lookup"><span data-stu-id="e164b-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e164b-154">将 `"ViewBag.DepartmentID"` 替换为基类中的 `DepartmentNameSL`。</span><span class="sxs-lookup"><span data-stu-id="e164b-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL`, which is in the base class.</span></span>

<span data-ttu-id="e164b-155">该页面包含课程编号的隐藏域 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e164b-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e164b-156">添加具有 `asp-for="Course.CourseID"` 的 `<label>` 标记帮助器也同样需要隐藏域。</span><span class="sxs-lookup"><span data-stu-id="e164b-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e164b-157">用户选择“保存”时，需要 `<input type="hidden">`，以便在已发布的数据中包括课程编号。</span><span class="sxs-lookup"><span data-stu-id="e164b-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user selects **Save**.</span></span>

## <a name="update-the-course-page-models"></a><span data-ttu-id="e164b-158">更新“课程”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-158">Update the Course page models</span></span>

<span data-ttu-id="e164b-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以在不需要跟踪时提高性能。</span><span class="sxs-lookup"><span data-stu-id="e164b-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

<span data-ttu-id="e164b-160">通过在 `OnGetAsync` 方法中添加 `AsNoTracking`，更新 Pages/Courses/Delete.cshtml.cs 和 ages/Courses/Details.cshtml.cs： </span><span class="sxs-lookup"><span data-stu-id="e164b-160">Update *Pages/Courses/Delete.cshtml.cs* and *Pages/Courses/Details.cshtml.cs* by adding `AsNoTracking` to the `OnGetAsync` methods:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/Delete.cshtml.cs?highlight=8-11&name=snippet)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="e164b-161">更新“课程”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-161">Update the Course Razor pages</span></span>

<span data-ttu-id="e164b-162">使用以下代码更新 Pages/Courses/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-162">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="e164b-163">对“详细信息”页执行相同更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-163">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="e164b-164">测试“课程”页</span><span class="sxs-lookup"><span data-stu-id="e164b-164">Test the Course pages</span></span>

<span data-ttu-id="e164b-165">测试“创建”、“编辑”、“详细信息”和“删除”页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-165">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="e164b-166">更新讲师“创建”和“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e164b-166">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="e164b-167">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-167">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e164b-168">下图显示包含一系列课程复选框的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-168">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![带课程信息的讲师“编辑”页](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="e164b-170">通过复选框可对分配给讲师的课程进行更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-170">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e164b-171">数据库中的每一门课程均有对应显示的复选框。</span><span class="sxs-lookup"><span data-stu-id="e164b-171">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="e164b-172">已分配给讲师的课程将会被选中。</span><span class="sxs-lookup"><span data-stu-id="e164b-172">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="e164b-173">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-173">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="e164b-174">如果课程数过多，另一个 UI 的使用效果可能更好。</span><span class="sxs-lookup"><span data-stu-id="e164b-174">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="e164b-175">但此处所示的用于管理多对多关系的方法不会发生变化。</span><span class="sxs-lookup"><span data-stu-id="e164b-175">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="e164b-176">若要创建或删除关系，则需要使用联接实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-176">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="e164b-177">为已分配的课程数据创建类</span><span class="sxs-lookup"><span data-stu-id="e164b-177">Create a class for assigned courses data</span></span>

<span data-ttu-id="e164b-178">使用以下代码创建 SchoolViewModels/AssignedCourseData.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-178">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e164b-179">`AssignedCourseData` 类包含的数据可用于为已分配给讲师的课程创建复选框。</span><span class="sxs-lookup"><span data-stu-id="e164b-179">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="e164b-180">创建“讲师”页模型基类</span><span class="sxs-lookup"><span data-stu-id="e164b-180">Create an Instructor page model base class</span></span>

<span data-ttu-id="e164b-181">创建 Pages/Instructors/InstructorCoursesPageModel.cs 基类：</span><span class="sxs-lookup"><span data-stu-id="e164b-181">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="e164b-182">`InstructorCoursesPageModel` 是用于“编辑”和“创建”页模型的基类。</span><span class="sxs-lookup"><span data-stu-id="e164b-182">The `InstructorCoursesPageModel` is the base class for the Edit and Create page models.</span></span> <span data-ttu-id="e164b-183">`PopulateAssignedCourseData` 读取所有 `Course` 实体以填充 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e164b-183">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e164b-184">该代码将设置每门课程的 `CourseID` 和标题，并决定是否为讲师分配该课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-184">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e164b-185">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用于高效查找。</span><span class="sxs-lookup"><span data-stu-id="e164b-185">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

### <a name="handle-office-location"></a><span data-ttu-id="e164b-186">处理办公室位置</span><span class="sxs-lookup"><span data-stu-id="e164b-186">Handle office location</span></span>

<span data-ttu-id="e164b-187">“编辑”页必须处理的另一个关系是 Instructor 实体与 `OfficeAssignment` 实体之间的一对零或一对一关系。</span><span class="sxs-lookup"><span data-stu-id="e164b-187">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e164b-188">讲师编辑代码必须处理以下场景：</span><span class="sxs-lookup"><span data-stu-id="e164b-188">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="e164b-189">如果用户清除办公室分配，则需删除 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-189">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e164b-190">如果用户输入办公室分配，且办公室分配原本为空，则需创建一个新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-190">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e164b-191">如果用户更改办公室分配，则需更新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-191">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

## <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="e164b-192">更新讲师“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-192">Update the Instructor Edit page model</span></span>

<span data-ttu-id="e164b-193">使用以下代码更新 Pages/Instructors/Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-193">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="e164b-194">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-194">The preceding code:</span></span>

* <span data-ttu-id="e164b-195">使用 `OfficeAssignment`、`CourseAssignment` 和 `CourseAssignment.Course` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-195">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="e164b-196">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-196">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e164b-197"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 可防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e164b-197"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e164b-198">如果办公室位置为空，则将 `Instructor.OfficeAssignment` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="e164b-198">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e164b-199">当 `Instructor.OfficeAssignment` 为 null 时，`OfficeAssignment` 表中的相关行将会删除。</span><span class="sxs-lookup"><span data-stu-id="e164b-199">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="e164b-200">调用 `OnGetAsync` 中的 `PopulateAssignedCourseData`，使用 `AssignedCourseData` 视图模型类为复选框提供信息。</span><span class="sxs-lookup"><span data-stu-id="e164b-200">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="e164b-201">调用 `OnPostAsync` 中的 `UpdateInstructorCourses`，将复选框中的信息应用于将要编辑的 Instructor 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-201">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="e164b-202">如果 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 失败，则调用 `OnPostAsync` 中的 `PopulateAssignedCourseData` 和 `UpdateInstructorCourses`。</span><span class="sxs-lookup"><span data-stu-id="e164b-202">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> fails.</span></span> <span data-ttu-id="e164b-203">这些方法调用将在页面重新显示错误消息时还原页面上所输入的已分配课程数据。</span><span class="sxs-lookup"><span data-stu-id="e164b-203">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

<span data-ttu-id="e164b-204">Razor 页面没有 Course 实体的集合，因此模型绑定器无法自动更新 `Courses` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-204">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `Courses` navigation property.</span></span> <span data-ttu-id="e164b-205">可在新的 `UpdateInstructorCourses` 方法中更新 `Courses` 导航属性，而不必使用模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="e164b-205">Instead of using the model binder to update the `Courses` navigation property, that's done in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e164b-206">为此，需要从模型绑定中排除 `Courses` 属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-206">Therefore you need to exclude the `Courses` property from model binding.</span></span> <span data-ttu-id="e164b-207">此操作无需对调用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 的代码进行任何更改，因为使用的重载包含已声明的属性，并且 `Courses` 不包括在该列表中。</span><span class="sxs-lookup"><span data-stu-id="e164b-207">This doesn't require any change to the code that calls <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> because you're using the overload with declared properties and `Courses` isn't in the include list.</span></span>

<span data-ttu-id="e164b-208">如果未选中任何复选框，则 `UpdateInstructorCourses` 中的代码将使用空集合初始化 `instructorToUpdate.Courses`，并返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="e164b-208">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `instructorToUpdate.Courses` with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_IfNull)]

<span data-ttu-id="e164b-209">之后，代码会循环访问数据库中的所有课程，并逐一检查当前分配给讲师的课程和页面中处于选中状态的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-209">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="e164b-210">为便于高效查找，后两个集合存储在 `HashSet` 对象中。</span><span class="sxs-lookup"><span data-stu-id="e164b-210">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e164b-211">如果某课程的复选框处于选中状态，但该课程不在 `Instructor.Courses` 导航属性中，则会将该课程添加到导航属性中的集合中。</span><span class="sxs-lookup"><span data-stu-id="e164b-211">If the check box for a course is selected but the course is ***not*** in the `Instructor.Courses` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="e164b-212">如果某课程的复选框未处于选中状态，但该课程存在 `Instructor.Courses` 导航属性中，则会从导航属性中删除该课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-212">If the check box for a course is ***not*** selected, but the course is in the `Instructor.Courses` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_UpdateCoursesElse)]

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="e164b-213">更新“讲师编辑”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-213">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="e164b-214">使用以下代码更新 Pages/Instructors/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-214">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="e164b-215">上面的代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="e164b-215">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e164b-216">每列均具有一个复选框和包含课程编号及标题的标题。</span><span class="sxs-lookup"><span data-stu-id="e164b-216">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="e164b-217">所有复选框均具有相同的名称（“selectedCourses”）。</span><span class="sxs-lookup"><span data-stu-id="e164b-217">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e164b-218">使用相同名称可指示模型绑定器将它们视为一个组。</span><span class="sxs-lookup"><span data-stu-id="e164b-218">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e164b-219">每个复选框的值特性都设置为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e164b-219">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="e164b-220">发布页面时，模型绑定器会传递一个数组，该数组只包括所选复选框的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e164b-220">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="e164b-221">初次呈现复选框时，分配给讲师的课程均已选中。</span><span class="sxs-lookup"><span data-stu-id="e164b-221">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="e164b-222">注意：此处所使用的编辑讲师课程数据的方法适用于数量有限的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-222">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e164b-223">对于规模远大于此的集合，则使用不同的 UI 和不同的更新方法会更实用和更高效。</span><span class="sxs-lookup"><span data-stu-id="e164b-223">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="e164b-224">运行应用并测试更新的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-224">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="e164b-225">更改某些课程分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-225">Change some course assignments.</span></span> <span data-ttu-id="e164b-226">这些更改将反映在“索引”页上。</span><span class="sxs-lookup"><span data-stu-id="e164b-226">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="e164b-227">更新讲师“创建”页</span><span class="sxs-lookup"><span data-stu-id="e164b-227">Update the Instructor Create page</span></span>

<span data-ttu-id="e164b-228">使用类似于“编辑”页面的代码更新“讲师创建”页面模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-228">Update the Instructor Create page model and with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Create.cshtml.cs?name=snippet_all)]

<span data-ttu-id="e164b-229">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-229">The preceding code:</span></span>

  * <span data-ttu-id="e164b-230">添加[日志记录](xref:fundamentals/logging/index)以记录警告和错误消息。</span><span class="sxs-lookup"><span data-stu-id="e164b-230">Adds [logging](xref:fundamentals/logging/index) for warning and error messages.</span></span>
  * <span data-ttu-id="e164b-231">调用 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>，它将通过一个数据库调用提取所有课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-231">Calls <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>, which fetches all the Courses in one database call.</span></span> <span data-ttu-id="e164b-232">将 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A> 用于小型集合时，可优化性能。</span><span class="sxs-lookup"><span data-stu-id="e164b-232">For small collections this is an optimization when using <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A>.</span></span> <span data-ttu-id="e164b-233">无需发出数据库请求，`FindAsync` 就会返回跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-233">`FindAsync` returns the tracked entity without a request to the database.</span></span>

    [!code-csharp[](intro/samples/cu50/Pages/Instructors/Create.cshtml.cs?name=snippet_find&highlight=9,15,34)]

  * <span data-ttu-id="e164b-234">`_context.Instructors.Add(newInstructor)` 利用[多对多关系](/ef/core/modeling/relationships#many-to-many)创建新的 `Instructor`，而无需显式映射联接表。</span><span class="sxs-lookup"><span data-stu-id="e164b-234">`_context.Instructors.Add(newInstructor)` creates a new `Instructor` using [many-to-many](/ef/core/modeling/relationships#many-to-many) relationships without explicitly mapping the join table.</span></span> <span data-ttu-id="e164b-235">[EF 5.0 中添加了多对多关系](/ef/core/what-is-new/ef-core-5.0/whatsnew)。</span><span class="sxs-lookup"><span data-stu-id="e164b-235">[Many-to-many was added in EF 5.0](/ef/core/what-is-new/ef-core-5.0/whatsnew).</span></span>

<span data-ttu-id="e164b-236">测试讲师“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-236">Test the instructor Create page.</span></span>

<span data-ttu-id="e164b-237">使用类似于“编辑”页面的代码更新“讲师创建”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e164b-237">Update the Instructor Create Razor page with code similar to the Edit page:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Create.cshtml)]

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="e164b-238">更新讲师“删除”页</span><span class="sxs-lookup"><span data-stu-id="e164b-238">Update the Instructor Delete page</span></span>

<span data-ttu-id="e164b-239">使用以下代码更新 Pages/Instructors/Delete.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-239">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="e164b-240">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-240">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-241">对 `Courses` 导航属性使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="e164b-241">Uses eager loading for the `Courses` navigation property.</span></span> <span data-ttu-id="e164b-242">必须包含 `Courses`，否则删除讲师时将不会删除课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-242">`Courses` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e164b-243">为避免阅读它们，可以在数据库中配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e164b-243">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e164b-244">如果要删除的讲师被指派为任何系的管理员，则需从这些系中删除该讲师分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-244">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="e164b-245">运行应用并测试“删除”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-245">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e164b-246">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e164b-246">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e164b-247">[上一个教程](xref:data/ef-rp/read-related-data)
> [下一个教程](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e164b-247">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"


<span data-ttu-id="e164b-248">本教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="e164b-248">This tutorial shows how to update related data.</span></span> <span data-ttu-id="e164b-249">下图显示了部分已完成页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-249">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="e164b-250">![课程“编辑”页](update-related-data/_static/course-edit30.png)
![讲师”编辑”页](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="e164b-250">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="e164b-251">更新课程“创建”和“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e164b-251">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="e164b-252">课程“创建”和“编辑”页的基架搭建代码具有显示院系 ID（整数）的“院系”下拉列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-252">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="e164b-253">下拉列表应显示院系名称，因此这两个页面都需要院系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-253">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="e164b-254">若要提供该列表，请使用“创建”和“编辑”页的基类。</span><span class="sxs-lookup"><span data-stu-id="e164b-254">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="e164b-255">创建课程“创建”和“编辑”的基类</span><span class="sxs-lookup"><span data-stu-id="e164b-255">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="e164b-256">使用以下代码创建 Pages/Courses/DepartmentNamePageModel.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="e164b-256">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="e164b-257">上面的代码创建 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) 以包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-257">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e164b-258">如果指定了 `selectedDepartment`，可在 `SelectList` 中选择该系。</span><span class="sxs-lookup"><span data-stu-id="e164b-258">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e164b-259">“创建”和“编辑”页模型类将派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e164b-259">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="e164b-260">更新课程“创建”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-260">Update the Course Create page model</span></span>

<span data-ttu-id="e164b-261">课程分配给院系。</span><span class="sxs-lookup"><span data-stu-id="e164b-261">A Course is assigned to a Department.</span></span> <span data-ttu-id="e164b-262">“创建”和“编辑”页的基类提供 `SelectList`，用于选择院系。</span><span class="sxs-lookup"><span data-stu-id="e164b-262">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="e164b-263">采用 `SelectList` 的下拉列表设置 `Course.DepartmentID` 外键 (FK) 属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-263">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e164b-264">EF Core 使用 `Course.DepartmentID` FK 加载 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-264">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![创建课程](update-related-data/_static/ddl30.png)

<span data-ttu-id="e164b-266">使用以下代码更新 Pages/Courses/Create.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-266">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e164b-267">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-267">The preceding code:</span></span>

* <span data-ttu-id="e164b-268">派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e164b-268">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e164b-269">使用 `TryUpdateModelAsync` 防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e164b-269">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e164b-270">删除 `ViewData["DepartmentID"]`。</span><span class="sxs-lookup"><span data-stu-id="e164b-270">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="e164b-271">基类中的 `DepartmentNameSL` 是强类型模型，将用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-271">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="e164b-272">建议使用强类型而非弱类型。</span><span class="sxs-lookup"><span data-stu-id="e164b-272">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e164b-273">有关详细信息，请参阅[弱类型数据（ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e164b-273">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="e164b-274">更新“课程创建”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-274">Update the Course Create Razor page</span></span>

<span data-ttu-id="e164b-275">使用以下代码更新 Pages/Courses/Create.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-275">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e164b-276">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-276">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-277">将标题从“DepartmentID”更改为“Department” 。</span><span class="sxs-lookup"><span data-stu-id="e164b-277">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e164b-278">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e164b-278">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e164b-279">添加“选择系”选项。</span><span class="sxs-lookup"><span data-stu-id="e164b-279">Adds the "Select Department" option.</span></span> <span data-ttu-id="e164b-280">如果尚未选择院系（而不是已选中首个院系），此更改将在下拉列表中显示“选择院系”。</span><span class="sxs-lookup"><span data-stu-id="e164b-280">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="e164b-281">在未选择系时添加验证消息。</span><span class="sxs-lookup"><span data-stu-id="e164b-281">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e164b-282">Razor 页面使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)：</span><span class="sxs-lookup"><span data-stu-id="e164b-282">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e164b-283">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-283">Test the Create page.</span></span> <span data-ttu-id="e164b-284">“创建”页显示系名称，而不是系 ID。</span><span class="sxs-lookup"><span data-stu-id="e164b-284">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="e164b-285">更新课程“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-285">Update the Course Edit page model</span></span>

<span data-ttu-id="e164b-286">使用以下代码更新 Pages/Courses/Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-286">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="e164b-287">这些更改与在“创建”页模型中所做的更改相似。</span><span class="sxs-lookup"><span data-stu-id="e164b-287">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e164b-288">在上面的代码中，`PopulateDepartmentsDropDownList` 在院系 ID 中传递并将在下拉列表中选择该院系。</span><span class="sxs-lookup"><span data-stu-id="e164b-288">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="e164b-289">更新“课程编辑”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-289">Update the Course Edit Razor page</span></span>

<span data-ttu-id="e164b-290">使用以下代码更新 Pages/Courses/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-290">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e164b-291">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-291">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-292">显示课程 ID。</span><span class="sxs-lookup"><span data-stu-id="e164b-292">Displays the course ID.</span></span> <span data-ttu-id="e164b-293">通常不显示实体的主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e164b-293">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e164b-294">PK 对用户不具有任何意义。</span><span class="sxs-lookup"><span data-stu-id="e164b-294">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e164b-295">在这种情况下，PK 就是课程编号。</span><span class="sxs-lookup"><span data-stu-id="e164b-295">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e164b-296">将“院系”下拉列表的标题从“DepartmentID”更改为“Department” 。</span><span class="sxs-lookup"><span data-stu-id="e164b-296">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e164b-297">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e164b-297">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e164b-298">该页面包含课程编号的隐藏域 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e164b-298">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e164b-299">添加具有 `asp-for="Course.CourseID"` 的 `<label>` 标记帮助器也同样需要隐藏域。</span><span class="sxs-lookup"><span data-stu-id="e164b-299">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e164b-300">用户单击“保存”时，需要 `<input type="hidden">`，以便在已发布的数据中包括课程编号。</span><span class="sxs-lookup"><span data-stu-id="e164b-300">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="e164b-301">更新课程“详细信息”和“删除”页</span><span class="sxs-lookup"><span data-stu-id="e164b-301">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="e164b-302">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以在不需要跟踪时提高性能。</span><span class="sxs-lookup"><span data-stu-id="e164b-302">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="e164b-303">更新“课程”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-303">Update the Course page models</span></span>

<span data-ttu-id="e164b-304">使用以下代码更新 Pages/Courses/Delete.cshtml.cs 以添加 `AsNoTracking`：</span><span class="sxs-lookup"><span data-stu-id="e164b-304">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="e164b-305">在 Pages/Courses/Details.cshtml.cs 文件中进行相同的更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-305">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="e164b-306">更新“课程”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-306">Update the Course Razor pages</span></span>

<span data-ttu-id="e164b-307">使用以下代码更新 Pages/Courses/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-307">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="e164b-308">对“详细信息”页执行相同更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-308">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="e164b-309">测试“课程”页</span><span class="sxs-lookup"><span data-stu-id="e164b-309">Test the Course pages</span></span>

<span data-ttu-id="e164b-310">测试“创建”、“编辑”、“详细信息”和“删除”页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-310">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="e164b-311">更新讲师“创建”和“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e164b-311">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="e164b-312">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-312">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e164b-313">下图显示包含一系列课程复选框的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-313">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![带课程信息的讲师“编辑”页](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="e164b-315">通过复选框可对分配给讲师的课程进行更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-315">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e164b-316">数据库中的每一门课程均有对应显示的复选框。</span><span class="sxs-lookup"><span data-stu-id="e164b-316">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="e164b-317">已分配给讲师的课程将会被选中。</span><span class="sxs-lookup"><span data-stu-id="e164b-317">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="e164b-318">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-318">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="e164b-319">如果课程数过多，另一个 UI 的使用效果可能更好。</span><span class="sxs-lookup"><span data-stu-id="e164b-319">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="e164b-320">但此处所示的用于管理多对多关系的方法不会发生变化。</span><span class="sxs-lookup"><span data-stu-id="e164b-320">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="e164b-321">若要创建或删除关系，则需要使用联接实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-321">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="e164b-322">为已分配的课程数据创建类</span><span class="sxs-lookup"><span data-stu-id="e164b-322">Create a class for assigned courses data</span></span>

<span data-ttu-id="e164b-323">使用以下代码创建 SchoolViewModels/AssignedCourseData.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-323">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e164b-324">`AssignedCourseData` 类包含的数据可用于为已分配给讲师的课程创建复选框。</span><span class="sxs-lookup"><span data-stu-id="e164b-324">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="e164b-325">创建“讲师”页模型基类</span><span class="sxs-lookup"><span data-stu-id="e164b-325">Create an Instructor page model base class</span></span>

<span data-ttu-id="e164b-326">创建 Pages/Instructors/InstructorCoursesPageModel.cs 基类：</span><span class="sxs-lookup"><span data-stu-id="e164b-326">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="e164b-327">`InstructorCoursesPageModel` 是将用于“编辑”和“创建”页模型的基类。</span><span class="sxs-lookup"><span data-stu-id="e164b-327">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e164b-328">`PopulateAssignedCourseData` 读取所有 `Course` 实体以填充 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e164b-328">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e164b-329">该代码将设置每门课程的 `CourseID` 和标题，并决定是否为讲师分配该课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-329">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e164b-330">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用于高效查找。</span><span class="sxs-lookup"><span data-stu-id="e164b-330">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="e164b-331">Razor 页面没有 Course 实体的集合，因此模型绑定器无法自动更新 `CourseAssignments` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-331">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e164b-332">可在新的 `UpdateInstructorCourses` 方法中更新 `CourseAssignments` 导航属性，而不必使用模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="e164b-332">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e164b-333">为此，需要从模型绑定中排除 `CourseAssignments` 属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-333">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="e164b-334">此操作无需对调用 `TryUpdateModel` 的代码进行任何更改，因为使用的重载包含已声明的属性，并且 `CourseAssignments` 不包括在该列表中。</span><span class="sxs-lookup"><span data-stu-id="e164b-334">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="e164b-335">如果未选中任何复选框，则 `UpdateInstructorCourses` 中的代码将使用空集合初始化 `CourseAssignments` 导航属性，并返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="e164b-335">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="e164b-336">之后，代码会循环访问数据库中的所有课程，并逐一检查当前分配给讲师的课程和页面中处于选中状态的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-336">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="e164b-337">为便于高效查找，后两个集合存储在 `HashSet` 对象中。</span><span class="sxs-lookup"><span data-stu-id="e164b-337">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e164b-338">如果某课程的复选框处于选中状态，但该课程不在 `Instructor.CourseAssignments` 导航属性中，则会将该课程添加到导航属性中的集合中。</span><span class="sxs-lookup"><span data-stu-id="e164b-338">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="e164b-339">如果某课程的复选框未处于选中状态，但该课程存在 `Instructor.CourseAssignments` 导航属性中，则会从导航属性中删除该课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-339">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="e164b-340">处理办公室位置</span><span class="sxs-lookup"><span data-stu-id="e164b-340">Handle office location</span></span>

<span data-ttu-id="e164b-341">“编辑”页必须处理的另一个关系是 Instructor 实体与 `OfficeAssignment` 实体之间的一对零或一对一关系。</span><span class="sxs-lookup"><span data-stu-id="e164b-341">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e164b-342">讲师编辑代码必须处理以下场景：</span><span class="sxs-lookup"><span data-stu-id="e164b-342">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="e164b-343">如果用户清除办公室分配，则需删除 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-343">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e164b-344">如果用户输入办公室分配，且办公室分配原本为空，则需创建一个新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-344">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e164b-345">如果用户更改办公室分配，则需更新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-345">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="e164b-346">更新讲师“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-346">Update the Instructor Edit page model</span></span>

<span data-ttu-id="e164b-347">使用以下代码更新 Pages/Instructors/Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-347">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="e164b-348">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-348">The preceding code:</span></span>

* <span data-ttu-id="e164b-349">使用 `OfficeAssignment`、`CourseAssignment` 和 `CourseAssignment.Course` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-349">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="e164b-350">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-350">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e164b-351">`TryUpdateModel` 可防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e164b-351">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e164b-352">如果办公室位置为空，则将 `Instructor.OfficeAssignment` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="e164b-352">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e164b-353">当 `Instructor.OfficeAssignment` 为 null 时，`OfficeAssignment` 表中的相关行将会删除。</span><span class="sxs-lookup"><span data-stu-id="e164b-353">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="e164b-354">调用 `OnGetAsync` 中的 `PopulateAssignedCourseData`，使用 `AssignedCourseData` 视图模型类为复选框提供信息。</span><span class="sxs-lookup"><span data-stu-id="e164b-354">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="e164b-355">调用 `OnPostAsync` 中的 `UpdateInstructorCourses`，将复选框中的信息应用于将要编辑的 Instructor 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-355">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="e164b-356">如果 `TryUpdateModel` 失败，则调用 `OnPostAsync` 中的 `PopulateAssignedCourseData` 和 `UpdateInstructorCourses`。</span><span class="sxs-lookup"><span data-stu-id="e164b-356">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="e164b-357">这些方法调用将在页面重新显示错误消息时还原页面上所输入的已分配课程数据。</span><span class="sxs-lookup"><span data-stu-id="e164b-357">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="e164b-358">更新“讲师编辑”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e164b-358">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="e164b-359">使用以下代码更新 Pages/Instructors/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-359">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="e164b-360">上面的代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="e164b-360">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e164b-361">每列均具有一个复选框和包含课程编号及标题的标题。</span><span class="sxs-lookup"><span data-stu-id="e164b-361">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="e164b-362">所有复选框均具有相同的名称（“selectedCourses”）。</span><span class="sxs-lookup"><span data-stu-id="e164b-362">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e164b-363">使用相同名称可指示模型绑定器将它们视为一个组。</span><span class="sxs-lookup"><span data-stu-id="e164b-363">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e164b-364">每个复选框的值特性都设置为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e164b-364">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="e164b-365">发布页面时，模型绑定器会传递一个数组，该数组只包括所选复选框的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e164b-365">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="e164b-366">初次呈现复选框时，分配给讲师的课程均已选中。</span><span class="sxs-lookup"><span data-stu-id="e164b-366">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="e164b-367">注意：此处所使用的编辑讲师课程数据的方法适用于数量有限的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-367">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e164b-368">对于规模远大于此的集合，则使用不同的 UI 和不同的更新方法会更实用和更高效。</span><span class="sxs-lookup"><span data-stu-id="e164b-368">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="e164b-369">运行应用并测试更新的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-369">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="e164b-370">更改某些课程分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-370">Change some course assignments.</span></span> <span data-ttu-id="e164b-371">这些更改将反映在“索引”页上。</span><span class="sxs-lookup"><span data-stu-id="e164b-371">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="e164b-372">更新讲师“创建”页</span><span class="sxs-lookup"><span data-stu-id="e164b-372">Update the Instructor Create page</span></span>

<span data-ttu-id="e164b-373">使用类似于“编辑”页面的代码更新“讲师创建”页面模型和 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e164b-373">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="e164b-374">测试讲师“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-374">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="e164b-375">更新讲师“删除”页</span><span class="sxs-lookup"><span data-stu-id="e164b-375">Update the Instructor Delete page</span></span>

<span data-ttu-id="e164b-376">使用以下代码更新 Pages/Instructors/Delete.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-376">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="e164b-377">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-377">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-378">对 `CourseAssignments` 导航属性使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="e164b-378">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e164b-379">必须包含 `CourseAssignments`，否则删除讲师时将不会删除课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-379">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e164b-380">为避免阅读它们，可以在数据库中配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e164b-380">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e164b-381">如果要删除的讲师被指派为任何系的管理员，则需从这些系中删除该讲师分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-381">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="e164b-382">运行应用并测试“删除”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-382">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e164b-383">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e164b-383">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e164b-384">[上一个教程](xref:data/ef-rp/read-related-data)
> [下一个教程](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e164b-384">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e164b-385">本教程演示如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="e164b-385">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="e164b-386">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="e164b-386">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="e164b-387">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="e164b-387">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e164b-388">下图显示了部分已完成页面。</span><span class="sxs-lookup"><span data-stu-id="e164b-388">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="e164b-389">![课程“编辑”页](update-related-data/_static/course-edit.png)
![讲师”编辑”页](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="e164b-389">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="e164b-390">查看并测试“创建”和“编辑”课程页。</span><span class="sxs-lookup"><span data-stu-id="e164b-390">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="e164b-391">创建新课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-391">Create a new course.</span></span> <span data-ttu-id="e164b-392">系按其主键（一个整数）进行选择，而不是按名称。</span><span class="sxs-lookup"><span data-stu-id="e164b-392">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="e164b-393">编辑新课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-393">Edit the new course.</span></span> <span data-ttu-id="e164b-394">完成测试后，请删除新课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-394">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="e164b-395">创建基类以共享通用代码</span><span class="sxs-lookup"><span data-stu-id="e164b-395">Create a base class to share common code</span></span>

<span data-ttu-id="e164b-396">“课程/创建”和“课程/编辑”页分别需要一个系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-396">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="e164b-397">针对“创建”和“编辑”页创建 Pages/Courses/DepartmentNamePageModel.cshtml.cs 基类：</span><span class="sxs-lookup"><span data-stu-id="e164b-397">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="e164b-398">上面的代码创建 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) 以包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-398">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e164b-399">如果指定了 `selectedDepartment`，可在 `SelectList` 中选择该系。</span><span class="sxs-lookup"><span data-stu-id="e164b-399">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e164b-400">“创建”和“编辑”页模型类将派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e164b-400">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="e164b-401">自定义课程页</span><span class="sxs-lookup"><span data-stu-id="e164b-401">Customize the Courses Pages</span></span>

<span data-ttu-id="e164b-402">创建新的课程实体时，新实体必须与现有院系有关系。</span><span class="sxs-lookup"><span data-stu-id="e164b-402">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="e164b-403">若要在创建课程的同时添加系，则“创建”和“编辑”的基类必须包含用于选择系的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="e164b-403">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="e164b-404">该下拉列表设置 `Course.DepartmentID` 外键 (FK) 属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-404">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e164b-405">EF Core 使用 `Course.DepartmentID` FK 加载 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="e164b-405">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![创建课程](update-related-data/_static/ddl.png)

<span data-ttu-id="e164b-407">使用以下代码更新“创建”页模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-407">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="e164b-408">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-408">The preceding code:</span></span>

* <span data-ttu-id="e164b-409">派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e164b-409">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e164b-410">使用 `TryUpdateModelAsync` 防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e164b-410">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e164b-411">将 `ViewData["DepartmentID"]` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e164b-411">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e164b-412">`ViewData["DepartmentID"]` 将替换为强类型 `DepartmentNameSL`。</span><span class="sxs-lookup"><span data-stu-id="e164b-412">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="e164b-413">建议使用强类型而非弱类型。</span><span class="sxs-lookup"><span data-stu-id="e164b-413">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e164b-414">有关详细信息，请参阅[弱类型数据（ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e164b-414">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="e164b-415">更新“课程创建”页</span><span class="sxs-lookup"><span data-stu-id="e164b-415">Update the Courses Create page</span></span>

<span data-ttu-id="e164b-416">使用以下代码更新 Pages/Courses/Create.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-416">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e164b-417">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-417">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e164b-418">将标题从“DepartmentID”更改为“Department” 。</span><span class="sxs-lookup"><span data-stu-id="e164b-418">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e164b-419">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e164b-419">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e164b-420">添加“选择系”选项。</span><span class="sxs-lookup"><span data-stu-id="e164b-420">Adds the "Select Department" option.</span></span> <span data-ttu-id="e164b-421">此更改将导致呈现“选择系”而不是第一个系。</span><span class="sxs-lookup"><span data-stu-id="e164b-421">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="e164b-422">在未选择系时添加验证消息。</span><span class="sxs-lookup"><span data-stu-id="e164b-422">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e164b-423">Razor 页面使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)：</span><span class="sxs-lookup"><span data-stu-id="e164b-423">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e164b-424">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-424">Test the Create page.</span></span> <span data-ttu-id="e164b-425">“创建”页显示系名称，而不是系 ID。</span><span class="sxs-lookup"><span data-stu-id="e164b-425">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="e164b-426">更新“课程编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-426">Update the Courses Edit page.</span></span>

<span data-ttu-id="e164b-427">使用以下代码替换 Pages/Courses/Edit.cshtml.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-427">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="e164b-428">这些更改与在“创建”页模型中所做的更改相似。</span><span class="sxs-lookup"><span data-stu-id="e164b-428">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e164b-429">在上面的代码中，`PopulateDepartmentsDropDownList` 在系 ID 中传递并将选择下拉列表中指定的系。</span><span class="sxs-lookup"><span data-stu-id="e164b-429">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="e164b-430">使用以下标记更新 Pages/Courses/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-430">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e164b-431">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-431">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e164b-432">显示课程 ID。</span><span class="sxs-lookup"><span data-stu-id="e164b-432">Displays the course ID.</span></span> <span data-ttu-id="e164b-433">通常不显示实体的主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e164b-433">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e164b-434">PK 对用户不具有任何意义。</span><span class="sxs-lookup"><span data-stu-id="e164b-434">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e164b-435">在这种情况下，PK 就是课程编号。</span><span class="sxs-lookup"><span data-stu-id="e164b-435">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e164b-436">将标题从“DepartmentID”更改为“Department” 。</span><span class="sxs-lookup"><span data-stu-id="e164b-436">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e164b-437">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="e164b-437">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e164b-438">该页面包含课程编号的隐藏域 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e164b-438">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e164b-439">添加具有 `asp-for="Course.CourseID"` 的 `<label>` 标记帮助器也同样需要隐藏域。</span><span class="sxs-lookup"><span data-stu-id="e164b-439">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e164b-440">用户单击“保存”时，需要 `<input type="hidden">`，以便在已发布的数据中包括课程编号。</span><span class="sxs-lookup"><span data-stu-id="e164b-440">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="e164b-441">测试更新的代码。</span><span class="sxs-lookup"><span data-stu-id="e164b-441">Test the updated code.</span></span> <span data-ttu-id="e164b-442">创建、编辑和删除课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-442">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="e164b-443">将 AsNoTracking 添加到“详细信息”和“删除”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-443">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="e164b-444">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以在不需要跟踪时提高性能。</span><span class="sxs-lookup"><span data-stu-id="e164b-444">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="e164b-445">将 `AsNoTracking` 添加到“删除”和“详细信息”页模型。</span><span class="sxs-lookup"><span data-stu-id="e164b-445">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="e164b-446">下面的代码显示更新的“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-446">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="e164b-447">在 Pages/Courses/Details.cshtml.cs 文件中更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="e164b-447">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="e164b-448">修改“删除”和“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="e164b-448">Modify the Delete and Details pages</span></span>

<span data-ttu-id="e164b-449">使用以下标记更新“删除”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e164b-449">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="e164b-450">对“详细信息”页执行相同更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-450">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="e164b-451">测试“课程”页</span><span class="sxs-lookup"><span data-stu-id="e164b-451">Test the Course pages</span></span>

<span data-ttu-id="e164b-452">测试“创建”、“编辑”、“详细信息”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="e164b-452">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="e164b-453">更新“讲师”页</span><span class="sxs-lookup"><span data-stu-id="e164b-453">Update the instructor pages</span></span>

<span data-ttu-id="e164b-454">以下各部分介绍更新“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-454">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="e164b-455">添加办公室位置</span><span class="sxs-lookup"><span data-stu-id="e164b-455">Add office location</span></span>

<span data-ttu-id="e164b-456">编辑讲师记录时，可能希望更新讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-456">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="e164b-457">`Instructor` 实体与 `OfficeAssignment` 实体是一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="e164b-457">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e164b-458">讲师代码必须处理：</span><span class="sxs-lookup"><span data-stu-id="e164b-458">The instructor code must handle:</span></span>

* <span data-ttu-id="e164b-459">如果用户清除办公室分配，则需删除 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-459">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e164b-460">如果用户输入办公室分配，且办公室分配原本为空，则需创建一个新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-460">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e164b-461">如果用户更改办公室分配，则需更新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-461">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="e164b-462">使用以下代码更新讲师“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-462">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="e164b-463">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e164b-463">The preceding code:</span></span>

* <span data-ttu-id="e164b-464">使用 `OfficeAssignment` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-464">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="e164b-465">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-465">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e164b-466">`TryUpdateModel` 可防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="e164b-466">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e164b-467">如果办公室位置为空，则将 `Instructor.OfficeAssignment` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="e164b-467">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e164b-468">当 `Instructor.OfficeAssignment` 为 null 时，`OfficeAssignment` 表中的相关行将会删除。</span><span class="sxs-lookup"><span data-stu-id="e164b-468">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="e164b-469">更新讲师“编辑”页</span><span class="sxs-lookup"><span data-stu-id="e164b-469">Update the instructor Edit page</span></span>

<span data-ttu-id="e164b-470">使用办公室位置更新 Pages/Instructors/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e164b-470">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="e164b-471">验证是否可以更改讲师办公室位置。</span><span class="sxs-lookup"><span data-stu-id="e164b-471">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="e164b-472">向“讲师编辑”页添加课程分配</span><span class="sxs-lookup"><span data-stu-id="e164b-472">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="e164b-473">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-473">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e164b-474">本部分将添加更改课程分配的功能。</span><span class="sxs-lookup"><span data-stu-id="e164b-474">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="e164b-475">下图显示更新的讲师“编辑”页：</span><span class="sxs-lookup"><span data-stu-id="e164b-475">The following image shows the updated instructor Edit page:</span></span>

![带课程信息的讲师“编辑”页](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="e164b-477">`Course` 和 `Instructor` 具有多对多关系。</span><span class="sxs-lookup"><span data-stu-id="e164b-477">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="e164b-478">若要添加和删除关系，可以从 `CourseAssignments` 联接实体集中添加和删除实体。</span><span class="sxs-lookup"><span data-stu-id="e164b-478">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="e164b-479">通过复选框可对分配给讲师的课程进行更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-479">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e164b-480">数据库中的每一门课程均有对应显示的复选框。</span><span class="sxs-lookup"><span data-stu-id="e164b-480">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="e164b-481">已分配给讲师的课程将会被勾选。</span><span class="sxs-lookup"><span data-stu-id="e164b-481">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="e164b-482">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-482">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="e164b-483">如果课程数量过多：</span><span class="sxs-lookup"><span data-stu-id="e164b-483">If the number of courses were much greater:</span></span>

* <span data-ttu-id="e164b-484">可以使用其他用户界面显示课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-484">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="e164b-485">使用联接实体创建或删除关系的方法不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-485">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="e164b-486">添加类以支持“创建”和“编辑”讲师页</span><span class="sxs-lookup"><span data-stu-id="e164b-486">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="e164b-487">使用以下代码创建 SchoolViewModels/AssignedCourseData.cs：</span><span class="sxs-lookup"><span data-stu-id="e164b-487">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e164b-488">`AssignedCourseData` 类包含的数据可用于为讲师已分配的课程创建复选框。</span><span class="sxs-lookup"><span data-stu-id="e164b-488">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="e164b-489">创建 Pages/Instructors/InstructorCoursesPageModel.cshtml.cs 基类：</span><span class="sxs-lookup"><span data-stu-id="e164b-489">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="e164b-490">`InstructorCoursesPageModel` 是将用于“编辑”和“创建”页模型的基类。</span><span class="sxs-lookup"><span data-stu-id="e164b-490">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e164b-491">`PopulateAssignedCourseData` 读取所有 `Course` 实体以填充 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e164b-491">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e164b-492">该代码将设置每门课程的 `CourseID` 和标题，并决定是否为讲师分配该课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-492">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e164b-493">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用于创建高效查找。</span><span class="sxs-lookup"><span data-stu-id="e164b-493">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="e164b-494">讲师“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="e164b-494">Instructors Edit page model</span></span>

<span data-ttu-id="e164b-495">使用以下代码更新讲师“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-495">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="e164b-496">上面的代码处理办公室分配更改。</span><span class="sxs-lookup"><span data-stu-id="e164b-496">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="e164b-497">更新讲师 Razor 视图：</span><span class="sxs-lookup"><span data-stu-id="e164b-497">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="e164b-498">将代码粘贴到 Visual Studio 中时，换行符会发生更改，其更改方式会导致代码中断。</span><span class="sxs-lookup"><span data-stu-id="e164b-498">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="e164b-499">按 Ctrl+Z 一次可撤消自动格式设置。</span><span class="sxs-lookup"><span data-stu-id="e164b-499">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="e164b-500">按 Ctrl+Z 可以修复换行符，使其看起来如此处所示。</span><span class="sxs-lookup"><span data-stu-id="e164b-500">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="e164b-501">缩进不一定要呈现完美形式，但 `@:</tr><tr>`、`@:<td>`、`@:</td>` 和 `@:</tr>` 行必须各成一行（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="e164b-501">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="e164b-502">选中新的代码块后，按 Tab 三次，使新代码与现有代码对齐。</span><span class="sxs-lookup"><span data-stu-id="e164b-502">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="e164b-503">[通过此链接](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)投票或查看此 bug 的状态。</span><span class="sxs-lookup"><span data-stu-id="e164b-503">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="e164b-504">上面的代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="e164b-504">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e164b-505">每列均具有一个复选框和包含课程编号及标题的标题。</span><span class="sxs-lookup"><span data-stu-id="e164b-505">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="e164b-506">所有复选框均具有相同的名称（“selectedCourses”）。</span><span class="sxs-lookup"><span data-stu-id="e164b-506">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e164b-507">使用相同名称可指示模型绑定器将它们视为一个组。</span><span class="sxs-lookup"><span data-stu-id="e164b-507">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e164b-508">每个复选框的值特性都设置为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e164b-508">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="e164b-509">发布页面时，模型绑定器会传递一个数组，该数组只包括所选复选框的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e164b-509">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="e164b-510">初次呈现复选框时，分配给讲师的课程具有已勾选的特性。</span><span class="sxs-lookup"><span data-stu-id="e164b-510">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="e164b-511">运行应用并测试更新的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-511">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="e164b-512">更改某些课程分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-512">Change some course assignments.</span></span> <span data-ttu-id="e164b-513">这些更改将反映在“索引”页上。</span><span class="sxs-lookup"><span data-stu-id="e164b-513">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="e164b-514">注意：此处所使用的编辑讲师课程数据的方法适用于数量有限的课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-514">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e164b-515">对于规模远大于此的集合，则使用不同的 UI 和不同的更新方法会更实用和更高效。</span><span class="sxs-lookup"><span data-stu-id="e164b-515">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="e164b-516">更新讲师“创建”页</span><span class="sxs-lookup"><span data-stu-id="e164b-516">Update the instructors Create page</span></span>

<span data-ttu-id="e164b-517">使用以下代码更新讲师“创建”页模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-517">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="e164b-518">前面的代码与 Pages/Instructors/Edit.cshtml.cs 代码类似。</span><span class="sxs-lookup"><span data-stu-id="e164b-518">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="e164b-519">使用以下标记更新“讲师创建”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="e164b-519">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="e164b-520">测试讲师“创建”页。</span><span class="sxs-lookup"><span data-stu-id="e164b-520">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="e164b-521">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="e164b-521">Update the Delete page</span></span>

<span data-ttu-id="e164b-522">使用以下代码更新“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="e164b-522">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="e164b-523">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="e164b-523">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e164b-524">对 `CourseAssignments` 导航属性使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="e164b-524">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e164b-525">必须包含 `CourseAssignments`，否则删除讲师时将不会删除课程。</span><span class="sxs-lookup"><span data-stu-id="e164b-525">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e164b-526">为避免阅读它们，可以在数据库中配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="e164b-526">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e164b-527">如果要删除的讲师被指派为任何系的管理员，则需从这些系中删除该讲师分配。</span><span class="sxs-lookup"><span data-stu-id="e164b-527">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e164b-528">其他资源</span><span class="sxs-lookup"><span data-stu-id="e164b-528">Additional resources</span></span>

* [<span data-ttu-id="e164b-529">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="e164b-529">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="e164b-530">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="e164b-530">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="e164b-531">[上一页](xref:data/ef-rp/read-related-data)
> [下一页](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e164b-531">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
