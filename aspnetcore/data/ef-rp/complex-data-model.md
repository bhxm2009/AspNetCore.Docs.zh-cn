---
title: 第 5 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 数据模型
author: rick-anderson
description: Razor 页面和实体框架教程系列的第 5 部分。
ms.author: riande
ms.custom: mvc
ms.date: 3/3/2021
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 23ac7fa83d6ee313e6695e3829a50ae94c967572
ms.sourcegitcommit: 4864500b0f43e33dc0445ce632bfbe1e43e79320
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105994150"
---
<!-- Removed from V 5.0, CourseAssignment -->

# <a name="part-5-razor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="59502-103">第 5 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 数据模型</span><span class="sxs-lookup"><span data-stu-id="59502-103">Part 5, Razor Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="59502-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jeremy Likness](https://twitter.com/jeremylikness) 和 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="59502-104">By [Tom Dykstra](https://github.com/tdykstra), [Jeremy Likness](https://twitter.com/jeremylikness), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="59502-105">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="59502-106">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="59502-106">In this tutorial:</span></span>

* <span data-ttu-id="59502-107">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="59502-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="59502-108">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="59502-109">完成的数据模型如下图所示：</span><span class="sxs-lookup"><span data-stu-id="59502-109">The completed data model is shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/EF.png)

<span data-ttu-id="59502-111">以下数据库关系图是使用 [Dataedo](https://dataedo.com/) 创建的：</span><span class="sxs-lookup"><span data-stu-id="59502-111">The following database diagram was made with [Dataedo](https://dataedo.com/):</span></span>

<!--Image added in https://github.com/dotnet/AspNetCore.Docs/pull/21922  -->

![Dataedo 关系图](intro/_static/5/AzureSQL_DB_ERD_madeIn_Dataedo.png)

<span data-ttu-id="59502-113">若要使用 Dataedo 创建数据库关系图，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="59502-113">To create a database diagram with Dataedo:</span></span>

* [<span data-ttu-id="59502-114">将应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="59502-114">Deploy the app to Azure</span></span>](/azure/app-service/tutorial-dotnetcore-sqldb-app)
* <span data-ttu-id="59502-115">在计算机上下载并安装 [Dataedo](https://dataedo.com/)。</span><span class="sxs-lookup"><span data-stu-id="59502-115">Download and install [Dataedo](https://dataedo.com/) on your computer.</span></span>
* <span data-ttu-id="59502-116">按照[在 5 分钟内生成 Azure SQL 数据库的文档](https://dataedo.com/tutorials/generate-documentation-for-azure-sql-database)中的说明进行操作</span><span class="sxs-lookup"><span data-stu-id="59502-116">Follow the instructions [Generate documentation for Azure SQL Database in 5 minutes ](https://dataedo.com/tutorials/generate-documentation-for-azure-sql-database)</span></span>

<span data-ttu-id="59502-117">在前面的 Dataedo 关系图中，`CourseInstructor` 是实体框架创建的联接表。</span><span class="sxs-lookup"><span data-stu-id="59502-117">In the preceding Dataedo diagram, the `CourseInstructor` is a join table created by Entity Framework.</span></span> <span data-ttu-id="59502-118">有关详细信息，请参阅[多对多](/ef/core/modeling/relationships#many-to-many)</span><span class="sxs-lookup"><span data-stu-id="59502-118">For more information, see [Many-to-many](/ef/core/modeling/relationships#many-to-many)</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="59502-119">Student 实体</span><span class="sxs-lookup"><span data-stu-id="59502-119">The Student entity</span></span>

<span data-ttu-id="59502-120">使用以下代码替换 Models/Student.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="59502-120">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="59502-121">前面的代码将添加一个 `FullName` 属性，并将以下特性添加到现有属性：</span><span class="sxs-lookup"><span data-stu-id="59502-121">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)
* [`[DisplayFormat]`](xref:System.ComponentModel.DataAnnotations.DisplayFormatAttribute)
* [`[StringLength]`](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)
* [`[Column]`](xref:System.ComponentModel.DataAnnotations.Schema.ColumnAttribute)
* [`[Required]`](xref:System.ComponentModel.DataAnnotations.RequiredAttribute)
* [`[Display]`](xref:System.ComponentModel.DataAnnotations.DisplayAttribute)

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="59502-122">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="59502-122">The FullName calculated property</span></span>

<span data-ttu-id="59502-123">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="59502-123">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="59502-124">无法设置 `FullName`，因此它只有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="59502-124">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="59502-125">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="59502-125">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="59502-126">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="59502-126">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="59502-127">对于学生注册日期，目前所有页面都显示时间和日期，但只有日期是相关的。</span><span class="sxs-lookup"><span data-stu-id="59502-127">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="59502-128">使用数据注释特性，可更改一次代码，修复每个页面中数据的显示格式。</span><span class="sxs-lookup"><span data-stu-id="59502-128">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="59502-129">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="59502-129">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="59502-130">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="59502-130">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="59502-131">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="59502-131">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="59502-132">例如：</span><span class="sxs-lookup"><span data-stu-id="59502-132">For example:</span></span>

* <span data-ttu-id="59502-133">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="59502-133">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="59502-134">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="59502-134">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="59502-135">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性。</span><span class="sxs-lookup"><span data-stu-id="59502-135">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="59502-136">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="59502-136">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="59502-137">DisplayFormat 特性</span><span class="sxs-lookup"><span data-stu-id="59502-137">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="59502-138">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="59502-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="59502-139">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="59502-139">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="59502-140">`DisplayFormat` 特性用于显式指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="59502-140">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="59502-141">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="59502-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="59502-142">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="59502-142">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="59502-143">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="59502-143">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="59502-144">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="59502-144">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="59502-145">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="59502-145">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="59502-146">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="59502-146">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="59502-147">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="59502-147">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="59502-148">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="59502-148">The browser can enable HTML5 features.</span></span> <span data-ttu-id="59502-149">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接和客户端输入验证。</span><span class="sxs-lookup"><span data-stu-id="59502-149">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="59502-150">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="59502-150">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="59502-151">有关详细信息，请参阅 [\<input> 标记帮助程序文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="59502-151">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="59502-152">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="59502-152">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="59502-153">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="59502-153">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="59502-154">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="59502-154">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="59502-155">显示的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="59502-155">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="59502-156">[稍后](#the-required-attribute)显示一个设置最小字符串长度的示例。</span><span class="sxs-lookup"><span data-stu-id="59502-156">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="59502-157">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="59502-157">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="59502-158">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="59502-158">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="59502-159">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="59502-159">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="59502-160">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 特性可用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="59502-160">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="59502-161">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="59502-161">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="59502-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-163">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器 。</span><span class="sxs-lookup"><span data-stu-id="59502-163">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="59502-165">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="59502-165">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="59502-166">名称字段具有类型 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="59502-166">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="59502-167">在本教程的后续部分创建和应用迁移时，因为字符串的长度特性，名称字段将变为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="59502-167">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-169">使用 SQLite 工具，检查 `Student` 表的列定义。</span><span class="sxs-lookup"><span data-stu-id="59502-169">Using a SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="59502-170">名称字段具有类型 `Text`。</span><span class="sxs-lookup"><span data-stu-id="59502-170">The name fields have type `Text`.</span></span> <span data-ttu-id="59502-171">请注意，首个名称字段名为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="59502-171">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="59502-172">在下一部分中，`FirstMidName` 将更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="59502-172">In the next section, `FirstMidName` is changed to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="59502-173">Column 特性</span><span class="sxs-lookup"><span data-stu-id="59502-173">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="59502-174">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="59502-174">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="59502-175">在 `Student` 模型中，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="59502-175">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="59502-176">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="59502-176">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="59502-177">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="59502-177">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="59502-178">使用 `[Column]` 特性，数据模型中的 `Student.FirstMidName` 可映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="59502-178">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="59502-179">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="59502-179">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="59502-180">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="59502-180">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="59502-181">这种差异将通过在本教程后面添加迁移来解决。</span><span class="sxs-lookup"><span data-stu-id="59502-181">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="59502-182">Required 特性</span><span class="sxs-lookup"><span data-stu-id="59502-182">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="59502-183">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="59502-183">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="59502-184">值类型（`DateTime`、`int` 和 `double`）等不可为 null 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="59502-184">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="59502-185">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="59502-185">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="59502-186">`Required` 特性必须与 `MinimumLength` 结合使用才能强制执行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="59502-186">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="59502-187">`MinimumLength` 和 `Required` 允许通过空格来满足验证。</span><span class="sxs-lookup"><span data-stu-id="59502-187">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="59502-188">使用 `RegularExpression` 特性来完全控制字符串。</span><span class="sxs-lookup"><span data-stu-id="59502-188">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="59502-189">Display 特性</span><span class="sxs-lookup"><span data-stu-id="59502-189">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="59502-190">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="59502-190">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="59502-191">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="59502-191">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="59502-192">创建迁移</span><span class="sxs-lookup"><span data-stu-id="59502-192">Create a migration</span></span>

<span data-ttu-id="59502-193">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="59502-193">Run the app and go to the Students page.</span></span> <span data-ttu-id="59502-194">此时引发异常。</span><span class="sxs-lookup"><span data-stu-id="59502-194">An exception is thrown.</span></span> <span data-ttu-id="59502-195">`[Column]` 特性导致 EF 希望查找名为 `FirstName` 的列，但数据库中的列名称仍为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="59502-195">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-196">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-197">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="59502-197">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
There are pending model changes
Pending model changes are detected in the following:

SchoolContext
```

* <span data-ttu-id="59502-198">在 PMC 中，输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-198">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
   
  ```

  <span data-ttu-id="59502-199">其中的第一个命令将生成以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="59502-199">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="59502-200">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="59502-200">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="59502-201">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="59502-201">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="59502-202">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="59502-202">Open the Student table in SSOX:</span></span>

  ![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="59502-204">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="59502-204">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="59502-205">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="59502-205">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="59502-206">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="59502-206">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-208">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="59502-208">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="59502-209">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="59502-209">Open a command window in the project folder.</span></span> <span data-ttu-id="59502-210">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-210">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="59502-211">数据库更新命令显示类似于以下示例的错误：</span><span class="sxs-lookup"><span data-stu-id="59502-211">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="59502-212">在本教程中，解决此错误的方法是删除并重新创建初始迁移。</span><span class="sxs-lookup"><span data-stu-id="59502-212">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="59502-213">有关详细信息，请参阅[迁移教程](xref:data/ef-rp/migrations)顶部的 SQLite 警告说明。</span><span class="sxs-lookup"><span data-stu-id="59502-213">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="59502-214">删除“Migrations”文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-214">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="59502-215">运行以下命令以删除数据库，创建新的初始迁移，并应用该迁移：</span><span class="sxs-lookup"><span data-stu-id="59502-215">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
   
  ```

* <span data-ttu-id="59502-216">使用 SQLite 工具检查 Student 表。</span><span class="sxs-lookup"><span data-stu-id="59502-216">Examine the Student table with a SQLite tool.</span></span> <span data-ttu-id="59502-217">以前为 `FirstMidName` 的列现在为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="59502-217">The column that was `FirstMidName` is now `FirstName`.</span></span>

---

* <span data-ttu-id="59502-218">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="59502-218">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="59502-219">请注意，时间和日期既未输入，也未显示。</span><span class="sxs-lookup"><span data-stu-id="59502-219">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="59502-220">选择“新建”，尝试输入不超过 50 个字符的名称。</span><span class="sxs-lookup"><span data-stu-id="59502-220">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="59502-221">在以下部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="59502-221">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="59502-222">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="59502-222">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="59502-223">Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="59502-223">The Instructor Entity</span></span>

<!-- no longer using PJT
![Instructor entity](complex-data-model/_static/instructor-entity.png) -->

<span data-ttu-id="59502-224">用以下代码创建 Models/Instructor.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-224">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="59502-225">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="59502-225">Multiple attributes can be on one line.</span></span> <span data-ttu-id="59502-226">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="59502-226">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="59502-227">导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-227">Navigation properties</span></span>

<span data-ttu-id="59502-228">`Courses` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-228">The `Courses` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="59502-229">一名讲师可以教授任意数量的课程，因此 `Courses` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="59502-229">An instructor can teach any number of courses, so `Courses` is defined as a collection.</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="59502-230">讲师最多可以有一个办公室，因此 `OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-230">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="59502-231">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-231">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="59502-232">OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-232">The OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="59502-234">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-234">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="59502-235">Key 特性</span><span class="sxs-lookup"><span data-stu-id="59502-235">The Key attribute</span></span>

<span data-ttu-id="59502-236">[`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) 特性用于在属性名不是 `classnameID` 或 `ID` 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="59502-236">The [`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) attribute is used to identify a property as the primary key (PK) when the property name is something other than `classnameID` or `ID`.</span></span>

<span data-ttu-id="59502-237">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="59502-237">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="59502-238">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-238">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="59502-239">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="59502-239">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="59502-240">如果一个表中的 PK 既是另一个表中的 PK 又是 FK，则会存在一对零或一对一关系。</span><span class="sxs-lookup"><span data-stu-id="59502-240">A one-to-zero-or-one relationship occurs when a PK in one table is both a PK and a FK in another table.</span></span>

<span data-ttu-id="59502-241">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为 `InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="59502-241">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="59502-242">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="59502-242">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="59502-243">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="59502-243">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span> <span data-ttu-id="59502-244">有关详细信息，请参阅 [EF 密钥](/ef/core/modeling/keys)。</span><span class="sxs-lookup"><span data-stu-id="59502-244">For more information, see [EF Keys](/ef/core/modeling/keys).</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="59502-245">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-245">The Instructor navigation property</span></span>

<span data-ttu-id="59502-246">`Instructor.OfficeAssignment` 导航属性可以为 null，因为给定的讲师可能没有 `OfficeAssignment` 行。</span><span class="sxs-lookup"><span data-stu-id="59502-246">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="59502-247">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-247">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="59502-248">`OfficeAssignment.Instructor` 导航属性将始终具有一个 Instructor 实体，因为外键 `InstructorID` 类型为 `int`，不可为 null 的值类型。</span><span class="sxs-lookup"><span data-stu-id="59502-248">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="59502-249">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-249">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="59502-250">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="59502-250">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="59502-251">Course 实体</span><span class="sxs-lookup"><span data-stu-id="59502-251">The Course Entity</span></span>

<span data-ttu-id="59502-252">用以下代码更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-252">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="59502-253">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="59502-253">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="59502-254">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-254">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="59502-255">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-255">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="59502-256">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有外键属性。</span><span class="sxs-lookup"><span data-stu-id="59502-256">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="59502-257">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="59502-257">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="59502-258">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="59502-258">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="59502-259">然而，在数据模型中显式包含 FK 可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="59502-259">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="59502-260">例如，假设某个模型中不包含 FK 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="59502-260">For example, consider a model where the FK property `DepartmentID` is ***not*** included.</span></span> <span data-ttu-id="59502-261">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="59502-261">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="59502-262">如果未显式加载，则 `Department` 属性为 `null`。</span><span class="sxs-lookup"><span data-stu-id="59502-262">The `Department` property is `null` if it's not explicitly loaded.</span></span>
* <span data-ttu-id="59502-263">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-263">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="59502-264">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-264">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="59502-265">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="59502-265">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="59502-266">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="59502-266">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="59502-267">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="59502-267">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="59502-268">数据库生成通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="59502-268">Database-generated is generally the best approach.</span></span> <span data-ttu-id="59502-269">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="59502-269">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="59502-270">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="59502-270">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="59502-271">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-271">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="59502-272">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="59502-272">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="59502-273">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="59502-273">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-274">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-274">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-275">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-275">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="59502-276">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-276">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="59502-277">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="59502-277">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="59502-278">一门课程可能由多位讲师讲授，因此 `Instructors` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="59502-278">A course may be taught by multiple instructors, so the `Instructors` navigation property is a collection:</span></span>

```csharp
        public ICollection<Instructor> Instructors { get; set; }
```

## <a name="the-department-entity"></a><span data-ttu-id="59502-279">Department 实体</span><span class="sxs-lookup"><span data-stu-id="59502-279">The Department entity</span></span>

<span data-ttu-id="59502-280">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-280">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet1)]

### <a name="the-column-attribute"></a><span data-ttu-id="59502-281">Column 特性</span><span class="sxs-lookup"><span data-stu-id="59502-281">The Column attribute</span></span>

<span data-ttu-id="59502-282">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="59502-282">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="59502-283">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="59502-283">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="59502-284">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="59502-284">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="59502-285">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="59502-285">Column mapping is generally not required.</span></span> <span data-ttu-id="59502-286">EF Core 基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="59502-286">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="59502-287">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="59502-287">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="59502-288">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="59502-288">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-289">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-289">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-290">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-290">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="59502-291">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="59502-291">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="59502-292">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="59502-292">An administrator is always an instructor.</span></span> <span data-ttu-id="59502-293">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="59502-293">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="59502-294">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="59502-294">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="59502-295">上面代码中的 `?` 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-295">The `?` in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="59502-296">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-296">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="59502-297">按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="59502-297">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="59502-298">此默认行为可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="59502-298">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="59502-299">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="59502-299">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="59502-300">例如，如果将 `Department.InstructorID` 属性定义为不可为 null，EF Core 将配置级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="59502-300">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="59502-301">在这种情况下，指定为管理员的讲师被删除时，该学院将被删除。</span><span class="sxs-lookup"><span data-stu-id="59502-301">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="59502-302">在这种情况下，限制规则将更有意义。</span><span class="sxs-lookup"><span data-stu-id="59502-302">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="59502-303">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则并禁用级联规则。</span><span class="sxs-lookup"><span data-stu-id="59502-303">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

<!-- todo review: Why update, just go with this from the start 
## The Enrollment entity

An enrollment record is for one course taken by one student.

![Enrollment entity](complex-data-model/_static/enrollment-entity.png)

Update *Models/Enrollment.cs* with the following code:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

-->

### <a name="the-enrollment-foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-304">注册外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-304">The Enrollment foreign key and navigation properties</span></span>

<span data-ttu-id="59502-305">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="59502-305">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png) 

<span data-ttu-id="59502-307">用以下代码更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-307">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Enrollment.cs?highlight=1,15)]

<span data-ttu-id="59502-308">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="59502-309">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="59502-310">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="59502-311">多对多关系</span><span class="sxs-lookup"><span data-stu-id="59502-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="59502-312">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="59502-313">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="59502-313">The `Enrollment` entity functions as a many-to-many join table \***with payload** _ in the database.</span></span> <span data-ttu-id="59502-314">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据。</span><span class="sxs-lookup"><span data-stu-id="59502-314">_ *_With payload_*\* means that the `Enrollment` table contains additional data besides FKs for the joined tables.</span></span> <span data-ttu-id="59502-315">在 `Enrollment` 实体中，除 FK 以外的其他数据是 PK 和 `Grade`。</span><span class="sxs-lookup"><span data-stu-id="59502-315">In the `Enrollment` entity, the additional data besides FKs are the PK and `Grade`.</span></span>

<span data-ttu-id="59502-316">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="59502-316">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="59502-317">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="59502-317">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="59502-318">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="59502-318">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="59502-320">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-320">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="59502-321">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="59502-321">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs, `CourseID` and `StudentID`.</span></span> <span data-ttu-id="59502-322">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="59502-322">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="59502-323">`Instructor` 和 `Course` 实体具有使用 PJT 的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-323">The `Instructor` and `Course` entities have a many-to-many relationship using a PJT.</span></span>

<!--
## The CourseAssignment entity

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

Create *Models/CourseAssignment.cs* with the following code:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

It's common to name a join entity `EntityName1EntityName2`. For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`. However, we recommend using a name that describes the relationship.

Data models start out simple and grow. Join tables without payload (PJTs) frequently evolve to include payload. By starting with a descriptive entity name, the name doesn't need to change when the join table changes. Ideally, the join entity would have its own natural (possibly single word) name in the business domain. For example, Books and Customers could be linked with a join entity called Ratings. For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.


### Composite key

The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table. `CourseAssignment` doesn't require a dedicated PK. The `InstructorID` and `CourseID` properties function as a composite PK. The only way to specify composite PKs to EF Core is with the *fluent API*. The next section shows how to configure the composite PK.

The composite key ensures that:

* Multiple rows are allowed for one course.
* Multiple rows are allowed for one instructor.
* Multiple rows aren't allowed for the same instructor and course.

The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible. To prevent such duplicates:

* Add a unique index on the FK fields, or
* Configure `Enrollment` with a primary composite key similar to `CourseAssignment`. For more information, see [Indexes](/ef/core/modeling/indexes).

-->

## <a name="update-the-database-context"></a><span data-ttu-id="59502-324">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="59502-324">Update the database context</span></span>

<span data-ttu-id="59502-325">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-325">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SS&highlight=15-17,21-28)]

<!-- TODO review -->
<span data-ttu-id="59502-326">上面的代码添加新实体，并且：</span><span class="sxs-lookup"><span data-stu-id="59502-326">The preceding code adds the new entities and:</span></span>

* <span data-ttu-id="59502-327">配置 `Instructor` 和 `Course` 实体之间的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-327">Configures the many-to-many relationship between the `Instructor` and `Course` entities.</span></span>
* <span data-ttu-id="59502-328">为 `Department` 实体配置并发标记。</span><span class="sxs-lookup"><span data-stu-id="59502-328">Configures a concurrency token for the `Department` entity.</span></span> <span data-ttu-id="59502-329">并发将在本教程的后续部分中讨论。</span><span class="sxs-lookup"><span data-stu-id="59502-329">Concurrency is discussed later in the tutorial.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="59502-330">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="59502-330">Fluent API alternative to attributes</span></span>

<span data-ttu-id="59502-331">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为。</span><span class="sxs-lookup"><span data-stu-id="59502-331">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="59502-332">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="59502-332">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="59502-333">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="59502-333">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="59502-334">在本教程中，fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="59502-334">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="59502-335">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="59502-335">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="59502-336">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="59502-336">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="59502-337">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="59502-337">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="59502-338">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="59502-338">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes *clean*.</span></span> <span data-ttu-id="59502-339">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="59502-339">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="59502-340">某些配置只能通过 Fluent API 完成（例如，指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="59502-340">There are some configurations that can only be done with the fluent API, for example, , specifying a composite PK.</span></span> <span data-ttu-id="59502-341">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="59502-341">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="59502-342">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="59502-342">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="59502-343">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="59502-343">Choose one of these two approaches.</span></span>
* <span data-ttu-id="59502-344">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="59502-344">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="59502-345">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="59502-345">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="59502-346">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="59502-346">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="59502-347">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="59502-347">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="59502-348">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="59502-348">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="59502-349">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="59502-349">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

<!-- 
## Entity diagram

The following illustration shows the diagram that EF Power Tools create for the completed School model.

![Entity diagram](complex-data-model/_static/diagram.png)

The preceding diagram shows:

* Several one-to-many relationship lines (1 to \*).
* The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.
* The zero-or-one-to-many relationship line (0..1 to *) between the `Instructor` and `Department` entities.
-->

## <a name="seed-the-database"></a><span data-ttu-id="59502-350">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="59502-350">Seed the database</span></span>

<span data-ttu-id="59502-351">更新 Data/DbInitializer.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="59502-351">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu50/Data/DbInitializer2.cs?name=snippet)]

<span data-ttu-id="59502-352">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="59502-352">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="59502-353">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="59502-353">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="59502-354">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="59502-354">The sample data is used for testing.</span></span>

<!-- This is beyond the scope of this tutorial too expensive to maintain, so removing
## Add a migration

Build the project.

# [Visual Studio](#tab/visual-studio)

In PMC, run the following command.

```powershell
Add-Migration ComplexDataModel
```

The preceding command displays a warning about possible data loss.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

If the `database update` command is run, the following error is produced:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

The next section fixes this error.

# [Visual Studio Code](#tab/visual-studio-code)

If migration is added and the `database update` command is run, the following error is returned:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

The next section fixes this error.

---

-->

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="59502-355">应用迁移或删除并重新创建</span><span class="sxs-lookup"><span data-stu-id="59502-355">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="59502-356">对于现有数据库，有两种方法可以更改数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-356">With the existing database, there are two approaches to changing the database:</span></span>

* <span data-ttu-id="59502-357">[删除并重新创建数据库](#drop)。</span><span class="sxs-lookup"><span data-stu-id="59502-357">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="59502-358">如果使用 SQLite，请选择此部分。</span><span class="sxs-lookup"><span data-stu-id="59502-358">Choose this section if when using SQLite.</span></span>
* <span data-ttu-id="59502-359">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="59502-359">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="59502-360">本部分的说明仅适用于 SQL Server，不适用于 SQLite。</span><span class="sxs-lookup"><span data-stu-id="59502-360">The instructions in this section work for SQL Server only, ***not for SQLite***.</span></span>

<span data-ttu-id="59502-361">这两个选项都适用于 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="59502-361">Either choice works for SQL Server.</span></span> <span data-ttu-id="59502-362">虽然应用迁移方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="59502-362">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span>

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="59502-363">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="59502-363">Drop and re-create the database</span></span>

<span data-ttu-id="59502-364">若要强制 EF Core 创建新的数据库，请删除并更新该数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-364">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-365">Visual Studio</span></span>](#tab/visual-studio)

  * <span data-ttu-id="59502-366">删除“Migrations”文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-366">Delete the *Migrations* folder.</span></span>
  * <span data-ttu-id="59502-367">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-367">In the **Package Manager Console** (PMC), run the following commands:</span></span>

  ```powershell
  Drop-Database
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-368">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-368">Visual Studio Code</span></span>](#tab/visual-studio-code)

  * <span data-ttu-id="59502-369">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-369">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="59502-370">项目文件夹包含 ContosoUniversity.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="59502-370">The project folder contains the *ContosoUniversity.csproj* file.</span></span>
  * <span data-ttu-id="59502-371">删除“Migrations”文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-371">Delete the *Migrations* folder.</span></span>
  * <span data-ttu-id="59502-372">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-372">Run the following commands:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

 ---

<span data-ttu-id="59502-373">运行应用。</span><span class="sxs-lookup"><span data-stu-id="59502-373">Run the app.</span></span> <span data-ttu-id="59502-374">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="59502-374">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="59502-375">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="59502-375">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-376">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-376">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-377">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-377">Open the database in SSOX:</span></span>

  * <span data-ttu-id="59502-378">如果之前已打开过 SSOX，请单击“刷新”按钮。</span><span class="sxs-lookup"><span data-stu-id="59502-378">If SSOX was opened previously, click the **Refresh** button.</span></span>
  * <span data-ttu-id="59502-379">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="59502-379">Expand the **Tables** node.</span></span> <span data-ttu-id="59502-380">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="59502-380">The created tables are displayed.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-381">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-381">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-382">使用 SQLite 工具检查数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-382">Use a SQLite tool to examine the database:</span></span>

* <span data-ttu-id="59502-383">新的表和列。</span><span class="sxs-lookup"><span data-stu-id="59502-383">New tables and columns.</span></span>
* <span data-ttu-id="59502-384">表中设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="59502-384">Seeded data in tables.</span></span>

---

<!-- Dropped, see previous comment 
<a name="applyexisting"></a>

## Apply the migration

This section is optional. These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.

When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data. With production data, steps must be taken to migrate the existing data. This section provides an example of fixing FK constraint violations. Don't make these code changes without a backup. Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.

The *{timestamp}_ComplexDataModel.cs* file contains the following code:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table. The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.

To make the `ComplexDataModel` migration work with existing data:

* Change the code to give the new column (`DepartmentID`) a default value.
* Create a fake department named "Temp" to act as the default department.

#### Fix the foreign key constraints

In the `ComplexDataModel` migration class, update the `Up` method:

* Open the *{timestamp}_ComplexDataModel.cs* file.
* Comment out the line of code that adds the `DepartmentID` column to the `Course` table.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Add the following highlighted code. The new code goes after the `.CreateTable( name: "Department"` block:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.

The way of handling the situation shown here is simplified for this tutorial. A production app would:

* Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.
* Not use the "Temp" department or the default value for `Course.DepartmentID`.

# [Visual Studio](#tab/visual-studio)

* In the **Package Manager Console** (PMC), run the following command:

  ```powershell
  Update-Database
  ```

Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables. (Cascade delete will take care of the Enrollment table.)

# [Visual Studio Code](#tab/visual-studio-code)

* If you're using SQL Server LocalDB with Visual Studio Code, run the following command:

  ```dotnetcli
  dotnet ef database update
  ```

---

Run the app. Running the app runs the `DbInitializer.Initialize` method. The `DbInitializer.Initialize` populates the new database.
-->

## <a name="next-steps"></a><span data-ttu-id="59502-385">后续步骤</span><span class="sxs-lookup"><span data-stu-id="59502-385">Next steps</span></span>

<span data-ttu-id="59502-386">接下来的两个教程演示如何读取和更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="59502-386">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="59502-387">[上一个教程](xref:data/ef-rp/migrations)
> [下一个教程](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="59502-387">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="59502-388">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-388">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="59502-389">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="59502-389">In this tutorial:</span></span>

* <span data-ttu-id="59502-390">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="59502-390">More entities and relationships are added.</span></span>
* <span data-ttu-id="59502-391">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-391">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="59502-392">完成的数据模型如下图所示：</span><span class="sxs-lookup"><span data-stu-id="59502-392">The completed data model is shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="59502-394">Student 实体</span><span class="sxs-lookup"><span data-stu-id="59502-394">The Student entity</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="59502-396">使用以下代码替换 Models/Student.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="59502-396">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="59502-397">前面的代码将添加一个 `FullName` 属性，并将以下特性添加到现有属性：</span><span class="sxs-lookup"><span data-stu-id="59502-397">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="59502-398">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="59502-398">The FullName calculated property</span></span>

<span data-ttu-id="59502-399">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="59502-399">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="59502-400">无法设置 `FullName`，因此它只有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="59502-400">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="59502-401">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="59502-401">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="59502-402">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="59502-402">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="59502-403">对于学生注册日期，目前所有页面都显示时间和日期，但只有日期是相关的。</span><span class="sxs-lookup"><span data-stu-id="59502-403">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="59502-404">使用数据注释特性，可更改一次代码，修复每个页面中数据的显示格式。</span><span class="sxs-lookup"><span data-stu-id="59502-404">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="59502-405">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="59502-405">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="59502-406">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="59502-406">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="59502-407">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="59502-407">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="59502-408">例如：</span><span class="sxs-lookup"><span data-stu-id="59502-408">For example:</span></span>

* <span data-ttu-id="59502-409">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="59502-409">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="59502-410">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="59502-410">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="59502-411">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性。</span><span class="sxs-lookup"><span data-stu-id="59502-411">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="59502-412">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="59502-412">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="59502-413">DisplayFormat 特性</span><span class="sxs-lookup"><span data-stu-id="59502-413">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="59502-414">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="59502-414">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="59502-415">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="59502-415">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="59502-416">`DisplayFormat` 特性用于显式指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="59502-416">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="59502-417">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="59502-417">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="59502-418">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="59502-418">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="59502-419">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="59502-419">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="59502-420">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="59502-420">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="59502-421">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="59502-421">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="59502-422">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="59502-422">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="59502-423">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="59502-423">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="59502-424">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="59502-424">The browser can enable HTML5 features.</span></span> <span data-ttu-id="59502-425">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接和客户端输入验证。</span><span class="sxs-lookup"><span data-stu-id="59502-425">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="59502-426">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="59502-426">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="59502-427">有关详细信息，请参阅 [\<input> 标记帮助程序文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="59502-427">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="59502-428">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="59502-428">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="59502-429">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="59502-429">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="59502-430">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="59502-430">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="59502-431">显示的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="59502-431">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="59502-432">[稍后](#the-required-attribute)显示一个设置最小字符串长度的示例。</span><span class="sxs-lookup"><span data-stu-id="59502-432">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="59502-433">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="59502-433">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="59502-434">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="59502-434">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="59502-435">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="59502-435">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="59502-436">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 特性可用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="59502-436">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="59502-437">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="59502-437">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="59502-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-439">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器 。</span><span class="sxs-lookup"><span data-stu-id="59502-439">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="59502-441">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="59502-441">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="59502-442">名称字段具有类型 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="59502-442">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="59502-443">在本教程的后续部分创建和应用迁移时，因为字符串的长度特性，名称字段将变为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="59502-443">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-445">在 SQLite 工具中检查 `Student` 表的列定义。</span><span class="sxs-lookup"><span data-stu-id="59502-445">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="59502-446">名称字段具有类型 `Text`。</span><span class="sxs-lookup"><span data-stu-id="59502-446">The name fields have type `Text`.</span></span> <span data-ttu-id="59502-447">请注意，首个名称字段名为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="59502-447">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="59502-448">下一节会将该列的名称更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="59502-448">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="59502-449">Column 特性</span><span class="sxs-lookup"><span data-stu-id="59502-449">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="59502-450">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="59502-450">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="59502-451">在 `Student` 模型中，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="59502-451">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="59502-452">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="59502-452">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="59502-453">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="59502-453">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="59502-454">使用 `[Column]` 特性，数据模型中的 `Student.FirstMidName` 可映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="59502-454">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="59502-455">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="59502-455">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="59502-456">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="59502-456">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="59502-457">这种差异将通过在本教程后面添加迁移来解决。</span><span class="sxs-lookup"><span data-stu-id="59502-457">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="59502-458">Required 特性</span><span class="sxs-lookup"><span data-stu-id="59502-458">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="59502-459">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="59502-459">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="59502-460">值类型（`DateTime`、`int` 和 `double`）等不可为 null 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="59502-460">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="59502-461">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="59502-461">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="59502-462">`Required` 特性必须与 `MinimumLength` 结合使用才能强制执行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="59502-462">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="59502-463">`MinimumLength` 和 `Required` 允许通过空格来满足验证。</span><span class="sxs-lookup"><span data-stu-id="59502-463">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="59502-464">使用 `RegularExpression` 特性来完全控制字符串。</span><span class="sxs-lookup"><span data-stu-id="59502-464">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="59502-465">Display 特性</span><span class="sxs-lookup"><span data-stu-id="59502-465">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="59502-466">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="59502-466">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="59502-467">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="59502-467">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="59502-468">创建迁移</span><span class="sxs-lookup"><span data-stu-id="59502-468">Create a migration</span></span>

<span data-ttu-id="59502-469">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="59502-469">Run the app and go to the Students page.</span></span> <span data-ttu-id="59502-470">此时引发异常。</span><span class="sxs-lookup"><span data-stu-id="59502-470">An exception is thrown.</span></span> <span data-ttu-id="59502-471">`[Column]` 特性导致 EF 希望查找名为 `FirstName` 的列，但数据库中的列名称仍为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="59502-471">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-472">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-473">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="59502-473">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="59502-474">在 PMC 中，输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-474">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="59502-475">其中的第一个命令将生成以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="59502-475">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="59502-476">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="59502-476">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="59502-477">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="59502-477">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="59502-478">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="59502-478">Open the Student table in SSOX:</span></span>

  ![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="59502-480">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="59502-480">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="59502-481">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="59502-481">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="59502-482">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="59502-482">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-483">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-483">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-484">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="59502-484">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="59502-485">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="59502-485">Open a command window in the project folder.</span></span> <span data-ttu-id="59502-486">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-486">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="59502-487">数据库更新命令显示类似于以下示例的错误：</span><span class="sxs-lookup"><span data-stu-id="59502-487">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="59502-488">在本教程中，解决此错误的方法是删除并重新创建初始迁移。</span><span class="sxs-lookup"><span data-stu-id="59502-488">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="59502-489">有关详细信息，请参阅[迁移教程](xref:data/ef-rp/migrations)顶部的 SQLite 警告说明。</span><span class="sxs-lookup"><span data-stu-id="59502-489">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="59502-490">删除“Migrations”文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-490">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="59502-491">运行以下命令以删除数据库，创建新的初始迁移，并应用该迁移：</span><span class="sxs-lookup"><span data-stu-id="59502-491">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="59502-492">在 SQLite 工具中检查 Student 表。</span><span class="sxs-lookup"><span data-stu-id="59502-492">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="59502-493">以前为 FirstMidName 的列现在为 FirstName。</span><span class="sxs-lookup"><span data-stu-id="59502-493">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="59502-494">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="59502-494">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="59502-495">请注意，时间和日期既未输入，也未显示。</span><span class="sxs-lookup"><span data-stu-id="59502-495">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="59502-496">选择“新建”，尝试输入不超过 50 个字符的名称。</span><span class="sxs-lookup"><span data-stu-id="59502-496">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="59502-497">在以下部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="59502-497">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="59502-498">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="59502-498">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="59502-499">Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="59502-499">The Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="59502-501">用以下代码创建 Models/Instructor.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-501">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="59502-502">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="59502-502">Multiple attributes can be on one line.</span></span> <span data-ttu-id="59502-503">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="59502-503">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="59502-504">导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-504">Navigation properties</span></span>

<span data-ttu-id="59502-505">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-505">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="59502-506">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="59502-506">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="59502-507">讲师最多可以有一个办公室，因此 `OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-507">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="59502-508">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-508">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="59502-509">OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-509">The OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="59502-511">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-511">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="59502-512">Key 特性</span><span class="sxs-lookup"><span data-stu-id="59502-512">The Key attribute</span></span>

<span data-ttu-id="59502-513">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="59502-513">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="59502-514">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="59502-514">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="59502-515">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-515">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="59502-516">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="59502-516">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="59502-517">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为 `InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="59502-517">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="59502-518">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="59502-518">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="59502-519">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="59502-519">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="59502-520">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-520">The Instructor navigation property</span></span>

<span data-ttu-id="59502-521">`Instructor.OfficeAssignment` 导航属性可以为 null，因为给定的讲师可能没有 `OfficeAssignment` 行。</span><span class="sxs-lookup"><span data-stu-id="59502-521">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="59502-522">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-522">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="59502-523">`OfficeAssignment.Instructor` 导航属性将始终具有一个 Instructor 实体，因为外键 `InstructorID` 类型为 `int`，不可为 null 的值类型。</span><span class="sxs-lookup"><span data-stu-id="59502-523">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="59502-524">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-524">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="59502-525">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="59502-525">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="59502-526">Course 实体</span><span class="sxs-lookup"><span data-stu-id="59502-526">The Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="59502-528">用以下代码更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-528">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="59502-529">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="59502-529">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="59502-530">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-530">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="59502-531">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-531">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="59502-532">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有外键属性。</span><span class="sxs-lookup"><span data-stu-id="59502-532">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="59502-533">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="59502-533">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="59502-534">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="59502-534">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="59502-535">然而，在数据模型中显式包含 FK 可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="59502-535">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="59502-536">例如，假设某个模型中不包含 FK 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="59502-536">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="59502-537">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="59502-537">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="59502-538">如果未显式加载，则 `Department` 属性为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-538">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="59502-539">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-539">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="59502-540">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-540">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="59502-541">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="59502-541">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="59502-542">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="59502-542">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="59502-543">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="59502-543">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="59502-544">数据库生成通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="59502-544">Database-generated is generally the best approach.</span></span> <span data-ttu-id="59502-545">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="59502-545">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="59502-546">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="59502-546">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="59502-547">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-547">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="59502-548">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="59502-548">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="59502-549">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="59502-549">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-550">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-550">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-551">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-551">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="59502-552">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-552">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="59502-553">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="59502-553">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="59502-554">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="59502-554">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="59502-555">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="59502-555">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="59502-556">Department 实体</span><span class="sxs-lookup"><span data-stu-id="59502-556">The Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="59502-558">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-558">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="59502-559">Column 特性</span><span class="sxs-lookup"><span data-stu-id="59502-559">The Column attribute</span></span>

<span data-ttu-id="59502-560">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="59502-560">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="59502-561">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="59502-561">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="59502-562">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="59502-562">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="59502-563">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="59502-563">Column mapping is generally not required.</span></span> <span data-ttu-id="59502-564">EF Core 基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="59502-564">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="59502-565">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="59502-565">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="59502-566">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="59502-566">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-567">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-567">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-568">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-568">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="59502-569">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="59502-569">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="59502-570">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="59502-570">An administrator is always an instructor.</span></span> <span data-ttu-id="59502-571">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="59502-571">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="59502-572">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="59502-572">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="59502-573">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-573">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="59502-574">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-574">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="59502-575">按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="59502-575">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="59502-576">此默认行为可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="59502-576">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="59502-577">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="59502-577">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="59502-578">例如，如果将 `Department.InstructorID` 属性定义为不可为 null，EF Core 将配置级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="59502-578">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="59502-579">在这种情况下，指定为管理员的讲师被删除时，该学院将被删除。</span><span class="sxs-lookup"><span data-stu-id="59502-579">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="59502-580">在这种情况下，限制规则将更有意义。</span><span class="sxs-lookup"><span data-stu-id="59502-580">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="59502-581">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则并禁用级联规则。</span><span class="sxs-lookup"><span data-stu-id="59502-581">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="59502-582">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-582">The Enrollment entity</span></span>

<span data-ttu-id="59502-583">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="59502-583">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="59502-585">用以下代码更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-585">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-586">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-586">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-587">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-587">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="59502-588">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-588">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="59502-589">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-589">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="59502-590">多对多关系</span><span class="sxs-lookup"><span data-stu-id="59502-590">Many-to-Many Relationships</span></span>

<span data-ttu-id="59502-591">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-591">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="59502-592">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="59502-592">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="59502-593">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="59502-593">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="59502-594">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="59502-594">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="59502-595">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="59502-595">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="59502-596">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="59502-596">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="59502-598">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-598">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="59502-599">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="59502-599">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="59502-600">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="59502-600">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="59502-601">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-601">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="59502-602">注意：EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="59502-602">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="59502-603">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="59502-603">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="59502-604">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-604">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="59502-606">用以下代码创建 Models/CourseAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-606">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="59502-607">“讲师-课程”的多对多关系要求使用联接表，而该联接表的实体则为 CourseAssignment。</span><span class="sxs-lookup"><span data-stu-id="59502-607">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="59502-609">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="59502-609">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="59502-610">例如，使用此模式的“讲师-课程”联接表将是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="59502-610">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="59502-611">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="59502-611">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="59502-612">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="59502-612">Data models start out simple and grow.</span></span> <span data-ttu-id="59502-613">无有效负载的联接表 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="59502-613">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="59502-614">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="59502-614">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="59502-615">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="59502-615">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="59502-616">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="59502-616">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="59502-617">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="59502-617">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="59502-618">组合键</span><span class="sxs-lookup"><span data-stu-id="59502-618">Composite key</span></span>

<span data-ttu-id="59502-619">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="59502-619">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="59502-620">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-620">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="59502-621">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-621">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="59502-622">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="59502-622">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="59502-623">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-623">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="59502-624">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="59502-624">The composite key ensures that:</span></span>

* <span data-ttu-id="59502-625">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="59502-625">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="59502-626">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="59502-626">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="59502-627">不允许同一讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="59502-627">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="59502-628">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="59502-628">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="59502-629">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="59502-629">To prevent such duplicates:</span></span>

* <span data-ttu-id="59502-630">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="59502-630">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="59502-631">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="59502-631">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="59502-632">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="59502-632">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="59502-633">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="59502-633">Update the database context</span></span>

<span data-ttu-id="59502-634">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-634">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="59502-635">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-635">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="59502-636">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="59502-636">Fluent API alternative to attributes</span></span>

<span data-ttu-id="59502-637">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为。</span><span class="sxs-lookup"><span data-stu-id="59502-637">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="59502-638">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="59502-638">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="59502-639">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="59502-639">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="59502-640">在本教程中，fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="59502-640">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="59502-641">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="59502-641">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="59502-642">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="59502-642">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="59502-643">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="59502-643">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="59502-644">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="59502-644">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="59502-645">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="59502-645">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="59502-646">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="59502-646">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="59502-647">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="59502-647">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="59502-648">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="59502-648">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="59502-649">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="59502-649">Choose one of these two approaches.</span></span>
* <span data-ttu-id="59502-650">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="59502-650">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="59502-651">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="59502-651">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="59502-652">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="59502-652">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="59502-653">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="59502-653">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="59502-654">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="59502-654">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="59502-655">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="59502-655">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="59502-656">实体关系图</span><span class="sxs-lookup"><span data-stu-id="59502-656">Entity diagram</span></span>

<span data-ttu-id="59502-657">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="59502-657">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="59502-659">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="59502-659">The preceding diagram shows:</span></span>

* <span data-ttu-id="59502-660">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="59502-660">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="59502-661">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="59502-661">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="59502-662">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="59502-662">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="59502-663">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="59502-663">Seed the database</span></span>

<span data-ttu-id="59502-664">更新 Data/DbInitializer.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="59502-664">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="59502-665">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="59502-665">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="59502-666">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="59502-666">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="59502-667">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="59502-667">The sample data is used for testing.</span></span> <span data-ttu-id="59502-668">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="59502-668">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="59502-669">添加迁移</span><span class="sxs-lookup"><span data-stu-id="59502-669">Add a migration</span></span>

<span data-ttu-id="59502-670">生成项目。</span><span class="sxs-lookup"><span data-stu-id="59502-670">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-671">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-671">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-672">在 PMC 中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="59502-672">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="59502-673">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="59502-673">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="59502-674">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="59502-674">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="59502-675">下一节将介绍如何处理此错误。</span><span class="sxs-lookup"><span data-stu-id="59502-675">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-676">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-676">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-677">如果添加迁移并运行 `database update` 命令，则将生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="59502-677">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="59502-678">下一节将介绍如何避免此错误。</span><span class="sxs-lookup"><span data-stu-id="59502-678">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="59502-679">应用迁移或删除并重新创建</span><span class="sxs-lookup"><span data-stu-id="59502-679">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="59502-680">现已有一个数据库，需要考虑如何将更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="59502-680">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="59502-681">本教程演示两种替代方法：</span><span class="sxs-lookup"><span data-stu-id="59502-681">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="59502-682">[删除并重新创建数据库](#drop)。</span><span class="sxs-lookup"><span data-stu-id="59502-682">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="59502-683">如果使用 SQLite，请选择此部分。</span><span class="sxs-lookup"><span data-stu-id="59502-683">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="59502-684">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="59502-684">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="59502-685">本部分的说明仅适用于 SQL Server，不适用于 SQLite。</span><span class="sxs-lookup"><span data-stu-id="59502-685">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="59502-686">这两个选项都适用于 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="59502-686">Either choice works for SQL Server.</span></span> <span data-ttu-id="59502-687">虽然应用迁移方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="59502-687">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="59502-688">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="59502-688">Drop and re-create the database</span></span>

<span data-ttu-id="59502-689">如果使用 SQL Server 并且想要在以下部分执行应用迁移方法，[请跳过此部分](#apply-the-migration)。</span><span class="sxs-lookup"><span data-stu-id="59502-689">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="59502-690">若要强制 EF Core 创建新的数据库，请删除并更新该数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-690">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-691">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-691">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59502-692">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-692">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="59502-693">删除“Migrations”文件夹，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-693">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-694">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-694">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="59502-695">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-695">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="59502-696">项目文件夹包含 ContosoUniversity.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="59502-696">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="59502-697">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="59502-697">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="59502-698">删除“Migrations”文件夹，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-698">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="59502-699">运行应用。</span><span class="sxs-lookup"><span data-stu-id="59502-699">Run the app.</span></span> <span data-ttu-id="59502-700">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="59502-700">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="59502-701">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="59502-701">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-702">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-702">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-703">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-703">Open the database in SSOX:</span></span>

* <span data-ttu-id="59502-704">如果之前已打开过 SSOX，请单击“刷新”按钮。</span><span class="sxs-lookup"><span data-stu-id="59502-704">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="59502-705">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="59502-705">Expand the **Tables** node.</span></span> <span data-ttu-id="59502-706">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="59502-706">The created tables are displayed.</span></span>

  ![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="59502-708">查看 CourseAssignment 表：</span><span class="sxs-lookup"><span data-stu-id="59502-708">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="59502-709">右键单击 CourseAssignment 表，然后选择“查看数据” 。</span><span class="sxs-lookup"><span data-stu-id="59502-709">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="59502-710">验证 CourseAssignment 表包含数据。</span><span class="sxs-lookup"><span data-stu-id="59502-710">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-712">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-712">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-713">使用 SQLite 工具检查数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-713">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="59502-714">新的表和列。</span><span class="sxs-lookup"><span data-stu-id="59502-714">New tables and columns.</span></span>
* <span data-ttu-id="59502-715">表中设定种子的数据，例如 CourseAssignment 表。</span><span class="sxs-lookup"><span data-stu-id="59502-715">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="59502-716">应用迁移</span><span class="sxs-lookup"><span data-stu-id="59502-716">Apply the migration</span></span>

<span data-ttu-id="59502-717">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="59502-717">This section is optional.</span></span> <span data-ttu-id="59502-718">这些步骤仅适用于 SQL Server LocalDB，并且仅当跳过前面的[删除并重新创建数据库](#drop)部分时才适用。</span><span class="sxs-lookup"><span data-stu-id="59502-718">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="59502-719">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="59502-719">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="59502-720">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="59502-720">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="59502-721">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="59502-721">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="59502-722">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="59502-722">Don't make these code changes without a backup.</span></span> <span data-ttu-id="59502-723">如果已完成上述[删除并重新创建数据库](#drop)部分，请不要更改这些代码。</span><span class="sxs-lookup"><span data-stu-id="59502-723">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="59502-724">{timestamp}_ComplexDataModel.cs 文件包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="59502-724">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="59502-725">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="59502-725">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="59502-726">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="59502-726">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="59502-727">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="59502-727">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="59502-728">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-728">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="59502-729">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="59502-729">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="59502-730">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="59502-730">Fix the foreign key constraints</span></span>

<span data-ttu-id="59502-731">在 `ComplexDataModel` 迁移类中，更新 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="59502-731">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="59502-732">打开 {timestamp}_ComplexDataModel.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="59502-732">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="59502-733">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="59502-733">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="59502-734">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="59502-734">Add the following highlighted code.</span></span> <span data-ttu-id="59502-735">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="59502-735">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="59502-736">经过上面的更改，现有的 `Course` 行将在 `ComplexDataModel.Up` 方法运行后与“临时”院系建立联系。</span><span class="sxs-lookup"><span data-stu-id="59502-736">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="59502-737">本教程简化了此处所示的处理方式。</span><span class="sxs-lookup"><span data-stu-id="59502-737">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="59502-738">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="59502-738">A production app would:</span></span>

* <span data-ttu-id="59502-739">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="59502-739">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="59502-740">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-740">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-741">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-741">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="59502-742">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-742">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="59502-743">由于 `DbInitializer.Initialize` 方法仅适用于空数据库，因此请使用 SSOX 删除 Student 表和 Course 表中的所有行。</span><span class="sxs-lookup"><span data-stu-id="59502-743">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="59502-744">（级联删除将负责 Enrollment 表。）</span><span class="sxs-lookup"><span data-stu-id="59502-744">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-745">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-745">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="59502-746">如果在 Visual Studio Code 中使用 SQL Server LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-746">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="59502-747">运行应用。</span><span class="sxs-lookup"><span data-stu-id="59502-747">Run the app.</span></span> <span data-ttu-id="59502-748">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="59502-748">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="59502-749">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="59502-749">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="59502-750">后续步骤</span><span class="sxs-lookup"><span data-stu-id="59502-750">Next steps</span></span>

<span data-ttu-id="59502-751">接下来的两个教程演示如何读取和更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="59502-751">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="59502-752">[上一个教程](xref:data/ef-rp/migrations)
> [下一个教程](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="59502-752">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59502-753">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-753">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="59502-754">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="59502-754">In this tutorial:</span></span>

* <span data-ttu-id="59502-755">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="59502-755">More entities and relationships are added.</span></span>
* <span data-ttu-id="59502-756">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-756">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="59502-757">已完成数据模型的实体类如下图所示：</span><span class="sxs-lookup"><span data-stu-id="59502-757">The entity classes for the completed data model are shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="59502-759">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="59502-759">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="59502-760">使用特性自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="59502-760">Customize the data model with attributes</span></span>

<span data-ttu-id="59502-761">此部分将使用特性自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="59502-761">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="59502-762">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="59502-762">The DataType attribute</span></span>

<span data-ttu-id="59502-763">学生页面当前显示注册日期。</span><span class="sxs-lookup"><span data-stu-id="59502-763">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="59502-764">通常情况下，日期字段仅显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="59502-764">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="59502-765">用以下突出显示的代码更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-765">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="59502-766">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="59502-766">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="59502-767">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="59502-767">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="59502-768">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="59502-768">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="59502-769">例如：</span><span class="sxs-lookup"><span data-stu-id="59502-769">For example:</span></span>

* <span data-ttu-id="59502-770">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="59502-770">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="59502-771">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="59502-771">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="59502-772">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="59502-772">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="59502-773">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="59502-773">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="59502-774">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="59502-774">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="59502-775">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="59502-775">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="59502-776">`DisplayFormat` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="59502-776">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="59502-777">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="59502-777">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="59502-778">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="59502-778">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="59502-779">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="59502-779">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="59502-780">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="59502-780">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="59502-781">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="59502-781">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="59502-782">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="59502-782">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="59502-783">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="59502-783">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="59502-784">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="59502-784">The browser can enable HTML5 features.</span></span> <span data-ttu-id="59502-785">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接、客户端输入验证等。</span><span class="sxs-lookup"><span data-stu-id="59502-785">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="59502-786">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="59502-786">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="59502-787">有关详细信息，请参阅 [\<input> 标记帮助程序文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="59502-787">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="59502-788">运行应用。</span><span class="sxs-lookup"><span data-stu-id="59502-788">Run the app.</span></span> <span data-ttu-id="59502-789">导航到学生索引页。</span><span class="sxs-lookup"><span data-stu-id="59502-789">Navigate to the Students Index page.</span></span> <span data-ttu-id="59502-790">将不再显示时间。</span><span class="sxs-lookup"><span data-stu-id="59502-790">Times are no longer displayed.</span></span> <span data-ttu-id="59502-791">使用 `Student` 模型的每个视图将显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="59502-791">Every view that uses the `Student` model displays the date without time.</span></span>

![“学生”索引页显示不带时间的日期](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="59502-793">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="59502-793">The StringLength attribute</span></span>

<span data-ttu-id="59502-794">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="59502-794">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="59502-795">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="59502-795">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="59502-796">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="59502-796">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="59502-797">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="59502-797">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="59502-798">使用以下代码更新 `Student` 模型：</span><span class="sxs-lookup"><span data-stu-id="59502-798">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="59502-799">上面的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="59502-799">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="59502-800">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="59502-800">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="59502-801">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 特性用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="59502-801">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="59502-802">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="59502-802">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="59502-803">运行应用：</span><span class="sxs-lookup"><span data-stu-id="59502-803">Run the app:</span></span>

* <span data-ttu-id="59502-804">导航到学生页。</span><span class="sxs-lookup"><span data-stu-id="59502-804">Navigate to the Students page.</span></span>
* <span data-ttu-id="59502-805">选择“新建”并输入不超过 50 个字符的名称。</span><span class="sxs-lookup"><span data-stu-id="59502-805">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="59502-806">选择“创建”时，客户端验证会显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="59502-806">Select **Create**, client-side validation shows an error message.</span></span>

![显示字符串长度错误的“学生索引”页](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="59502-808">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器 。</span><span class="sxs-lookup"><span data-stu-id="59502-808">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="59502-810">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="59502-810">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="59502-811">名称字段的类型为 `nvarchar(MAX)`，因为数据库上尚未运行迁移。</span><span class="sxs-lookup"><span data-stu-id="59502-811">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="59502-812">稍后在本教程中运行迁移时，名称字段将变成 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="59502-812">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="59502-813">Column 特性</span><span class="sxs-lookup"><span data-stu-id="59502-813">The Column attribute</span></span>

<span data-ttu-id="59502-814">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="59502-814">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="59502-815">在本部分，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="59502-815">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="59502-816">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="59502-816">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="59502-817">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="59502-817">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="59502-818">用以下突出显示的代码更新 *Student.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="59502-818">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="59502-819">进行上述更改后，应用中的 `Student.FirstMidName` 将映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="59502-819">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="59502-820">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="59502-820">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="59502-821">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="59502-821">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="59502-822">如果在执行迁移前运行应用，则会生成如下异常：</span><span class="sxs-lookup"><span data-stu-id="59502-822">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="59502-823">若要更新数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-823">To update the DB:</span></span>

* <span data-ttu-id="59502-824">生成项目。</span><span class="sxs-lookup"><span data-stu-id="59502-824">Build the project.</span></span>
* <span data-ttu-id="59502-825">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="59502-825">Open a command window in the project folder.</span></span> <span data-ttu-id="59502-826">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-826">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-827">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-827">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-828">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-828">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="59502-829">`migrations add ColumnFirstName` 命令将生成如下警告消息：</span><span class="sxs-lookup"><span data-stu-id="59502-829">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="59502-830">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="59502-830">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="59502-831">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="59502-831">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="59502-832">测试应用。</span><span class="sxs-lookup"><span data-stu-id="59502-832">Test the app.</span></span>

<span data-ttu-id="59502-833">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="59502-833">Open the Student table in SSOX:</span></span>

![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="59502-835">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="59502-835">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="59502-836">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="59502-836">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="59502-837">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="59502-837">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="59502-838">在下一部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="59502-838">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="59502-839">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="59502-839">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="59502-840">Student 实体更新</span><span class="sxs-lookup"><span data-stu-id="59502-840">Student entity update</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="59502-842">用以下代码更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-842">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="59502-843">Required 特性</span><span class="sxs-lookup"><span data-stu-id="59502-843">The Required attribute</span></span>

<span data-ttu-id="59502-844">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="59502-844">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="59502-845">值类型（`DateTime`、`int`、`double`）等不可为 NULL 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="59502-845">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="59502-846">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="59502-846">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="59502-847">不能用 `StringLength` 特性中的最短长度参数替换 `Required` 特性：</span><span class="sxs-lookup"><span data-stu-id="59502-847">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="59502-848">Display 特性</span><span class="sxs-lookup"><span data-stu-id="59502-848">The Display attribute</span></span>

<span data-ttu-id="59502-849">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="59502-849">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="59502-850">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="59502-850">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="59502-851">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="59502-851">The FullName calculated property</span></span>

<span data-ttu-id="59502-852">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="59502-852">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="59502-853">`FullName` 不能设置并且仅具有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="59502-853">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="59502-854">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="59502-854">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="59502-855">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="59502-855">Create the Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="59502-857">用以下代码创建 Models/Instructor.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-857">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="59502-858">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="59502-858">Multiple attributes can be on one line.</span></span> <span data-ttu-id="59502-859">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="59502-859">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="59502-860">CourseAssignments 和 OfficeAssignment 导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-860">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="59502-861">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-861">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="59502-862">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="59502-862">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="59502-863">如果导航属性包含多个实体：</span><span class="sxs-lookup"><span data-stu-id="59502-863">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="59502-864">它必须是可在其中添加、删除和更新实体的列表类型。</span><span class="sxs-lookup"><span data-stu-id="59502-864">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="59502-865">导航属性类型包括：</span><span class="sxs-lookup"><span data-stu-id="59502-865">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="59502-866">如果指定了 `ICollection<T>`，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="59502-866">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="59502-867">`CourseAssignment` 实体在“多对多关系”部分进行介绍。</span><span class="sxs-lookup"><span data-stu-id="59502-867">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="59502-868">Contoso University 业务规则规定一名讲师最多可获得一间办公室。</span><span class="sxs-lookup"><span data-stu-id="59502-868">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="59502-869">`OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-869">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="59502-870">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-870">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="59502-871">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-871">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="59502-873">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-873">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="59502-874">Key 特性</span><span class="sxs-lookup"><span data-stu-id="59502-874">The Key attribute</span></span>

<span data-ttu-id="59502-875">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="59502-875">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="59502-876">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="59502-876">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="59502-877">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-877">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="59502-878">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="59502-878">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="59502-879">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为：</span><span class="sxs-lookup"><span data-stu-id="59502-879">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="59502-880">`InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="59502-880">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="59502-881">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="59502-881">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="59502-882">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="59502-882">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="59502-883">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-883">The Instructor navigation property</span></span>

<span data-ttu-id="59502-884">`Instructor` 实体的 `OfficeAssignment` 导航属性可以为 NULL，因为：</span><span class="sxs-lookup"><span data-stu-id="59502-884">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="59502-885">引用类型（例如，类可以为 NULL）。</span><span class="sxs-lookup"><span data-stu-id="59502-885">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="59502-886">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-886">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="59502-887">`OfficeAssignment` 实体具有不可为 NULL 的 `Instructor` 导航属性，因为：</span><span class="sxs-lookup"><span data-stu-id="59502-887">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="59502-888">`InstructorID` 不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-888">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="59502-889">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="59502-889">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="59502-890">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="59502-890">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="59502-891">`[Required]` 特性可以应用于 `Instructor` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-891">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="59502-892">上面的代码指定必须存在相关的讲师。</span><span class="sxs-lookup"><span data-stu-id="59502-892">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="59502-893">上面的代码没有必要，因为 `InstructorID` 外键（也是 PK）不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-893">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="59502-894">修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="59502-894">Modify the Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="59502-896">用以下代码更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-896">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="59502-897">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="59502-897">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="59502-898">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-898">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="59502-899">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-899">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="59502-900">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="59502-900">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="59502-901">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="59502-901">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="59502-902">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="59502-902">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="59502-903">数据模型中包含 FK 后可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="59502-903">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="59502-904">例如，假设某个模型中不包含 FK 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="59502-904">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="59502-905">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="59502-905">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="59502-906">如果未显式加载 `Department` 实体，则该实体将为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-906">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="59502-907">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-907">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="59502-908">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="59502-908">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="59502-909">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="59502-909">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="59502-910">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="59502-910">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="59502-911">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="59502-911">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="59502-912">由数据库生成 PK 值通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="59502-912">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="59502-913">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="59502-913">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="59502-914">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="59502-914">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="59502-915">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-915">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="59502-916">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="59502-916">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="59502-917">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="59502-917">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-918">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-918">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-919">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-919">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="59502-920">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="59502-920">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="59502-921">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="59502-921">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="59502-922">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="59502-922">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="59502-923">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="59502-923">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="59502-924">创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="59502-924">Create the Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="59502-926">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-926">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="59502-927">Column 特性</span><span class="sxs-lookup"><span data-stu-id="59502-927">The Column attribute</span></span>

<span data-ttu-id="59502-928">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="59502-928">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="59502-929">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="59502-929">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="59502-930">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="59502-930">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="59502-931">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="59502-931">Column mapping is generally not required.</span></span> <span data-ttu-id="59502-932">EF Core 通常基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="59502-932">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="59502-933">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="59502-933">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="59502-934">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="59502-934">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-935">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-935">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-936">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-936">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="59502-937">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="59502-937">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="59502-938">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="59502-938">An administrator is always an instructor.</span></span> <span data-ttu-id="59502-939">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="59502-939">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="59502-940">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="59502-940">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="59502-941">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-941">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="59502-942">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-942">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="59502-943">注意：按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="59502-943">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="59502-944">级联删除可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="59502-944">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="59502-945">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="59502-945">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="59502-946">例如，如果 `Department.InstructorID` 属性定义为不可为 NULL：</span><span class="sxs-lookup"><span data-stu-id="59502-946">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="59502-947">EF Core 会配置级联删除规则，以在删除讲师时删除院系。</span><span class="sxs-lookup"><span data-stu-id="59502-947">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="59502-948">在删除讲师时删除院系并不是预期行为。</span><span class="sxs-lookup"><span data-stu-id="59502-948">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="59502-949">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则而不是级联规则。</span><span class="sxs-lookup"><span data-stu-id="59502-949">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="59502-950">上面的代码会针对“系-讲师”关系禁用级联删除。</span><span class="sxs-lookup"><span data-stu-id="59502-950">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="59502-951">更新 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-951">Update the Enrollment entity</span></span>

<span data-ttu-id="59502-952">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="59502-952">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="59502-954">用以下代码更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="59502-954">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="59502-955">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="59502-955">Foreign key and navigation properties</span></span>

<span data-ttu-id="59502-956">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="59502-956">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="59502-957">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-957">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="59502-958">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="59502-958">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="59502-959">多对多关系</span><span class="sxs-lookup"><span data-stu-id="59502-959">Many-to-Many Relationships</span></span>

<span data-ttu-id="59502-960">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-960">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="59502-961">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="59502-961">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="59502-962">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="59502-962">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="59502-963">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="59502-963">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="59502-964">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="59502-964">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="59502-965">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="59502-965">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="59502-967">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-967">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="59502-968">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="59502-968">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="59502-969">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="59502-969">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="59502-970">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="59502-970">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="59502-971">注意：EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="59502-971">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="59502-972">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="59502-972">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="59502-973">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="59502-973">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="59502-975">用以下代码创建 Models/CourseAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-975">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="59502-976">讲师-课程</span><span class="sxs-lookup"><span data-stu-id="59502-976">Instructor-to-Courses</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="59502-978">讲师-课程的多对多关系：</span><span class="sxs-lookup"><span data-stu-id="59502-978">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="59502-979">要求必须用实体集表示联接表。</span><span class="sxs-lookup"><span data-stu-id="59502-979">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="59502-980">为纯联接表（无有效负载的表）。</span><span class="sxs-lookup"><span data-stu-id="59502-980">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="59502-981">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="59502-981">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="59502-982">例如，使用此模式的“讲师-课程”联接表是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="59502-982">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="59502-983">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="59502-983">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="59502-984">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="59502-984">Data models start out simple and grow.</span></span> <span data-ttu-id="59502-985">无有效负载联接 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="59502-985">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="59502-986">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="59502-986">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="59502-987">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="59502-987">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="59502-988">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="59502-988">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="59502-989">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="59502-989">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="59502-990">组合键</span><span class="sxs-lookup"><span data-stu-id="59502-990">Composite key</span></span>

<span data-ttu-id="59502-991">FK 不能为 NULL。</span><span class="sxs-lookup"><span data-stu-id="59502-991">FKs are not nullable.</span></span> <span data-ttu-id="59502-992">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="59502-992">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="59502-993">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-993">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="59502-994">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-994">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="59502-995">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="59502-995">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="59502-996">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-996">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="59502-997">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="59502-997">The composite key ensures:</span></span>

* <span data-ttu-id="59502-998">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="59502-998">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="59502-999">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="59502-999">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="59502-1000">不允许相同的讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="59502-1000">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="59502-1001">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="59502-1001">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="59502-1002">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="59502-1002">To prevent such duplicates:</span></span>

* <span data-ttu-id="59502-1003">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="59502-1003">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="59502-1004">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="59502-1004">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="59502-1005">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="59502-1005">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="59502-1006">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="59502-1006">Update the DB context</span></span>

<span data-ttu-id="59502-1007">将以下突出显示的代码添加到 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="59502-1007">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="59502-1008">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="59502-1008">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="59502-1009">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="59502-1009">Fluent API alternative to attributes</span></span>

<span data-ttu-id="59502-1010">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为。</span><span class="sxs-lookup"><span data-stu-id="59502-1010">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="59502-1011">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="59502-1011">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="59502-1012">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="59502-1012">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="59502-1013">在本教程中，Fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="59502-1013">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="59502-1014">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="59502-1014">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="59502-1015">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="59502-1015">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="59502-1016">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="59502-1016">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="59502-1017">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="59502-1017">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="59502-1018">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="59502-1018">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="59502-1019">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="59502-1019">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="59502-1020">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="59502-1020">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="59502-1021">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="59502-1021">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="59502-1022">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="59502-1022">Choose one of these two approaches.</span></span>
* <span data-ttu-id="59502-1023">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="59502-1023">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="59502-1024">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="59502-1024">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="59502-1025">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="59502-1025">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="59502-1026">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="59502-1026">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="59502-1027">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="59502-1027">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="59502-1028">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="59502-1028">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="59502-1029">显示关系的实体关系图</span><span class="sxs-lookup"><span data-stu-id="59502-1029">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="59502-1030">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="59502-1030">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="59502-1032">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="59502-1032">The preceding diagram shows:</span></span>

* <span data-ttu-id="59502-1033">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="59502-1033">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="59502-1034">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="59502-1034">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="59502-1035">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="59502-1035">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="59502-1036">使用测试数据为数据库设定种子</span><span class="sxs-lookup"><span data-stu-id="59502-1036">Seed the DB with Test Data</span></span>

<span data-ttu-id="59502-1037">更新 Data/DbInitializer.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="59502-1037">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="59502-1038">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="59502-1038">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="59502-1039">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="59502-1039">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="59502-1040">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="59502-1040">The sample data is used for testing.</span></span> <span data-ttu-id="59502-1041">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="59502-1041">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="59502-1042">添加迁移</span><span class="sxs-lookup"><span data-stu-id="59502-1042">Add a migration</span></span>

<span data-ttu-id="59502-1043">生成项目。</span><span class="sxs-lookup"><span data-stu-id="59502-1043">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-1044">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-1044">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-1045">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-1045">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="59502-1046">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="59502-1046">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="59502-1047">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="59502-1047">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="59502-1048">应用迁移</span><span class="sxs-lookup"><span data-stu-id="59502-1048">Apply the migration</span></span>

<span data-ttu-id="59502-1049">现已有一个数据库，需要考虑如何将未来的更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="59502-1049">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="59502-1050">本教程演示两种方法：</span><span class="sxs-lookup"><span data-stu-id="59502-1050">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="59502-1051">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="59502-1051">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="59502-1052">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="59502-1052">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="59502-1053">虽然此方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="59502-1053">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="59502-1054">**说明**：这是本教程的一个可选部分。</span><span class="sxs-lookup"><span data-stu-id="59502-1054">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="59502-1055">你可以执行删除和重新创建的相关步骤并跳过此部分。</span><span class="sxs-lookup"><span data-stu-id="59502-1055">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="59502-1056">如果希望执行本部分中的步骤，请勿执行删除和重新创建步骤。</span><span class="sxs-lookup"><span data-stu-id="59502-1056">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="59502-1057">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="59502-1057">Drop and re-create the database</span></span>

<span data-ttu-id="59502-1058">已更新 `DbInitializer` 中的代码将为新实体添加种子数据。</span><span class="sxs-lookup"><span data-stu-id="59502-1058">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="59502-1059">若要强制 EF Core 创建新的 DB，请删除并更新 DB：</span><span class="sxs-lookup"><span data-stu-id="59502-1059">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="59502-1060">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="59502-1060">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="59502-1061">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="59502-1061">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="59502-1062">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="59502-1062">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="59502-1063">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="59502-1063">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="59502-1064">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="59502-1064">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="59502-1065">项目文件夹包含 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="59502-1065">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="59502-1066">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="59502-1066">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="59502-1067">运行应用。</span><span class="sxs-lookup"><span data-stu-id="59502-1067">Run the app.</span></span> <span data-ttu-id="59502-1068">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="59502-1068">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="59502-1069">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="59502-1069">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="59502-1070">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="59502-1070">Open the DB in SSOX:</span></span>

* <span data-ttu-id="59502-1071">如果之前已打开过 SSOX，请单击“刷新”按钮。</span><span class="sxs-lookup"><span data-stu-id="59502-1071">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="59502-1072">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="59502-1072">Expand the **Tables** node.</span></span> <span data-ttu-id="59502-1073">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="59502-1073">The created tables are displayed.</span></span>

![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="59502-1075">查看 CourseAssignment 表：</span><span class="sxs-lookup"><span data-stu-id="59502-1075">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="59502-1076">右键单击 CourseAssignment 表，然后选择“查看数据” 。</span><span class="sxs-lookup"><span data-stu-id="59502-1076">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="59502-1077">验证 CourseAssignment 表包含数据。</span><span class="sxs-lookup"><span data-stu-id="59502-1077">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="59502-1079">将迁移应用到现有数据库</span><span class="sxs-lookup"><span data-stu-id="59502-1079">Apply the migration to the existing database</span></span>

<span data-ttu-id="59502-1080">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="59502-1080">This section is optional.</span></span> <span data-ttu-id="59502-1081">只有当跳过之前的[删除并重新创建数据库](#drop)部分时才可以执行上述步骤。</span><span class="sxs-lookup"><span data-stu-id="59502-1081">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="59502-1082">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="59502-1082">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="59502-1083">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="59502-1083">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="59502-1084">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="59502-1084">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="59502-1085">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="59502-1085">Don't make these code changes without a backup.</span></span> <span data-ttu-id="59502-1086">如果已完成上述部分并更新数据库，则不要执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="59502-1086">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="59502-1087">{timestamp}_ComplexDataModel.cs 文件包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="59502-1087">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="59502-1088">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="59502-1088">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="59502-1089">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="59502-1089">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="59502-1090">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="59502-1090">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="59502-1091">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-1091">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="59502-1092">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="59502-1092">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="59502-1093">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="59502-1093">Fix the foreign key constraints</span></span>

<span data-ttu-id="59502-1094">更新 `ComplexDataModel` 类 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="59502-1094">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="59502-1095">打开 {timestamp}_ComplexDataModel.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="59502-1095">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="59502-1096">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="59502-1096">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="59502-1097">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="59502-1097">Add the following highlighted code.</span></span> <span data-ttu-id="59502-1098">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="59502-1098">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="59502-1099">经过上面的更改，现有的 `Course` 行将在 `ComplexDataModel` `Up` 方法运行后与“临时”院系建立联系。</span><span class="sxs-lookup"><span data-stu-id="59502-1099">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="59502-1100">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="59502-1100">A production app would:</span></span>

* <span data-ttu-id="59502-1101">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="59502-1101">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="59502-1102">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="59502-1102">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="59502-1103">下一教程将介绍相关数据。</span><span class="sxs-lookup"><span data-stu-id="59502-1103">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59502-1104">其他资源</span><span class="sxs-lookup"><span data-stu-id="59502-1104">Additional resources</span></span>

* [<span data-ttu-id="59502-1105">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="59502-1105">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="59502-1106">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="59502-1106">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="59502-1107">[上一页](xref:data/ef-rp/migrations)
> [下一页](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="59502-1107">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end