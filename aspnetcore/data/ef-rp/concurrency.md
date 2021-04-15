---
title: 第 8 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 并发
author: rick-anderson
description: Razor 页面和实体框架教程系列第 8 部分。
ms.author: riande
ms.custom: mvc
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: d95a395ba91801fd066f0e75f7e5fe5b3ef46f73
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957478"
---
# <a name="part-8-razor-pages-with-ef-core-in-aspnet-core---concurrency"></a>第 8 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 并发

[Tom Dykstra](https://github.com/tdykstra) 和 [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

本教程介绍如何处理多个用户并发更新同一实体时出现的冲突。

## <a name="concurrency-conflicts"></a>并发冲突

在以下情况下，会发生并发冲突：

* 用户导航到实体的编辑页面。
* 第一个用户的更改还未写入数据库之前，另一用户更新同一实体。

如果未启用并发检测，则最后更新数据库的人员将覆盖其他用户的更改。 如果这种风险是可以接受的，则并发编程的成本可能会超过收益。

### <a name="pessimistic-concurrency"></a>悲观并发

预防并发冲突的一种方法是使用数据库锁定。 这称为悲观并发。 应用在读取要更新的数据库行之前，将请求锁定。 锁定某一行的更新访问权限之后，其他用户在第一个锁定释放之前无法锁定该行。

管理锁定有缺点。 它的编程可能很复杂，并且随着用户增加可能会导致性能问题。 Entity Framework Core 不提供对悲观并发的内置支持。

### <a name="optimistic-concurrency"></a>开放式并发

乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。 例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。

![将预算更改为零](concurrency/_static/change-budget30.png)

在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。

![将开始日期更改为 2013](concurrency/_static/change-date30.png)

Jane 单击“保存”后看到更改生效，因为浏览器会显示预算金额为零的“索引”页面。

John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。 接下来的情况取决于并发冲突的处理方式：

* 跟踪用户已修改的属性，并仅更新数据库中相应的列。

  在这种情况下，数据不会丢失。 两个用户更新了不同的属性。 下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。 这种更新方法可以减少导致数据丢失的冲突数。 这种方法具有一些缺点：

  * 无法避免数据丢失，如果对同一属性进行竞争性更改的话。
  * 通常不适用于 Web 应用。 它需要维持重要状态，以便跟踪所有提取值和新值。 维持大量状态可能影响应用性能。
  * 可能会增加应用复杂性（与实体上的并发检测相比）。

* 让 John 的更改覆盖 Jane 的更改。

  下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。 这种方法称为“客户端优先”或“最后一个优先”方案 。 客户端的所有值优先于数据存储的值。 基架代码不处理并发，“客户端优先”方案会自动执行。

* 阻止在数据库中更新 John 的更改。 应用通常会：

  * 显示错误消息。
  * 显示数据的当前状态。
  * 允许用户重新应用更改。

  这称为“存储优先”方案。 数据存储值优先于客户端提交的值。 本教程中使用了“存储优先”方案。 此方法可确保用户在未收到警报时不会覆盖任何更改。

<a name="concurrency"></a>

## <a name="conflict-detection-in-ef-core"></a>EF Core 中的冲突检测

使用配置为[并发标记](/ef/core/modeling/concurrency)的属性实现乐观并发控制。 当 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 触发更新或删除操作时，数据库中的并发标记值将与 EF Core 读取的原始值进行比较：

* 如果这些值匹配，则可以完成该操作。
* 如果这些值不匹配，EF Core 会假设另一个用户已执行冲突操作，然后会中止当前事务，并引发 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>。

另一个用户或进程执行与当前操作冲突的操作，这种情况称为“并发冲突”。

在关系数据库中，EF Core 会从 `UPDATE` 和 `DELETE` 语句的 `WHERE` 子句中查看并发标记的值，以检测并发冲突。

必须将数据模型配置为启用冲突检测，方法是包括一个可用于确定行更改时间的跟踪列。 EF 提供两种使用[并发标记](/ef/core/modeling/concurrency)的方法：

  * 将 [`[ConcurrencyCheck]`](xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute) 或 <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> 应用于模型上的属性。 不建议使用此方法。 有关详细信息，请参阅 [EF Core 中的并发标记](/ef/core/modeling/concurrency)。

  * 将 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 或 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsRowVersion%2A> 应用于模型中的并发标记。 本教程中使用了此方法。

SQL Server 方法和 SQLite 实现细节略有不同。 本教程的后续部分将提供一个差异文件，其中列出了这些差异。 Visual Studio 选项卡提供 SQL Server 方法。 Visual Studio Code 选项卡提供非 SQL Server 数据库（如 SQLite）的方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  * 在模型中包含一个用于确定某行更改时间的跟踪列。
  * 将 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 应用于并发属性。

  用以下突出显示的代码更新 Models/Department.cs 文件：

  [!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet3&highlight=27,28)]

<xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 用于将列标识为并发跟踪列。 Fluent API 是指定跟踪属性的另一种方法：

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("ConcurrencyToken")
  .IsRowVersion();
```

实体属性上的 [`[Timestamp]`](xref:System.ComponentModel.DataAnnotations.TimestampAttribute) 特性在 <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 方法中生成以下的代码：

```csharp
 b.Property<byte[]>("ConcurrencyToken")
     .IsConcurrencyToken()
     .ValueGeneratedOnAddOrUpdate()
     .HasColumnType("rowversion");
```

前面的代码：

* 将属性类型 `ConcurrencyToken` 设置为字节数组。 `byte[]` 是 SQL Server 所需的类型。
* 调用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsConcurrencyToken%2A>。  `IsConcurrencyToken` 将属性配置为并发标记。 执行更新时，数据库中的并发标记值将与原始值进行比较，以确保从数据库检索到该实例之后它未发生更改。 若它已发生更改，将引发 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>，并且不会应用更改。
* 调用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAddOrUpdate%2A>，它会将 `ConcurrencyToken` 属性配置为在添加或更新实体时自动生成一个值。
* `HasColumnType("rowversion")` 将 SQL Server 数据库中的列类型设置为 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。

以下代码显示更新 `Department` 名称时由 EF Core 生成的部分 T-SQL：

[!code-sql[](intro/samples/cu50snapshots/sql.txt?highlight=2-3)]

前面突出显示的代码显示包含 `ConcurrencyToken` 的 `WHERE` 子句。 如果数据库 `ConcurrencyToken` 不等于 `ConcurrencyToken` 参数 `@p2`，则不更新行。

以下突出显示的代码显示验证更新哪一行的 T-SQL：

[!code-sql[](intro/samples/cu50snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。 如果没有更新行，EF Core 会引发 `DbUpdateConcurrencyException`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  * 在模型中包含一个可用于确定某行更改时间的跟踪列。 此示例中使用了 `GUID`。 用以下突出显示的代码更新 Models/Department.cs 文件：

    [!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippetSL&highlight=27)]

  * 通过在 `Department.ConcurrencyToken` 属性上调用 <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken>，更新 Data/SchoolContext.cs 文件：

    [!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SQLite&highlight=21-23)]

    `IsConcurrencyToken` 将属性配置为并发标记。 执行更新时，数据库中的并发标记值将与原始值进行比较，以确保从数据库检索到该实例之后它未发生更改。 若它已发生更改，将引发 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>，并且不会应用更改。

  * 在用于更新实体的代码中，更新并发标记的值。 在此示例中，更新实体的方法使用以下代码。 本教程稍后将执行此步骤。

    ```csharp
    departmentToUpdate.ConcurrencyToken = Guid.NewGuid();
    ```

  除了在更新方法中更新并发标记，也可以设置一个触发器以便数据库能够自动更新它。 有关详细信息，请参阅 [SQLite 和 EF Core 并发标记](https://www.bricelam.net/2020/08/07/sqlite-and-efcore-concurrency-tokens.html)。
  <!--* Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> to set the `rowVersion` value to the value from the entity when it was read. The `OriginalValue` update  code is shown in [the concurrency updates](#c-up) section of this document. This step will be done later in the tutorial. -->

---

## <a name="add-a-migration"></a>添加迁移

添加 `ConcurrencyToken` 属性可更改需要迁移的数据库模型。

生成项目。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 PMC 中运行以下命令：

  ```powershell
  Add-Migration RowVersion
  Update-Database
    
  ```

前面的命令：

* 创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。
* 更新 Migrations/SchoolContextModelSnapshot.cs 文件。 此次更新将以下代码添加到 `BuildModel` 方法：

```csharp
 b.Property<byte[]>("ConcurrencyToken")
     .IsConcurrencyToken()
     .ValueGeneratedOnAddOrUpdate()
     .HasColumnType("rowversion");
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在终端中运行以下命令：

  ```dotnetcli
  dotnet ef migrations add RowVersion
  dotnet ef database update
  ```

前面的命令：

* 创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。
* 更新 Migrations/SchoolContextModelSnapshot.cs 文件。 此次更新将以下突出显示的代码添加到 `BuildModel` 方法：

```csharp
b.Property<Guid>("ConcurrencyToken")
    .IsConcurrencyToken()
    .HasColumnType("TEXT");
```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>搭建“院系”页面的基架

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：
  * 创建“Pages/Departments”文件夹。  
  * 将 `Department` 用于模型类。
  * 使用现有的上下文类，而不是新建上下文类。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 创建“Pages/Departments”文件夹。

* 运行以下命令，搭建“院系”页的基架。

  在 Windows 上：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **在 Linux 或 macOS 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

### <a name="add-a-utility-class"></a>添加实用工具类

使用以下代码添加名为 `Utility` 的类：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet_sl)]

---

`Utility` 类提供 `GetLastChars` 方法，该方法用于显示并发标记的最后几个字符。 下面的代码提供了同时使用 SQLite 和 SQL Server 的代码：

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet_full)]

`#if SQLiteVersion` [预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/)将分隔 SQLite 和 SQL Server 版本中的差异，并帮助：

* 创作者维护一个同时用于这两个版本的代码库。
* SQLite 开发人员将应用部署到 Azure 并使用 SQL Azure。

生成项目。

## <a name="update-the-index-page"></a>更新“索引”页

基架工具为“索引”页创建了 `ConcurrencyToken` 列，但生产应用中不会显示该字段。 本教程中显示 `ConcurrencyToken` 的最后一个部分，以帮助展示并发处理的工作原理。 无法保证最后一个部分本身是唯一的。

更新 Pages\Departments\Index.cshtml 页：

* 用院系替换索引。
* 更改包含 `ConcurrencyToken` 的代码，以便仅显示最后几个字符。
* 将 `FirstMidName` 替换为 `FullName`。

以下代码显示更新后的页面：

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>更新编辑页模型

使用以下代码更新 Pages\Departments\Edit.cshtml.cs：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_All_ss)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

---

<a name="c-up"></a>

### <a name="the-concurrency-updates"></a>并发更新

当在 `OnGetAsync` 方法中提取时，<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> 是使用实体中的 `ConcurrencyToken` 值进行更新。 EF Core 使用包含原始 `ConcurrencyToken` 值的 `WHERE` 子句生成 `SQL UPDATE` 命令。 如果 `UPDATE` 命令未影响任何行，则引发 `DbUpdateConcurrencyException` 异常。 当所有行都不具有原始的 `ConcurrencyToken` 值，`UPDATE` 命令将不会影响任何行。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=19-99)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion_sl&highlight=21-99)]

---

在上述突出显示的代码中：

* `Department.ConcurrencyToken` 中的值是在 `Edit` 页的 `Get` 请求中提取实体时的值。 通过 Razor 页面中显示将要编辑的实体的隐藏字段将该值提供给 `OnPost` 方法。 模型绑定器将隐藏字段值复制到 `Department.ConcurrencyToken`。
* `OriginalValue` 是 EF Core 用于 `WHERE` 子句的值。 在突出显示的代码行执行前：
  * `OriginalValue` 具有在此方法中调用 `FirstOrDefaultAsync` 时数据库中所包含的值。
  * 此值可能与“编辑”页面显示的值不同。
* 突出显示的代码可确保 EF Core 使用原始 `ConcurrencyToken` 值，该值来自于 SQL `UPDATE` 语句的 `WHERE` 子句中所显示的 `Department` 实体。

下面的代码演示了 `Department` 模型。 `Department` 初始化：

  * 由 EF 查询在 `OnGetAsync` 方法中完成。
  * 使用[模型绑定](xref:mvc/models/model-binding)的 Razor 页面中隐藏字段使用的 `OnPostAsync` 方法：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_mb&highlight=10-11,17-20,50-99)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_mb_ss&highlight=10-11,17-20,55-99)]

---

前面的代码显示来自 `HTTP POST` 请求的 `Department` 实体的 `ConcurrencyToken` 值设置为来自 `HTTP GET` 请求的 `ConcurrencyToken` 值。

发生并发错误时，以下突出显示的代码会获取客户端值（发布到此方法的值）和数据库值。

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

以下突出显示的代码将 `ConcurrencyToken` 值设置为从数据库检索的新值。 用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

`ModelState` 具有先前的 `ConcurrencyToken` 值，因此需使用 [`ModelState.Remove`](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.Remove%2A) 语句。 在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。

### <a name="sql-server-vs-sqlite-code-differences"></a>SQL Server 与 SQLite 代码差异

下面显示了 SQL Server 和 SQLite 版本之间的差异：

```diff
+ using System;    // For GUID on SQLite

+ departmentToUpdate.ConcurrencyToken = Guid.NewGuid();

 _context.Entry(departmentToUpdate)
    .Property(d => d.ConcurrencyToken).OriginalValue = Department.ConcurrencyToken;

- Department.ConcurrencyToken = (byte[])dbValues.ConcurrencyToken;
+ Department.ConcurrencyToken = dbValues.ConcurrencyToken;
```

### <a name="update-the-edit-razor-page"></a>更新“编辑”Razor 页

使用以下代码更新 Pages/Departments/Edit.cshtml：

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

前面的代码：

* 将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。
* 添加隐藏的行版本。 必须添加 `ConcurrencyToken`，以便回发能够绑定值。
* 显示 `ConcurrencyToken` 的最后一个字节以进行调试。
* 将 `ViewData` 替换为强类型 `InstructorNameSL`。

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>使用编辑页测试并发冲突

在英语系打开编辑的两个浏览器实例：

* 运行应用，然后选择“院系”。
* 右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。
* 在第一个选项卡中，单击英语系的“编辑”超链接。

两个浏览器选项卡显示相同信息。

在第一个浏览器选项卡中更改名称，然后单击“保存”。

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-130.png)

浏览器显示更改值并更新 `ConcurrencyToken` 标记后的索引页。 请注意更新后的 `ConcurrencyToken` 标记，它在其他选项卡的第二回发中显示。

在第二个浏览器选项卡中更改不同字段。

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-230.png)

单击“保存” 。 可看见所有不匹配数据库值的字段的错误消息：

![“院系编辑”页错误消息](concurrency/_static/edit-error30.png)

此浏览器窗口将不会更改名称字段。 将当前值（语言）复制并粘贴到名称字段。 退出选项卡。客户端验证将删除错误消息。

再次单击“保存”。 保存在第二个浏览器选项卡中输入的值。 在索引页中可以看到保存的值。

## <a name="update-the-delete-page-model"></a>更新“删除”页面模型

使用以下代码更新 Pages/Departments/Delete.cshtml.cs：

[!code-csharp[](intro/samples/cu50/Pages/Departments/Delete.cshtml.cs)]

删除页检测提取实体并更改时的并发冲突。 提取实体后，`Department.ConcurrencyToken` 为行版本。 EF Core 创建 `SQL DELETE` 命令时，它包括具有的 `ConcurrencyToken` 的 WHERE 子句。 如果 `SQL DELETE` 命令导致零行受影响：

* `SQL DELETE` 命令中的 `ConcurrencyToken` 与数据库中的 `ConcurrencyToken` 不匹配。
* 这将引发 `DbUpdateConcurrencyException` 异常。
* 使用 `concurrencyError` 调用 `OnGetAsync`。

### <a name="update-the-delete-razor-page"></a>更新“删除 Razor”页面

使用以下代码更新 Pages/Departments/Delete.cshtml：

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

上面的代码执行以下更改：

* 将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。
* 添加错误消息。
* 将“管理员”字段中的 FirstMidName 替换为 FullName。
* 更改 `ConcurrencyToken` 以显示最后一个字节。
* 添加隐藏的行版本。 必须添加 `ConcurrencyToken`，以便回发能够绑定值。

### <a name="test-concurrency-conflicts"></a>测试并发冲突

创建测试系。

在测试系打开删除的两个浏览器实例：

* 运行应用，然后选择“院系”。
* 右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。
* 单击测试系的“编辑”超链接。

两个浏览器选项卡显示相同信息。

在第一个浏览器选项卡中更改预算，然后单击“保存”。

浏览器显示更改值并更新 `ConcurrencyToken` 标记后的索引页。 请注意更新后的 `ConcurrencyToken` 标记，它在其他选项卡的第二回发中显示。

从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。 单击“删除”删除实体，除非 `ConcurrencyToken` 已更新。

## <a name="additional-resources"></a>其他资源

* [EF Core 中的并发令牌](/ef/core/modeling/concurrency)
* [EF Core 中的并发处理](/ef/core/saving/concurrency)
* [调试 ASP.NET Core 2.x 源](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>后续步骤

这是本系列的最后一个教程。 [本系列教程的 MVC 版本](xref:data/ef-mvc/index)中介绍了其他主题。

> [!div class="step-by-step"]
> [上一个教程](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

本教程介绍如何处理多个用户并发更新同一实体（同时）时出现的冲突。

## <a name="concurrency-conflicts"></a>并发冲突

在以下情况下，会发生并发冲突：

* 用户导航到实体的编辑页面。
* 第一个用户的更改还未写入数据库之前，另一用户更新同一实体。

如果未启用并发检测，则最后更新数据库的人员将覆盖其他用户的更改。 如果这种风险是可以接受的，则并发编程的成本可能会超过收益。

### <a name="pessimistic-concurrency-locking"></a>悲观并发（锁定）

预防并发冲突的一种方法是使用数据库锁定。 这称为悲观并发。 应用在读取要更新的数据库行之前，将请求锁定。 锁定某一行的更新访问权限之后，其他用户在第一个锁定释放之前无法锁定该行。

管理锁定有缺点。 它的编程可能很复杂，并且随着用户增加可能会导致性能问题。 Entity Framework Core 未提供对它的内置支持，并且本教程不展示其实现方式。

### <a name="optimistic-concurrency"></a>开放式并发

乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。 例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。

![将预算更改为零](concurrency/_static/change-budget30.png)

在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。

![将开始日期更改为 2013](concurrency/_static/change-date30.png)

Jane 单击“保存”后看到更改生效，因为浏览器会显示预算金额为零的“索引”页面。

John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。 接下来的情况取决于并发冲突的处理方式：

* 可以跟踪用户已修改的属性，并仅更新数据库中相应的列。

  在这种情况下，数据不会丢失。 两个用户更新了不同的属性。 下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。 这种更新方法可以减少导致数据丢失的冲突数。 这种方法具有一些缺点：
 
  * 无法避免数据丢失，如果对同一属性进行竞争性更改的话。
  * 通常不适用于 Web 应用。 它需要维持重要状态，以便跟踪所有提取值和新值。 维持大量状态可能影响应用性能。
  * 可能会增加应用复杂性（与实体上的并发检测相比）。

* 可让 John 的更改覆盖 Jane 的更改。

  下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。 这种方法称为“客户端优先”或“最后一个优先”方案 。 （客户端的所有值优先于数据存储的值。）如果不对并发处理进行任何编码，则自动执行“客户端优先”。

* 可以阻止在数据库中更新 John 的更改。 应用通常会：

  * 显示错误消息。
  * 显示数据的当前状态。
  * 允许用户重新应用更改。

  这称为“存储优先”方案。 （数据存储值优先于客户端提交的值。）本教程实施“存储优先”方案。 此方法可确保用户在未收到警报时不会覆盖任何更改。

## <a name="conflict-detection-in-ef-core"></a>EF Core 中的冲突检测

EF Core 在检测到冲突时会引发 `DbConcurrencyException` 异常。 数据模型必须配置为启用冲突检测。 启用冲突检测的选项包括以下项：

* 配置 EF Core，在 Update 或 Delete 命令的 Where 子句中包含配置为[并发令牌](/ef/core/modeling/concurrency)的列的原始值。

  调用 `SaveChanges` 时，Where 子句查找使用 <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 特性注释的所有属性的原始值。 如果在第一次读取行之后有任意并发令牌属性发生了更改，更新语句将无法查找到要更新的行。 EF Core 将其解释为并发冲突。 对于包含许多列的数据库表，此方法可能导致非常多的 Where 子句，并且可能需要大量的状态。 因此通常不建议使用此方法，并且它也不是本教程中使用的方法。

* 数据库表中包含一个可用于确定某行更改时间的跟踪列。

  在 SQL Server 数据库中，跟踪列的数据类型是 `rowversion`。 `rowversion` 值是一个序列号，该编号随着每次行的更新递增。 在 Update 或 Delete 命令中，Where 子句包含跟踪列的原始值（原始行版本号）。 如果其他用户已更改要更新的行，则 `rowversion` 列中的值与原始值不同。 在这种情况下，Update 或 Delete 语句会由于 Where 子句而无法找到要更新的行。 如果 Update 或 Delete 命令未影响任何行，EF Core 会引发并发异常。

## <a name="add-a-tracking-property"></a>添加跟踪属性

在 Models/Department.cs 中，添加名为 RowVersion 的跟踪属性：

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 特性用于将列标识为并发跟踪列。 Fluent API 是指定跟踪属性的另一种方法：

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

对于 SQL Server 数据库，定义为字节数组的实体属性上的 `[Timestamp]` 特性：

* 使列包含在 DELETE 和 UPDATE WHERE 子句中。
* 将数据库中的列类型设置为 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。

数据库生成有序的行版本号，该版本号随着每次行的更新递增。 在 `Update` 或 `Delete` 命令中，`Where` 子句包括提取的行版本值。 如果要更新的行在提取之后已更改：

* 当前的行版本值与提取值不相匹配。
* `Update` 或 `Delete` 命令不查找行，因为 `Where` 子句会查找提取行的版本值。
* 引发一个 `DbUpdateConcurrencyException`。

以下代码显示更新 Department 名称时由 EF Core 生成的部分 T-SQL：

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

前面突出显示的代码显示包含 `RowVersion` 的 `WHERE` 子句。 如果数据库 `RowVersion` 不等于 `RowVersion` 参数 (`@p2`)，则不更新行。

以下突出显示的代码显示验证更新哪一行的 T-SQL：

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。 如果没有更新行，EF Core 会引发 `DbUpdateConcurrencyException`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

对于 SQLite 数据库，定义为字节数组的实体属性上的 `[Timestamp]` 特性：

* 使列包含在 DELETE 和 UPDATE WHERE 子句中。
* 映射到 BLOB 列类型。

每当行更新时，数据库触发器都会将 RowVersion 列更新为新的随机字节数组。 在 `Update` 或 `Delete` 命令中，`Where` 子句包括 RowVersion 列的提取值。 如果要更新的行在提取之后已更改：

* 当前的行版本值与提取值不相匹配。
* `Update` 或 `Delete` 命令不查找行，因为 `Where` 子句会查找原始行版本值。
* 引发一个 `DbUpdateConcurrencyException`。

---

### <a name="update-the-database"></a>更新数据库

添加 `RowVersion` 属性可更改需要迁移的数据库模型。

生成项目。 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 PMC 中运行以下命令：

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 在终端中运行以下命令：

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

此命令：

* 创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。
* 更新 Migrations/SchoolContextModelSnapshot.cs 文件。 此次更新将以下突出显示的代码添加到 `BuildModel` 方法：

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 PMC 中运行以下命令：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开 `Migrations/<timestamp>_RowVersion.cs` 文件，并添加以下突出显示的代码：

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  前面的代码：

  * 将现有行更新为随机 blob 值。
  * 添加数据库触发器，该触发器在行更新时将 RowVersion 列设置为随机 blob 值。

* 在终端中运行以下命令：

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>搭建“院系”页面的基架

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：

* 创建“Pages/Departments”文件夹。  
* 将 `Department` 用于模型类。
  * 使用现有的上下文类，而不是新建上下文类。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 创建“Pages/Departments”文件夹。

* 运行以下命令，搭建“院系”页的基架。

  在 Windows 上：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **在 Linux 或 macOS 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

生成项目。

## <a name="update-the-index-page"></a>更新“索引”页

基架工具为“索引”页创建了 `RowVersion` 列，但生产应用中不会显示该字段。 本教程中显示 `RowVersion` 的最后一个字节，以帮助展示并发处理的工作原理。 无法保证最后一个字节本身是唯一的。

更新 Pages\Departments\Index.cshtml 页：

* 用院系替换索引。
* 更改包含 `RowVersion` 的代码，以便只显示字节数组的最后一个字节。
* 将 FirstMidName 替换为 FullName。

以下代码显示更新后的页面：

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>更新编辑页模型

使用以下代码更新 Pages\Departments\Edit.cshtml.cs：

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

当在 `OnGetAsync` 方法中提取时，<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> 是使用实体中的 `rowVersion` 值进行更新。 EF Core 使用包含原始 `RowVersion` 值的 WHERE 子句生成 SQL UPDATE 命令。 如果没有行受到 UPDATE 命令影响（没有行具有原始 `RowVersion` 值），将引发 `DbUpdateConcurrencyException` 异常。

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

在上述突出显示的代码中：

* `Department.RowVersion` 中的值是最初在“编辑”页的 Get 请求中所提取的实体中的值。 通过 Razor 页面中显示将要编辑的实体的隐藏字段将该值提供给 `OnPost` 方法。 模型绑定器将隐藏字段值复制到 `Department.RowVersion`。
* `OriginalValue` 是 EF Core 将用于 Where 子句的值。 在执行突出显示的代码行之前，`OriginalValue` 具有在此方法中调用 `FirstOrDefaultAsync` 时数据库中的值，该值可能与“编辑”页面上所显示的值不同。
* 突出显示的代码可确保 EF Core 使用原始 `RowVersion` 值，该值来自于 SQL UPDATE 语句的 Where 子句中所显示的 `Department` 实体。

发生并发错误时，以下突出显示的代码会获取客户端值（发布到此方法的值）和数据库值。

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

以下突出显示的代码将 `RowVersion` 值设置为从数据库检索的新值。 用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

`ModelState` 具有旧的 `RowVersion` 值，因此需使用 `ModelState.Remove` 语句。 在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。

### <a name="update-the-edit-page"></a>更新“编辑”页

使用以下代码更新 Pages/Departments/Edit.cshtml：

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

前面的代码：

* 将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。
* 添加隐藏的行版本。 必须添加 `RowVersion`，以便回发能够绑定值。
* 显示 `RowVersion` 的最后一个字节以进行调试。
* 将 `ViewData` 替换为强类型 `InstructorNameSL`。

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>使用编辑页测试并发冲突

在英语系打开编辑的两个浏览器实例：

* 运行应用，然后选择“院系”。
* 右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。
* 在第一个选项卡中，单击英语系的“编辑”超链接。

两个浏览器选项卡显示相同信息。

在第一个浏览器选项卡中更改名称，然后单击“保存”。

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-130.png)

浏览器显示更改值并更新 rowVersion 标记后的索引页。 请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。

在第二个浏览器选项卡中更改不同字段。

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-230.png)

单击“保存” 。 可看见所有不匹配数据库值的字段的错误消息：

![“院系编辑”页错误消息](concurrency/_static/edit-error30.png)

此浏览器窗口将不会更改名称字段。 将当前值（语言）复制并粘贴到名称字段。 退出选项卡。客户端验证将删除错误消息。

再次单击“保存”。 保存在第二个浏览器选项卡中输入的值。 在索引页中可以看到保存的值。

## <a name="update-the-delete-page-model"></a>更新“删除”页面模型

使用以下代码更新 Pages/Departments/Delete.cshtml.cs：

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

删除页检测提取实体并更改时的并发冲突。 提取实体后，`Department.RowVersion` 为行版本。 EF Core 创建 SQL DELETE 命令时，它包括具有 `RowVersion` 的 WHERE 子句。 如果 SQL DELETE 命令导致零行受影响：

* SQL DELETE 命令中的 `RowVersion` 与数据库中的 `RowVersion` 不匹配。
* 引发 DbUpdateConcurrencyException 异常。
* 使用 `concurrencyError` 调用 `OnGetAsync`。

### <a name="update-the-delete-page"></a>更新“删除”页

使用以下代码更新 Pages/Departments/Delete.cshtml：

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

上面的代码执行以下更改：

* 将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。
* 添加错误消息。
* 将“管理员”字段中的 FirstMidName 替换为 FullName。
* 更改 `RowVersion` 以显示最后一个字节。
* 添加隐藏的行版本。 必须添加 `RowVersion`，以便回发能够绑定值。

### <a name="test-concurrency-conflicts"></a>测试并发冲突

创建测试系。

在测试系打开删除的两个浏览器实例：

* 运行应用，然后选择“院系”。
* 右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。
* 单击测试系的“编辑”超链接。

两个浏览器选项卡显示相同信息。

在第一个浏览器选项卡中更改预算，然后单击“保存”。

浏览器显示更改值并更新 rowVersion 标记后的索引页。 请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。

从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。 单击“删除”删除实体，除非 `RowVersion` 已更新。

## <a name="additional-resources"></a>其他资源

* [EF Core 中的并发令牌](/ef/core/modeling/concurrency)
* [EF Core 中的并发处理](/ef/core/saving/concurrency)
* [调试 ASP.NET Core 2.x 源](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>后续步骤

这是本系列的最后一个教程。 [本系列教程的 MVC 版本](xref:data/ef-mvc/index)中介绍了其他主题。

> [!div class="step-by-step"]
> [上一个教程](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本教程介绍如何处理多个用户并发更新同一实体（同时）时出现的冲突。 如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。 [下载说明](xref:index#how-to-download-a-sample)。

## <a name="concurrency-conflicts"></a>并发冲突

在以下情况下，会发生并发冲突：

* 用户导航到实体的编辑页面。
* 第一个用户的更改还未写入数据库之前，另一用户更新同一实体。

如果未启用并发检测，当发生并发更新时：

* 最后一个更新优先。 也就是最后一个更新的值保存至数据库。
* 第一个并发更新将会丢失。

### <a name="optimistic-concurrency"></a>开放式并发

乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。 例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。

![将预算更改为零](concurrency/_static/change-budget.png)

在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。

![将开始日期更改为 2013](concurrency/_static/change-date.png)

Jane 先单击“保存”，并在浏览器显示索引页时看到她的更改。

![预算已更改为零](concurrency/_static/budget-zero.png)

John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。 接下来的情况取决于并发冲突的处理方式。

乐观并发包括以下选项：

* 可以跟踪用户已修改的属性，并仅更新数据库中相应的列。

  在这种情况下，数据不会丢失。 两个用户更新了不同的属性。 下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。 这种更新方法可以减少导致数据丢失的冲突数。 这种方法：
 
  * 无法避免数据丢失，如果对同一属性进行竞争性更改的话。
  * 通常不适用于 Web 应用。 它需要维持重要状态，以便跟踪所有提取值和新值。 维持大量状态可能影响应用性能。
  * 可能会增加应用复杂性（与实体上的并发检测相比）。

* 可让 John 的更改覆盖 Jane 的更改。

  下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。 这种方法称为“客户端优先”或“最后一个优先”方案 。 （客户端的所有值优先于数据存储的值。）如果不对并发处理进行任何编码，则自动执行“客户端优先”。

* 可以阻止在数据库中更新 John 的更改。 应用通常会：

  * 显示错误消息。
  * 显示数据的当前状态。
  * 允许用户重新应用更改。

  这称为“存储优先”方案。 （数据存储值优先于客户端提交的值。）本教程实施“存储优先”方案。 此方法可确保用户在未收到警报时不会覆盖任何更改。

## <a name="handling-concurrency"></a>处理并发

当属性配置为[并发令牌](/ef/core/modeling/concurrency)时：

* EF Core 验证提取属性后是否未更改属性。 调用 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 或 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 时会执行此检查。
* 如果提取属性后更改了属性，将引发 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)。 

数据库和数据模型必须配置为支持引发 `DbUpdateConcurrencyException`。

### <a name="detecting-concurrency-conflicts-on-a-property"></a>检测属性的并发冲突

可使用 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 特性在属性级别检测并发冲突。 该特性可应用于模型上的多个属性。 有关详细信息，请参阅[数据注释 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)。

本教程中不使用 `[ConcurrencyCheck]` 特性。

### <a name="detecting-concurrency-conflicts-on-a-row"></a>检测行的并发冲突

要检测并发冲突，请将 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 跟踪列添加到模型。  `rowversion` :

* 是 SQL Server 特定的。 其他数据库可能无法提供类似功能。
* 用于确定从数据库提取实体后未更改实体。 

数据库生成 `rowversion` 序号，该数字随着每次行的更新递增。 在 `Update` 或 `Delete` 命令中，`Where` 子句包括 `rowversion` 的提取值。 如果要更新的行已更改：

* `rowversion` 不匹配提取值。
* `Update` 或 `Delete` 命令不能找到行，因为 `Where` 子句包含提取的 `rowversion`。
* 引发一个 `DbUpdateConcurrencyException`。

在 EF Core 中，如果未通过 `Update` 或 `Delete` 命令更新行，则引发并发异常。

### <a name="add-a-tracking-property-to-the-department-entity"></a>向 Department 实体添加跟踪属性

在 Models/Department.cs 中，添加名为 RowVersion 的跟踪属性：

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 特性指定此列包含在 `Update` 和 `Delete` 命令的 `Where` 子句中。 该特性称为 `Timestamp`，因为之前版本的 SQL Server 在 SQL `rowversion` 类型将其替换之前使用 SQL `timestamp` 数据类型。

Fluent API 还可指定跟踪属性：

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

以下代码显示更新 Department 名称时由 EF Core 生成的部分 T-SQL：

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

前面突出显示的代码显示包含 `RowVersion` 的 `WHERE` 子句。 如果数据库 `RowVersion` 不等于 `RowVersion` 参数（`@p2`），则不更新行。

以下突出显示的代码显示验证更新哪一行的 T-SQL：

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。 在没有行更新的情况下，EF Core 引发 `DbUpdateConcurrencyException`。

在 Visual Studio 的输出窗口中可看见 EF Core 生成的 T-SQL。

### <a name="update-the-db"></a>更新数据库

添加 `RowVersion` 属性可更改数据库模型，这需要迁移。

生成项目。 在命令窗口中输入以下命令：

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

前面的命令：

* 添加 Migrations/{time stamp}_RowVersion.cs 迁移文件。
* 更新 Migrations/SchoolContextModelSnapshot.cs 文件。 此次更新将以下突出显示的代码添加到 `BuildModel` 方法：

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* 运行迁移以更新数据库。

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>构架院系模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明操作，并对模型类使用 `Department`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 运行下面的命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

上述命令为 `Department` 模型创建基架。 在 Visual Studio 中打开项目。

生成项目。

### <a name="update-the-departments-index-page"></a>更新院系索引页

基架引擎为索引页创建 `RowVersion` 列，但不应显示该字段。 本教程中显示 `RowVersion` 的最后一个字节，以帮助理解并发。 不能保证最后一个字节是唯一的。 实际应用不会显示 `RowVersion` 或 `RowVersion` 的最后一个字节。

更新索引页：

* 用院系替换索引。
* 将包含 `RowVersion` 的标记替换为 `RowVersion` 的最后一个字节。
* 将 FirstMidName 替换为 FullName。

以下标记显示更新后的页面：

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>更新编辑页模型

使用以下代码更新 Pages\Departments\Edit.cshtml.cs：

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

要检测并发问题，请使用来自所提取实体的 `rowVersion` 值更新 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)。 EF Core 使用包含原始 `RowVersion` 值的 WHERE 子句生成 SQL UPDATE 命令。 如果没有行受到 UPDATE 命令影响（没有行具有原始 `RowVersion` 值），将引发 `DbUpdateConcurrencyException` 异常。

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

在前面的代码中，`Department.RowVersion` 为实体提取后的值。 使用此方法调用 `FirstOrDefaultAsync` 时，`OriginalValue` 为数据库中的值。

以下代码获取客户端值（向此方法发布的值）和数据库值：

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

以下突出显示的代码将 `RowVersion` 值设置为从数据库检索的新值。 用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

`ModelState` 具有旧的 `RowVersion` 值，因此需使用 `ModelState.Remove` 语句。 在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。

## <a name="update-the-edit-page"></a>更新“编辑”页

使用以下标记更新 Pages/Departments/Edit.cshtml：

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

前面的标记：

* 将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。
* 添加隐藏的行版本。 必须添加 `RowVersion`，以便回发绑定值。
* 显示 `RowVersion` 的最后一个字节以进行调试。
* 将 `ViewData` 替换为强类型 `InstructorNameSL`。

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>使用编辑页测试并发冲突

在英语系打开编辑的两个浏览器实例：

* 运行应用，然后选择“院系”。
* 右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。
* 在第一个选项卡中，单击英语系的“编辑”超链接。

两个浏览器选项卡显示相同信息。

在第一个浏览器选项卡中更改名称，然后单击“保存”。

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-1.png)

浏览器显示更改值并更新 rowVersion 标记后的索引页。 请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。

在第二个浏览器选项卡中更改不同字段。

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-2.png)

单击“保存” 。 可看见所有不匹配数据库值的字段的错误消息：

![“院系编辑”页错误消息 1](concurrency/_static/edit-error.png)

此浏览器窗口将不会更改名称字段。 将当前值（语言）复制并粘贴到名称字段。 退出选项卡。客户端验证将删除错误消息。

![“院系编辑”页错误消息 2](concurrency/_static/cv.png)

再次单击“保存”。 保存在第二个浏览器选项卡中输入的值。 在索引页中可以看到保存的值。

## <a name="update-the-delete-page"></a>更新“删除”页

使用以下代码更新“删除”页模型：

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

删除页检测提取实体并更改时的并发冲突。 提取实体后，`Department.RowVersion` 为行版本。 EF Core 创建 SQL DELETE 命令时，它包括具有 `RowVersion` 的 WHERE 子句。 如果 SQL DELETE 命令导致零行受影响：

* SQL DELETE 命令中的 `RowVersion` 与数据库中的 `RowVersion` 不匹配。
* 引发 DbUpdateConcurrencyException 异常。
* 使用 `concurrencyError` 调用 `OnGetAsync`。

### <a name="update-the-delete-page"></a>更新“删除”页

使用以下代码更新 Pages/Departments/Delete.cshtml：

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

上面的代码执行以下更改：

* 将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。
* 添加错误消息。
* 将“管理员”字段中的 FirstMidName 替换为 FullName。
* 更改 `RowVersion` 以显示最后一个字节。
* 添加隐藏的行版本。 必须添加 `RowVersion`，以便回发绑定值。

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>使用删除页测试并发冲突

创建测试系。

在测试系打开删除的两个浏览器实例：

* 运行应用，然后选择“院系”。
* 右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。
* 单击测试系的“编辑”超链接。

两个浏览器选项卡显示相同信息。

在第一个浏览器选项卡中更改预算，然后单击“保存”。

浏览器显示更改值并更新 rowVersion 标记后的索引页。 请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。

从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。 单击“删除”删除实体，除非 `RowVersion` 已更新。

请参阅[继承](xref:data/ef-mvc/inheritance)了解如何继承数据模型。

### <a name="additional-resources"></a>其他资源

* [EF Core 中的并发令牌](/ef/core/modeling/concurrency)
* [EF Core 中的并发处理](/ef/core/saving/concurrency)
* [本教程的 YouTube 版本（处理并发冲突）](https://youtu.be/EosxHTFgYps)
* [本教程的 YouTube 版本（第 2 部分）](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [本教程的 YouTube 版本（第 3 部分）](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [上一篇](xref:data/ef-rp/update-related-data)

::: moniker-end
