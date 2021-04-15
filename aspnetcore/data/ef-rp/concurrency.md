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
# <a name="part-8-razor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="67288-103">第 8 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 并发</span><span class="sxs-lookup"><span data-stu-id="67288-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="67288-104">[Tom Dykstra](https://github.com/tdykstra) 和 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="67288-104">[Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="67288-105">本教程介绍如何处理多个用户并发更新同一实体时出现的冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently.</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="67288-106">并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-106">Concurrency conflicts</span></span>

<span data-ttu-id="67288-107">在以下情况下，会发生并发冲突：</span><span class="sxs-lookup"><span data-stu-id="67288-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="67288-108">用户导航到实体的编辑页面。</span><span class="sxs-lookup"><span data-stu-id="67288-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="67288-109">第一个用户的更改还未写入数据库之前，另一用户更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="67288-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="67288-110">如果未启用并发检测，则最后更新数据库的人员将覆盖其他用户的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="67288-111">如果这种风险是可以接受的，则并发编程的成本可能会超过收益。</span><span class="sxs-lookup"><span data-stu-id="67288-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency"></a><span data-ttu-id="67288-112">悲观并发</span><span class="sxs-lookup"><span data-stu-id="67288-112">Pessimistic concurrency</span></span>

<span data-ttu-id="67288-113">预防并发冲突的一种方法是使用数据库锁定。</span><span class="sxs-lookup"><span data-stu-id="67288-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="67288-114">这称为悲观并发。</span><span class="sxs-lookup"><span data-stu-id="67288-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="67288-115">应用在读取要更新的数据库行之前，将请求锁定。</span><span class="sxs-lookup"><span data-stu-id="67288-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="67288-116">锁定某一行的更新访问权限之后，其他用户在第一个锁定释放之前无法锁定该行。</span><span class="sxs-lookup"><span data-stu-id="67288-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="67288-117">管理锁定有缺点。</span><span class="sxs-lookup"><span data-stu-id="67288-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="67288-118">它的编程可能很复杂，并且随着用户增加可能会导致性能问题。</span><span class="sxs-lookup"><span data-stu-id="67288-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="67288-119">Entity Framework Core 不提供对悲观并发的内置支持。</span><span class="sxs-lookup"><span data-stu-id="67288-119">Entity Framework Core provides no built-in support for pessimistic concurrency.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="67288-120">开放式并发</span><span class="sxs-lookup"><span data-stu-id="67288-120">Optimistic concurrency</span></span>

<span data-ttu-id="67288-121">乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。</span><span class="sxs-lookup"><span data-stu-id="67288-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="67288-122">例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![将预算更改为零](concurrency/_static/change-budget30.png)

<span data-ttu-id="67288-124">在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。</span><span class="sxs-lookup"><span data-stu-id="67288-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![将开始日期更改为 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="67288-126">Jane 单击“保存”后看到更改生效，因为浏览器会显示预算金额为零的“索引”页面。</span><span class="sxs-lookup"><span data-stu-id="67288-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="67288-127">John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="67288-128">接下来的情况取决于并发冲突的处理方式：</span><span class="sxs-lookup"><span data-stu-id="67288-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="67288-129">跟踪用户已修改的属性，并仅更新数据库中相应的列。</span><span class="sxs-lookup"><span data-stu-id="67288-129">Keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="67288-130">在这种情况下，数据不会丢失。</span><span class="sxs-lookup"><span data-stu-id="67288-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="67288-131">两个用户更新了不同的属性。</span><span class="sxs-lookup"><span data-stu-id="67288-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="67288-132">下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="67288-133">这种更新方法可以减少导致数据丢失的冲突数。</span><span class="sxs-lookup"><span data-stu-id="67288-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="67288-134">这种方法具有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="67288-134">This approach has some disadvantages:</span></span>

  * <span data-ttu-id="67288-135">无法避免数据丢失，如果对同一属性进行竞争性更改的话。</span><span class="sxs-lookup"><span data-stu-id="67288-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="67288-136">通常不适用于 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="67288-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="67288-137">它需要维持重要状态，以便跟踪所有提取值和新值。</span><span class="sxs-lookup"><span data-stu-id="67288-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="67288-138">维持大量状态可能影响应用性能。</span><span class="sxs-lookup"><span data-stu-id="67288-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="67288-139">可能会增加应用复杂性（与实体上的并发检测相比）。</span><span class="sxs-lookup"><span data-stu-id="67288-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="67288-140">让 John 的更改覆盖 Jane 的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-140">Let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="67288-141">下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="67288-142">这种方法称为“客户端优先”或“最后一个优先”方案 。</span><span class="sxs-lookup"><span data-stu-id="67288-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="67288-143">客户端的所有值优先于数据存储的值。</span><span class="sxs-lookup"><span data-stu-id="67288-143">All values from the client take precedence over what's in the data store.</span></span> <span data-ttu-id="67288-144">基架代码不处理并发，“客户端优先”方案会自动执行。</span><span class="sxs-lookup"><span data-stu-id="67288-144">The scaffolded code does no concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="67288-145">阻止在数据库中更新 John 的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-145">Prevent John's change from being updated in the database.</span></span> <span data-ttu-id="67288-146">应用通常会：</span><span class="sxs-lookup"><span data-stu-id="67288-146">Typically, the app would:</span></span>

  * <span data-ttu-id="67288-147">显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-147">Display an error message.</span></span>
  * <span data-ttu-id="67288-148">显示数据的当前状态。</span><span class="sxs-lookup"><span data-stu-id="67288-148">Show the current state of the data.</span></span>
  * <span data-ttu-id="67288-149">允许用户重新应用更改。</span><span class="sxs-lookup"><span data-stu-id="67288-149">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="67288-150">这称为“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="67288-150">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="67288-151">数据存储值优先于客户端提交的值。</span><span class="sxs-lookup"><span data-stu-id="67288-151">The data-store values take precedence over the values submitted by the client.</span></span> <span data-ttu-id="67288-152">本教程中使用了“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="67288-152">The Store Wins scenario is used in this tutorial.</span></span> <span data-ttu-id="67288-153">此方法可确保用户在未收到警报时不会覆盖任何更改。</span><span class="sxs-lookup"><span data-stu-id="67288-153">This method ensures that no changes are overwritten without a user being alerted.</span></span>

<a name="concurrency"></a>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="67288-154">EF Core 中的冲突检测</span><span class="sxs-lookup"><span data-stu-id="67288-154">Conflict detection in EF Core</span></span>

<span data-ttu-id="67288-155">使用配置为[并发标记](/ef/core/modeling/concurrency)的属性实现乐观并发控制。</span><span class="sxs-lookup"><span data-stu-id="67288-155">Properties configured as [concurrency tokens](/ef/core/modeling/concurrency) are used to implement optimistic concurrency control.</span></span> <span data-ttu-id="67288-156">当 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 或 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 触发更新或删除操作时，数据库中的并发标记值将与 EF Core 读取的原始值进行比较：</span><span class="sxs-lookup"><span data-stu-id="67288-156">When an update or delete operation is triggered by  <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>, the value of the concurrency token in the database is compared against the original value read by EF Core:</span></span>

* <span data-ttu-id="67288-157">如果这些值匹配，则可以完成该操作。</span><span class="sxs-lookup"><span data-stu-id="67288-157">If the values match, the operation can complete.</span></span>
* <span data-ttu-id="67288-158">如果这些值不匹配，EF Core 会假设另一个用户已执行冲突操作，然后会中止当前事务，并引发 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>。</span><span class="sxs-lookup"><span data-stu-id="67288-158">If the values do not match, EF Core assumes that another user has performed a conflicting operation, aborts the current transaction, and throws a  <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>.</span></span>

<span data-ttu-id="67288-159">另一个用户或进程执行与当前操作冲突的操作，这种情况称为“并发冲突”。</span><span class="sxs-lookup"><span data-stu-id="67288-159">Another user or process performing an operation that conflicts with the current operation is known as *concurrency conflict*.</span></span>

<span data-ttu-id="67288-160">在关系数据库中，EF Core 会从 `UPDATE` 和 `DELETE` 语句的 `WHERE` 子句中查看并发标记的值，以检测并发冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-160">On relational databases EF Core checks for the value of the concurrency token in the `WHERE` clause of `UPDATE` and `DELETE` statements to detect a concurrency conflict.</span></span>

<span data-ttu-id="67288-161">必须将数据模型配置为启用冲突检测，方法是包括一个可用于确定行更改时间的跟踪列。</span><span class="sxs-lookup"><span data-stu-id="67288-161">The data model must be configured to enable conflict detection by include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="67288-162">EF 提供两种使用[并发标记](/ef/core/modeling/concurrency)的方法：</span><span class="sxs-lookup"><span data-stu-id="67288-162">EF provides two approaches for [concurrency tokens](/ef/core/modeling/concurrency):</span></span>

  * <span data-ttu-id="67288-163">将 [`[ConcurrencyCheck]`](xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute) 或 <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> 应用于模型上的属性。</span><span class="sxs-lookup"><span data-stu-id="67288-163">Applying [`[ConcurrencyCheck]`](xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute) or <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> to a property on the model.</span></span> <span data-ttu-id="67288-164">不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="67288-164">This approach is not recommended.</span></span> <span data-ttu-id="67288-165">有关详细信息，请参阅 [EF Core 中的并发标记](/ef/core/modeling/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="67288-165">For more information, see [Concurrency Tokens in EF Core](/ef/core/modeling/concurrency).</span></span>

  * <span data-ttu-id="67288-166">将 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 或 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsRowVersion%2A> 应用于模型中的并发标记。</span><span class="sxs-lookup"><span data-stu-id="67288-166">Applying <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> or <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsRowVersion%2A> to a concurrency token in the model.</span></span> <span data-ttu-id="67288-167">本教程中使用了此方法。</span><span class="sxs-lookup"><span data-stu-id="67288-167">This is the approach used in this tutorial.</span></span>

<span data-ttu-id="67288-168">SQL Server 方法和 SQLite 实现细节略有不同。</span><span class="sxs-lookup"><span data-stu-id="67288-168">The  SQL Server approach and SQLite implementation details are slightly different.</span></span> <span data-ttu-id="67288-169">本教程的后续部分将提供一个差异文件，其中列出了这些差异。</span><span class="sxs-lookup"><span data-stu-id="67288-169">A difference file is shown later in the tutorial listing the differences.</span></span> <span data-ttu-id="67288-170">Visual Studio 选项卡提供 SQL Server 方法。</span><span class="sxs-lookup"><span data-stu-id="67288-170">The Visual Studio tab shows the SQL Server approach.</span></span> <span data-ttu-id="67288-171">Visual Studio Code 选项卡提供非 SQL Server 数据库（如 SQLite）的方法。</span><span class="sxs-lookup"><span data-stu-id="67288-171">The Visual Studio Code tab shows the approach for non-SQL Server databases, such as SQLite.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-172">Visual Studio</span></span>](#tab/visual-studio)

  * <span data-ttu-id="67288-173">在模型中包含一个用于确定某行更改时间的跟踪列。</span><span class="sxs-lookup"><span data-stu-id="67288-173">In the model, include a tracking column that is used to determine when a row has been changed.</span></span>
  * <span data-ttu-id="67288-174">将 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 应用于并发属性。</span><span class="sxs-lookup"><span data-stu-id="67288-174">Apply the <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> to the concurrency property.</span></span>

  <span data-ttu-id="67288-175">用以下突出显示的代码更新 Models/Department.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="67288-175">Update the *Models/Department.cs* file with the following highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet3&highlight=27,28)]

<span data-ttu-id="67288-176"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 用于将列标识为并发跟踪列。</span><span class="sxs-lookup"><span data-stu-id="67288-176">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="67288-177">Fluent API 是指定跟踪属性的另一种方法：</span><span class="sxs-lookup"><span data-stu-id="67288-177">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("ConcurrencyToken")
  .IsRowVersion();
```

<span data-ttu-id="67288-178">实体属性上的 [`[Timestamp]`](xref:System.ComponentModel.DataAnnotations.TimestampAttribute) 特性在 <xref:Microsoft.EntityFrameworkCore.ModelBuilder> 方法中生成以下的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-178">The [`[Timestamp]`](xref:System.ComponentModel.DataAnnotations.TimestampAttribute) attribute on an entity property generates the following code in the <xref:Microsoft.EntityFrameworkCore.ModelBuilder> method:</span></span>

```csharp
 b.Property<byte[]>("ConcurrencyToken")
     .IsConcurrencyToken()
     .ValueGeneratedOnAddOrUpdate()
     .HasColumnType("rowversion");
```

<span data-ttu-id="67288-179">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-179">The preceding code:</span></span>

* <span data-ttu-id="67288-180">将属性类型 `ConcurrencyToken` 设置为字节数组。</span><span class="sxs-lookup"><span data-stu-id="67288-180">Sets the property type `ConcurrencyToken` to byte array.</span></span> <span data-ttu-id="67288-181">`byte[]` 是 SQL Server 所需的类型。</span><span class="sxs-lookup"><span data-stu-id="67288-181">`byte[]` is the required type for SQL Server.</span></span>
* <span data-ttu-id="67288-182">调用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsConcurrencyToken%2A>。</span><span class="sxs-lookup"><span data-stu-id="67288-182">Calls <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsConcurrencyToken%2A>.</span></span>  <span data-ttu-id="67288-183">`IsConcurrencyToken` 将属性配置为并发标记。</span><span class="sxs-lookup"><span data-stu-id="67288-183">`IsConcurrencyToken` configures the property as a concurrency token.</span></span> <span data-ttu-id="67288-184">执行更新时，数据库中的并发标记值将与原始值进行比较，以确保从数据库检索到该实例之后它未发生更改。</span><span class="sxs-lookup"><span data-stu-id="67288-184">On updates, the concurrency token value in the database is compared to the original value when to ensure it has not changed since the instance was retrieved from the database.</span></span> <span data-ttu-id="67288-185">若它已发生更改，将引发 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>，并且不会应用更改。</span><span class="sxs-lookup"><span data-stu-id="67288-185">If it has changed, a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> is thrown and changes are not applied.</span></span>
* <span data-ttu-id="67288-186">调用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAddOrUpdate%2A>，它会将 `ConcurrencyToken` 属性配置为在添加或更新实体时自动生成一个值。</span><span class="sxs-lookup"><span data-stu-id="67288-186">Calls <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAddOrUpdate%2A>, which configures the `ConcurrencyToken` property to have a value automatically generated when adding or updating an entity.</span></span>
* <span data-ttu-id="67288-187">`HasColumnType("rowversion")` 将 SQL Server 数据库中的列类型设置为 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="67288-187">`HasColumnType("rowversion")` sets the column type in the SQL Server database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="67288-188">以下代码显示更新 `Department` 名称时由 EF Core 生成的部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="67288-188">The following code shows a portion of the T-SQL generated by EF Core when the `Department` name is updated:</span></span>

[!code-sql[](intro/samples/cu50snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="67288-189">前面突出显示的代码显示包含 `ConcurrencyToken` 的 `WHERE` 子句。</span><span class="sxs-lookup"><span data-stu-id="67288-189">The preceding highlighted code shows the `WHERE` clause containing `ConcurrencyToken`.</span></span> <span data-ttu-id="67288-190">如果数据库 `ConcurrencyToken` 不等于 `ConcurrencyToken` 参数 `@p2`，则不更新行。</span><span class="sxs-lookup"><span data-stu-id="67288-190">If the database `ConcurrencyToken` doesn't equal the `ConcurrencyToken` parameter `@p2`, no rows are updated.</span></span>

<span data-ttu-id="67288-191">以下突出显示的代码显示验证更新哪一行的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="67288-191">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu50snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="67288-192">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。</span><span class="sxs-lookup"><span data-stu-id="67288-192">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="67288-193">如果没有更新行，EF Core 会引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-193">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

  * <span data-ttu-id="67288-195">在模型中包含一个可用于确定某行更改时间的跟踪列。</span><span class="sxs-lookup"><span data-stu-id="67288-195">In the model, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="67288-196">此示例中使用了 `GUID`。</span><span class="sxs-lookup"><span data-stu-id="67288-196">In this sample, a `GUID` is used.</span></span> <span data-ttu-id="67288-197">用以下突出显示的代码更新 Models/Department.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="67288-197">Update the *Models/Department.cs* file with the following highlighted code:</span></span>

    [!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippetSL&highlight=27)]

  * <span data-ttu-id="67288-198">通过在 `Department.ConcurrencyToken` 属性上调用 <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken>，更新 Data/SchoolContext.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="67288-198">Update the *Data/SchoolContext.cs* file by calling <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> on the `Department.ConcurrencyToken` property:</span></span>

    [!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SQLite&highlight=21-23)]

    <span data-ttu-id="67288-199">`IsConcurrencyToken` 将属性配置为并发标记。</span><span class="sxs-lookup"><span data-stu-id="67288-199">`IsConcurrencyToken` configures the property as a concurrency token.</span></span> <span data-ttu-id="67288-200">执行更新时，数据库中的并发标记值将与原始值进行比较，以确保从数据库检索到该实例之后它未发生更改。</span><span class="sxs-lookup"><span data-stu-id="67288-200">On updates, the concurrency token value in the database is compared to the original value when to ensure it has not changed since the instance was retrieved from the database.</span></span> <span data-ttu-id="67288-201">若它已发生更改，将引发 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>，并且不会应用更改。</span><span class="sxs-lookup"><span data-stu-id="67288-201">If it has changed, a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> is thrown and changes are not applied.</span></span>

  * <span data-ttu-id="67288-202">在用于更新实体的代码中，更新并发标记的值。</span><span class="sxs-lookup"><span data-stu-id="67288-202">In the code that updates the entity, update the value of the concurrency token.</span></span> <span data-ttu-id="67288-203">在此示例中，更新实体的方法使用以下代码。</span><span class="sxs-lookup"><span data-stu-id="67288-203">In this sample, the method that updates the entity uses the following code.</span></span> <span data-ttu-id="67288-204">本教程稍后将执行此步骤。</span><span class="sxs-lookup"><span data-stu-id="67288-204">This step will be done later in the tutorial.</span></span>

    ```csharp
    departmentToUpdate.ConcurrencyToken = Guid.NewGuid();
    ```

  <span data-ttu-id="67288-205">除了在更新方法中更新并发标记，也可以设置一个触发器以便数据库能够自动更新它。</span><span class="sxs-lookup"><span data-stu-id="67288-205">An alternative to updating the concurrency token in the update method is to set up a trigger so it's automatically updated by the database.</span></span> <span data-ttu-id="67288-206">有关详细信息，请参阅 [SQLite 和 EF Core 并发标记](https://www.bricelam.net/2020/08/07/sqlite-and-efcore-concurrency-tokens.html)。</span><span class="sxs-lookup"><span data-stu-id="67288-206">For more information, see [SQLite and EF Core Concurrency Tokens](https://www.bricelam.net/2020/08/07/sqlite-and-efcore-concurrency-tokens.html).</span></span>
  <!--* Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> to set the `rowVersion` value to the value from the entity when it was read. The `OriginalValue` update  code is shown in [the concurrency updates](#c-up) section of this document. This step will be done later in the tutorial. -->

---

## <a name="add-a-migration"></a><span data-ttu-id="67288-207">添加迁移</span><span class="sxs-lookup"><span data-stu-id="67288-207">Add a migration</span></span>

<span data-ttu-id="67288-208">添加 `ConcurrencyToken` 属性可更改需要迁移的数据库模型。</span><span class="sxs-lookup"><span data-stu-id="67288-208">Adding the `ConcurrencyToken` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="67288-209">生成项目。</span><span class="sxs-lookup"><span data-stu-id="67288-209">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-210">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67288-211">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-211">Run the following commands in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  Update-Database
    
  ```

<span data-ttu-id="67288-212">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="67288-212">The preceding commands:</span></span>

* <span data-ttu-id="67288-213">创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="67288-213">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="67288-214">更新 Migrations/SchoolContextModelSnapshot.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="67288-214">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="67288-215">此次更新将以下代码添加到 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="67288-215">The update adds the following code to the `BuildModel` method:</span></span>

```csharp
 b.Property<byte[]>("ConcurrencyToken")
     .IsConcurrencyToken()
     .ValueGeneratedOnAddOrUpdate()
     .HasColumnType("rowversion");
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="67288-217">在终端中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-217">Run the following commands in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  dotnet ef database update
  ```

<span data-ttu-id="67288-218">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="67288-218">The preceding commands:</span></span>

* <span data-ttu-id="67288-219">创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="67288-219">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="67288-220">更新 Migrations/SchoolContextModelSnapshot.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="67288-220">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="67288-221">此次更新将以下突出显示的代码添加到 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="67288-221">The update adds the following highlighted code to the `BuildModel` method:</span></span>

```csharp
b.Property<Guid>("ConcurrencyToken")
    .IsConcurrencyToken()
    .HasColumnType("TEXT");
```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="67288-222">搭建“院系”页面的基架</span><span class="sxs-lookup"><span data-stu-id="67288-222">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-223">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67288-224">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="67288-224">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>
  * <span data-ttu-id="67288-225">创建“Pages/Departments”文件夹。</span><span class="sxs-lookup"><span data-stu-id="67288-225">Create a *Pages/Departments* folder.</span></span>  
  * <span data-ttu-id="67288-226">将 `Department` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="67288-226">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="67288-227">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="67288-227">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="67288-229">创建“Pages/Departments”文件夹。</span><span class="sxs-lookup"><span data-stu-id="67288-229">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="67288-230">运行以下命令，搭建“院系”页的基架。</span><span class="sxs-lookup"><span data-stu-id="67288-230">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="67288-231">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="67288-231">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="67288-232">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="67288-232">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

### <a name="add-a-utility-class"></a><span data-ttu-id="67288-233">添加实用工具类</span><span class="sxs-lookup"><span data-stu-id="67288-233">Add a utility class</span></span>

<span data-ttu-id="67288-234">使用以下代码添加名为 `Utility` 的类：</span><span class="sxs-lookup"><span data-stu-id="67288-234">Add a class named `Utility` with the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-235">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet_sl)]

---

<span data-ttu-id="67288-237">`Utility` 类提供 `GetLastChars` 方法，该方法用于显示并发标记的最后几个字符。</span><span class="sxs-lookup"><span data-stu-id="67288-237">The `Utility` class provides the `GetLastChars` method used to display the last few characters of the concurrency token.</span></span> <span data-ttu-id="67288-238">下面的代码提供了同时使用 SQLite 和 SQL Server 的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-238">The following code shows the code that works with both SQLite ad SQL Server:</span></span>

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet_full)]

<span data-ttu-id="67288-239">`#if SQLiteVersion` [预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/)将分隔 SQLite 和 SQL Server 版本中的差异，并帮助：</span><span class="sxs-lookup"><span data-stu-id="67288-239">The `#if SQLiteVersion` [preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/) isolates the differences in the SQLite and SQL Server versions and helps:</span></span>

* <span data-ttu-id="67288-240">创作者维护一个同时用于这两个版本的代码库。</span><span class="sxs-lookup"><span data-stu-id="67288-240">The author maintain one code base for both versions.</span></span>
* <span data-ttu-id="67288-241">SQLite 开发人员将应用部署到 Azure 并使用 SQL Azure。</span><span class="sxs-lookup"><span data-stu-id="67288-241">SQLite developers deploy the app to Azure and use SQL Azure.</span></span>

<span data-ttu-id="67288-242">生成项目。</span><span class="sxs-lookup"><span data-stu-id="67288-242">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="67288-243">更新“索引”页</span><span class="sxs-lookup"><span data-stu-id="67288-243">Update the Index page</span></span>

<span data-ttu-id="67288-244">基架工具为“索引”页创建了 `ConcurrencyToken` 列，但生产应用中不会显示该字段。</span><span class="sxs-lookup"><span data-stu-id="67288-244">The scaffolding tool created a `ConcurrencyToken` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="67288-245">本教程中显示 `ConcurrencyToken` 的最后一个部分，以帮助展示并发处理的工作原理。</span><span class="sxs-lookup"><span data-stu-id="67288-245">In this tutorial, the last portion of the `ConcurrencyToken` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="67288-246">无法保证最后一个部分本身是唯一的。</span><span class="sxs-lookup"><span data-stu-id="67288-246">The last portion isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="67288-247">更新 Pages\Departments\Index.cshtml 页：</span><span class="sxs-lookup"><span data-stu-id="67288-247">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="67288-248">用院系替换索引。</span><span class="sxs-lookup"><span data-stu-id="67288-248">Replace Index with Departments.</span></span>
* <span data-ttu-id="67288-249">更改包含 `ConcurrencyToken` 的代码，以便仅显示最后几个字符。</span><span class="sxs-lookup"><span data-stu-id="67288-249">Change the code containing `ConcurrencyToken` to show just the last few characters.</span></span>
* <span data-ttu-id="67288-250">将 `FirstMidName` 替换为 `FullName`。</span><span class="sxs-lookup"><span data-stu-id="67288-250">Replace `FirstMidName` with `FullName`.</span></span>

<span data-ttu-id="67288-251">以下代码显示更新后的页面：</span><span class="sxs-lookup"><span data-stu-id="67288-251">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="67288-252">更新编辑页模型</span><span class="sxs-lookup"><span data-stu-id="67288-252">Update the Edit page model</span></span>

<span data-ttu-id="67288-253">使用以下代码更新 Pages\Departments\Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="67288-253">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-254">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_All_ss)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-255">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-255">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

---

<a name="c-up"></a>

### <a name="the-concurrency-updates"></a><span data-ttu-id="67288-256">并发更新</span><span class="sxs-lookup"><span data-stu-id="67288-256">The concurrency updates</span></span>

<span data-ttu-id="67288-257">当在 `OnGetAsync` 方法中提取时，<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> 是使用实体中的 `ConcurrencyToken` 值进行更新。</span><span class="sxs-lookup"><span data-stu-id="67288-257"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `ConcurrencyToken` value from the entity when it was fetched in the `OnGetAsync` method.</span></span> <span data-ttu-id="67288-258">EF Core 使用包含原始 `ConcurrencyToken` 值的 `WHERE` 子句生成 `SQL UPDATE` 命令。</span><span class="sxs-lookup"><span data-stu-id="67288-258">EF Core generates a `SQL UPDATE` command with a `WHERE` clause containing the original `ConcurrencyToken` value.</span></span> <span data-ttu-id="67288-259">如果 `UPDATE` 命令未影响任何行，则引发 `DbUpdateConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-259">If no rows are affected by the `UPDATE` command, a `DbUpdateConcurrencyException` exception is thrown.</span></span> <span data-ttu-id="67288-260">当所有行都不具有原始的 `ConcurrencyToken` 值，`UPDATE` 命令将不会影响任何行。</span><span class="sxs-lookup"><span data-stu-id="67288-260">No rows are affected by the `UPDATE` command when no rows have the original `ConcurrencyToken` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-261">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=19-99)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion_sl&highlight=21-99)]

---

<span data-ttu-id="67288-263">在上述突出显示的代码中：</span><span class="sxs-lookup"><span data-stu-id="67288-263">In the preceding highlighted code:</span></span>

* <span data-ttu-id="67288-264">`Department.ConcurrencyToken` 中的值是在 `Edit` 页的 `Get` 请求中提取实体时的值。</span><span class="sxs-lookup"><span data-stu-id="67288-264">The value in `Department.ConcurrencyToken` is the value when the entity was fetched in the `Get` request for the `Edit` page.</span></span> <span data-ttu-id="67288-265">通过 Razor 页面中显示将要编辑的实体的隐藏字段将该值提供给 `OnPost` 方法。</span><span class="sxs-lookup"><span data-stu-id="67288-265">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="67288-266">模型绑定器将隐藏字段值复制到 `Department.ConcurrencyToken`。</span><span class="sxs-lookup"><span data-stu-id="67288-266">The hidden field value is copied to `Department.ConcurrencyToken` by the model binder.</span></span>
* <span data-ttu-id="67288-267">`OriginalValue` 是 EF Core 用于 `WHERE` 子句的值。</span><span class="sxs-lookup"><span data-stu-id="67288-267">`OriginalValue` is what EF Core uses in the `WHERE` clause.</span></span> <span data-ttu-id="67288-268">在突出显示的代码行执行前：</span><span class="sxs-lookup"><span data-stu-id="67288-268">Before the highlighted line of code executes:</span></span>
  * <span data-ttu-id="67288-269">`OriginalValue` 具有在此方法中调用 `FirstOrDefaultAsync` 时数据库中所包含的值。</span><span class="sxs-lookup"><span data-stu-id="67288-269">`OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method.</span></span>
  * <span data-ttu-id="67288-270">此值可能与“编辑”页面显示的值不同。</span><span class="sxs-lookup"><span data-stu-id="67288-270">This value  might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="67288-271">突出显示的代码可确保 EF Core 使用原始 `ConcurrencyToken` 值，该值来自于 SQL `UPDATE` 语句的 `WHERE` 子句中所显示的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="67288-271">The highlighted code makes sure that EF Core uses the original `ConcurrencyToken`  value from the displayed `Department` entity in the SQL `UPDATE` statement's `WHERE` clause.</span></span>

<span data-ttu-id="67288-272">下面的代码演示了 `Department` 模型。</span><span class="sxs-lookup"><span data-stu-id="67288-272">The following code shows the `Department` model.</span></span> <span data-ttu-id="67288-273">`Department` 初始化：</span><span class="sxs-lookup"><span data-stu-id="67288-273">`Department` is initialized in the:</span></span>

  * <span data-ttu-id="67288-274">由 EF 查询在 `OnGetAsync` 方法中完成。</span><span class="sxs-lookup"><span data-stu-id="67288-274">`OnGetAsync` method by the EF query.</span></span>
  * <span data-ttu-id="67288-275">使用[模型绑定](xref:mvc/models/model-binding)的 Razor 页面中隐藏字段使用的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="67288-275">`OnPostAsync` method by the hidden field in the Razor page using [model binding](xref:mvc/models/model-binding):</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-276">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-276">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_mb&highlight=10-11,17-20,50-99)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_mb_ss&highlight=10-11,17-20,55-99)]

---

<span data-ttu-id="67288-278">前面的代码显示来自 `HTTP POST` 请求的 `Department` 实体的 `ConcurrencyToken` 值设置为来自 `HTTP GET` 请求的 `ConcurrencyToken` 值。</span><span class="sxs-lookup"><span data-stu-id="67288-278">The preceding code shows the `ConcurrencyToken` value  of the `Department` entity from the `HTTP POST` request is set to the `ConcurrencyToken` value from the `HTTP GET` request.</span></span>

<span data-ttu-id="67288-279">发生并发错误时，以下突出显示的代码会获取客户端值（发布到此方法的值）和数据库值。</span><span class="sxs-lookup"><span data-stu-id="67288-279">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="67288-280">以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：</span><span class="sxs-lookup"><span data-stu-id="67288-280">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="67288-281">以下突出显示的代码将 `ConcurrencyToken` 值设置为从数据库检索的新值。</span><span class="sxs-lookup"><span data-stu-id="67288-281">The following highlighted code sets the `ConcurrencyToken` value to the new value retrieved from the database.</span></span> <span data-ttu-id="67288-282">用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。</span><span class="sxs-lookup"><span data-stu-id="67288-282">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="67288-283">`ModelState` 具有先前的 `ConcurrencyToken` 值，因此需使用 [`ModelState.Remove`](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.Remove%2A) 语句。</span><span class="sxs-lookup"><span data-stu-id="67288-283">The [`ModelState.Remove`](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.Remove%2A) statement is required because `ModelState` has the previous `ConcurrencyToken` value.</span></span> <span data-ttu-id="67288-284">在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。</span><span class="sxs-lookup"><span data-stu-id="67288-284">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="sql-server-vs-sqlite-code-differences"></a><span data-ttu-id="67288-285">SQL Server 与 SQLite 代码差异</span><span class="sxs-lookup"><span data-stu-id="67288-285">SQL Server vs SQLite code differences</span></span>

<span data-ttu-id="67288-286">下面显示了 SQL Server 和 SQLite 版本之间的差异：</span><span class="sxs-lookup"><span data-stu-id="67288-286">The following shows the differences between the SQL Server and SQLite versions:</span></span>

```diff
+ using System;    // For GUID on SQLite

+ departmentToUpdate.ConcurrencyToken = Guid.NewGuid();

 _context.Entry(departmentToUpdate)
    .Property(d => d.ConcurrencyToken).OriginalValue = Department.ConcurrencyToken;

- Department.ConcurrencyToken = (byte[])dbValues.ConcurrencyToken;
+ Department.ConcurrencyToken = dbValues.ConcurrencyToken;
```

### <a name="update-the-edit-razor-page"></a><span data-ttu-id="67288-287">更新“编辑”Razor 页</span><span class="sxs-lookup"><span data-stu-id="67288-287">Update the Edit Razor page</span></span>

<span data-ttu-id="67288-288">使用以下代码更新 Pages/Departments/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="67288-288">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="67288-289">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-289">The preceding code:</span></span>

* <span data-ttu-id="67288-290">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="67288-290">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="67288-291">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-291">Adds a hidden row version.</span></span> <span data-ttu-id="67288-292">必须添加 `ConcurrencyToken`，以便回发能够绑定值。</span><span class="sxs-lookup"><span data-stu-id="67288-292">`ConcurrencyToken` must be added so postback binds the value.</span></span>
* <span data-ttu-id="67288-293">显示 `ConcurrencyToken` 的最后一个字节以进行调试。</span><span class="sxs-lookup"><span data-stu-id="67288-293">Displays the last byte of `ConcurrencyToken` for debugging purposes.</span></span>
* <span data-ttu-id="67288-294">将 `ViewData` 替换为强类型 `InstructorNameSL`。</span><span class="sxs-lookup"><span data-stu-id="67288-294">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="67288-295">使用编辑页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-295">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="67288-296">在英语系打开编辑的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="67288-296">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="67288-297">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="67288-297">Run the app and select Departments.</span></span>
* <span data-ttu-id="67288-298">右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="67288-298">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="67288-299">在第一个选项卡中，单击英语系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="67288-299">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="67288-300">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="67288-300">The two browser tabs display the same information.</span></span>

<span data-ttu-id="67288-301">在第一个浏览器选项卡中更改名称，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-301">Change the name in the first browser tab and click **Save**.</span></span>

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="67288-303">浏览器显示更改值并更新 `ConcurrencyToken` 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="67288-303">The browser shows the Index page with the changed value and updated `ConcurrencyToken`indicator.</span></span> <span data-ttu-id="67288-304">请注意更新后的 `ConcurrencyToken` 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="67288-304">Note the updated `ConcurrencyToken`indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="67288-305">在第二个浏览器选项卡中更改不同字段。</span><span class="sxs-lookup"><span data-stu-id="67288-305">Change a different field in the second browser tab.</span></span>

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="67288-307">单击“保存” 。</span><span class="sxs-lookup"><span data-stu-id="67288-307">Click **Save**.</span></span> <span data-ttu-id="67288-308">可看见所有不匹配数据库值的字段的错误消息：</span><span class="sxs-lookup"><span data-stu-id="67288-308">You see error messages for all fields that don't match the database values:</span></span>

![“院系编辑”页错误消息](concurrency/_static/edit-error30.png)

<span data-ttu-id="67288-310">此浏览器窗口将不会更改名称字段。</span><span class="sxs-lookup"><span data-stu-id="67288-310">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="67288-311">将当前值（语言）复制并粘贴到名称字段。</span><span class="sxs-lookup"><span data-stu-id="67288-311">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="67288-312">退出选项卡。客户端验证将删除错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-312">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="67288-313">再次单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-313">Click **Save** again.</span></span> <span data-ttu-id="67288-314">保存在第二个浏览器选项卡中输入的值。</span><span class="sxs-lookup"><span data-stu-id="67288-314">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="67288-315">在索引页中可以看到保存的值。</span><span class="sxs-lookup"><span data-stu-id="67288-315">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="67288-316">更新“删除”页面模型</span><span class="sxs-lookup"><span data-stu-id="67288-316">Update the Delete page model</span></span>

<span data-ttu-id="67288-317">使用以下代码更新 Pages/Departments/Delete.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="67288-317">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="67288-318">删除页检测提取实体并更改时的并发冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-318">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="67288-319">提取实体后，`Department.ConcurrencyToken` 为行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-319">`Department.ConcurrencyToken` is the row version when the entity was fetched.</span></span> <span data-ttu-id="67288-320">EF Core 创建 `SQL DELETE` 命令时，它包括具有的 `ConcurrencyToken` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="67288-320">When EF Core creates the `SQL DELETE` command, it includes a WHERE clause with `ConcurrencyToken`.</span></span> <span data-ttu-id="67288-321">如果 `SQL DELETE` 命令导致零行受影响：</span><span class="sxs-lookup"><span data-stu-id="67288-321">If the `SQL DELETE` command results in zero rows affected:</span></span>

* <span data-ttu-id="67288-322">`SQL DELETE` 命令中的 `ConcurrencyToken` 与数据库中的 `ConcurrencyToken` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="67288-322">The `ConcurrencyToken` in the `SQL DELETE` command doesn't match `ConcurrencyToken` in the database.</span></span>
* <span data-ttu-id="67288-323">这将引发 `DbUpdateConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-323">A `DbUpdateConcurrencyException` exception is thrown.</span></span>
* <span data-ttu-id="67288-324">使用 `concurrencyError` 调用 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="67288-324">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="67288-325">更新“删除 Razor”页面</span><span class="sxs-lookup"><span data-stu-id="67288-325">Update the Delete Razor page</span></span>

<span data-ttu-id="67288-326">使用以下代码更新 Pages/Departments/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="67288-326">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="67288-327">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="67288-327">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="67288-328">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="67288-328">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="67288-329">添加错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-329">Adds an error message.</span></span>
* <span data-ttu-id="67288-330">将“管理员”字段中的 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="67288-330">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="67288-331">更改 `ConcurrencyToken` 以显示最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="67288-331">Changes `ConcurrencyToken` to display the last byte.</span></span>
* <span data-ttu-id="67288-332">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-332">Adds a hidden row version.</span></span> <span data-ttu-id="67288-333">必须添加 `ConcurrencyToken`，以便回发能够绑定值。</span><span class="sxs-lookup"><span data-stu-id="67288-333">`ConcurrencyToken` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="67288-334">测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-334">Test concurrency conflicts</span></span>

<span data-ttu-id="67288-335">创建测试系。</span><span class="sxs-lookup"><span data-stu-id="67288-335">Create a test department.</span></span>

<span data-ttu-id="67288-336">在测试系打开删除的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="67288-336">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="67288-337">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="67288-337">Run the app and select Departments.</span></span>
* <span data-ttu-id="67288-338">右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="67288-338">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="67288-339">单击测试系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="67288-339">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="67288-340">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="67288-340">The two browser tabs display the same information.</span></span>

<span data-ttu-id="67288-341">在第一个浏览器选项卡中更改预算，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-341">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="67288-342">浏览器显示更改值并更新 `ConcurrencyToken` 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="67288-342">The browser shows the Index page with the changed value and updated `ConcurrencyToken`indicator.</span></span> <span data-ttu-id="67288-343">请注意更新后的 `ConcurrencyToken` 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="67288-343">Note the updated `ConcurrencyToken`indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="67288-344">从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。</span><span class="sxs-lookup"><span data-stu-id="67288-344">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="67288-345">单击“删除”删除实体，除非 `ConcurrencyToken` 已更新。</span><span class="sxs-lookup"><span data-stu-id="67288-345">Clicking **Delete** deletes the entity, unless `ConcurrencyToken` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67288-346">其他资源</span><span class="sxs-lookup"><span data-stu-id="67288-346">Additional resources</span></span>

* [<span data-ttu-id="67288-347">EF Core 中的并发令牌</span><span class="sxs-lookup"><span data-stu-id="67288-347">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="67288-348">EF Core 中的并发处理</span><span class="sxs-lookup"><span data-stu-id="67288-348">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="67288-349">调试 ASP.NET Core 2.x 源</span><span class="sxs-lookup"><span data-stu-id="67288-349">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="67288-350">后续步骤</span><span class="sxs-lookup"><span data-stu-id="67288-350">Next steps</span></span>

<span data-ttu-id="67288-351">这是本系列的最后一个教程。</span><span class="sxs-lookup"><span data-stu-id="67288-351">This is the last tutorial in the series.</span></span> <span data-ttu-id="67288-352">[本系列教程的 MVC 版本](xref:data/ef-mvc/index)中介绍了其他主题。</span><span class="sxs-lookup"><span data-stu-id="67288-352">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="67288-353">上一个教程</span><span class="sxs-lookup"><span data-stu-id="67288-353">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="67288-354">本教程介绍如何处理多个用户并发更新同一实体（同时）时出现的冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-354">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="67288-355">并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-355">Concurrency conflicts</span></span>

<span data-ttu-id="67288-356">在以下情况下，会发生并发冲突：</span><span class="sxs-lookup"><span data-stu-id="67288-356">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="67288-357">用户导航到实体的编辑页面。</span><span class="sxs-lookup"><span data-stu-id="67288-357">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="67288-358">第一个用户的更改还未写入数据库之前，另一用户更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="67288-358">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="67288-359">如果未启用并发检测，则最后更新数据库的人员将覆盖其他用户的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-359">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="67288-360">如果这种风险是可以接受的，则并发编程的成本可能会超过收益。</span><span class="sxs-lookup"><span data-stu-id="67288-360">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="67288-361">悲观并发（锁定）</span><span class="sxs-lookup"><span data-stu-id="67288-361">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="67288-362">预防并发冲突的一种方法是使用数据库锁定。</span><span class="sxs-lookup"><span data-stu-id="67288-362">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="67288-363">这称为悲观并发。</span><span class="sxs-lookup"><span data-stu-id="67288-363">This is called pessimistic concurrency.</span></span> <span data-ttu-id="67288-364">应用在读取要更新的数据库行之前，将请求锁定。</span><span class="sxs-lookup"><span data-stu-id="67288-364">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="67288-365">锁定某一行的更新访问权限之后，其他用户在第一个锁定释放之前无法锁定该行。</span><span class="sxs-lookup"><span data-stu-id="67288-365">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="67288-366">管理锁定有缺点。</span><span class="sxs-lookup"><span data-stu-id="67288-366">Managing locks has disadvantages.</span></span> <span data-ttu-id="67288-367">它的编程可能很复杂，并且随着用户增加可能会导致性能问题。</span><span class="sxs-lookup"><span data-stu-id="67288-367">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="67288-368">Entity Framework Core 未提供对它的内置支持，并且本教程不展示其实现方式。</span><span class="sxs-lookup"><span data-stu-id="67288-368">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="67288-369">开放式并发</span><span class="sxs-lookup"><span data-stu-id="67288-369">Optimistic concurrency</span></span>

<span data-ttu-id="67288-370">乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。</span><span class="sxs-lookup"><span data-stu-id="67288-370">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="67288-371">例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-371">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![将预算更改为零](concurrency/_static/change-budget30.png)

<span data-ttu-id="67288-373">在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。</span><span class="sxs-lookup"><span data-stu-id="67288-373">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![将开始日期更改为 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="67288-375">Jane 单击“保存”后看到更改生效，因为浏览器会显示预算金额为零的“索引”页面。</span><span class="sxs-lookup"><span data-stu-id="67288-375">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="67288-376">John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-376">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="67288-377">接下来的情况取决于并发冲突的处理方式：</span><span class="sxs-lookup"><span data-stu-id="67288-377">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="67288-378">可以跟踪用户已修改的属性，并仅更新数据库中相应的列。</span><span class="sxs-lookup"><span data-stu-id="67288-378">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="67288-379">在这种情况下，数据不会丢失。</span><span class="sxs-lookup"><span data-stu-id="67288-379">In the scenario, no data would be lost.</span></span> <span data-ttu-id="67288-380">两个用户更新了不同的属性。</span><span class="sxs-lookup"><span data-stu-id="67288-380">Different properties were updated by the two users.</span></span> <span data-ttu-id="67288-381">下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-381">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="67288-382">这种更新方法可以减少导致数据丢失的冲突数。</span><span class="sxs-lookup"><span data-stu-id="67288-382">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="67288-383">这种方法具有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="67288-383">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="67288-384">无法避免数据丢失，如果对同一属性进行竞争性更改的话。</span><span class="sxs-lookup"><span data-stu-id="67288-384">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="67288-385">通常不适用于 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="67288-385">Is generally not practical in a web app.</span></span> <span data-ttu-id="67288-386">它需要维持重要状态，以便跟踪所有提取值和新值。</span><span class="sxs-lookup"><span data-stu-id="67288-386">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="67288-387">维持大量状态可能影响应用性能。</span><span class="sxs-lookup"><span data-stu-id="67288-387">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="67288-388">可能会增加应用复杂性（与实体上的并发检测相比）。</span><span class="sxs-lookup"><span data-stu-id="67288-388">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="67288-389">可让 John 的更改覆盖 Jane 的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-389">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="67288-390">下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-390">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="67288-391">这种方法称为“客户端优先”或“最后一个优先”方案 。</span><span class="sxs-lookup"><span data-stu-id="67288-391">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="67288-392">（客户端的所有值优先于数据存储的值。）如果不对并发处理进行任何编码，则自动执行“客户端优先”。</span><span class="sxs-lookup"><span data-stu-id="67288-392">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="67288-393">可以阻止在数据库中更新 John 的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-393">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="67288-394">应用通常会：</span><span class="sxs-lookup"><span data-stu-id="67288-394">Typically, the app would:</span></span>

  * <span data-ttu-id="67288-395">显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-395">Display an error message.</span></span>
  * <span data-ttu-id="67288-396">显示数据的当前状态。</span><span class="sxs-lookup"><span data-stu-id="67288-396">Show the current state of the data.</span></span>
  * <span data-ttu-id="67288-397">允许用户重新应用更改。</span><span class="sxs-lookup"><span data-stu-id="67288-397">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="67288-398">这称为“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="67288-398">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="67288-399">（数据存储值优先于客户端提交的值。）本教程实施“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="67288-399">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="67288-400">此方法可确保用户在未收到警报时不会覆盖任何更改。</span><span class="sxs-lookup"><span data-stu-id="67288-400">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="67288-401">EF Core 中的冲突检测</span><span class="sxs-lookup"><span data-stu-id="67288-401">Conflict detection in EF Core</span></span>

<span data-ttu-id="67288-402">EF Core 在检测到冲突时会引发 `DbConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-402">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="67288-403">数据模型必须配置为启用冲突检测。</span><span class="sxs-lookup"><span data-stu-id="67288-403">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="67288-404">启用冲突检测的选项包括以下项：</span><span class="sxs-lookup"><span data-stu-id="67288-404">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="67288-405">配置 EF Core，在 Update 或 Delete 命令的 Where 子句中包含配置为[并发令牌](/ef/core/modeling/concurrency)的列的原始值。</span><span class="sxs-lookup"><span data-stu-id="67288-405">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="67288-406">调用 `SaveChanges` 时，Where 子句查找使用 <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 特性注释的所有属性的原始值。</span><span class="sxs-lookup"><span data-stu-id="67288-406">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="67288-407">如果在第一次读取行之后有任意并发令牌属性发生了更改，更新语句将无法查找到要更新的行。</span><span class="sxs-lookup"><span data-stu-id="67288-407">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="67288-408">EF Core 将其解释为并发冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-408">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="67288-409">对于包含许多列的数据库表，此方法可能导致非常多的 Where 子句，并且可能需要大量的状态。</span><span class="sxs-lookup"><span data-stu-id="67288-409">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="67288-410">因此通常不建议使用此方法，并且它也不是本教程中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="67288-410">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="67288-411">数据库表中包含一个可用于确定某行更改时间的跟踪列。</span><span class="sxs-lookup"><span data-stu-id="67288-411">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="67288-412">在 SQL Server 数据库中，跟踪列的数据类型是 `rowversion`。</span><span class="sxs-lookup"><span data-stu-id="67288-412">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="67288-413">`rowversion` 值是一个序列号，该编号随着每次行的更新递增。</span><span class="sxs-lookup"><span data-stu-id="67288-413">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="67288-414">在 Update 或 Delete 命令中，Where 子句包含跟踪列的原始值（原始行版本号）。</span><span class="sxs-lookup"><span data-stu-id="67288-414">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="67288-415">如果其他用户已更改要更新的行，则 `rowversion` 列中的值与原始值不同。</span><span class="sxs-lookup"><span data-stu-id="67288-415">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="67288-416">在这种情况下，Update 或 Delete 语句会由于 Where 子句而无法找到要更新的行。</span><span class="sxs-lookup"><span data-stu-id="67288-416">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="67288-417">如果 Update 或 Delete 命令未影响任何行，EF Core 会引发并发异常。</span><span class="sxs-lookup"><span data-stu-id="67288-417">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="67288-418">添加跟踪属性</span><span class="sxs-lookup"><span data-stu-id="67288-418">Add a tracking property</span></span>

<span data-ttu-id="67288-419">在 Models/Department.cs 中，添加名为 RowVersion 的跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="67288-419">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="67288-420"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 特性用于将列标识为并发跟踪列。</span><span class="sxs-lookup"><span data-stu-id="67288-420">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="67288-421">Fluent API 是指定跟踪属性的另一种方法：</span><span class="sxs-lookup"><span data-stu-id="67288-421">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="67288-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67288-423">对于 SQL Server 数据库，定义为字节数组的实体属性上的 `[Timestamp]` 特性：</span><span class="sxs-lookup"><span data-stu-id="67288-423">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="67288-424">使列包含在 DELETE 和 UPDATE WHERE 子句中。</span><span class="sxs-lookup"><span data-stu-id="67288-424">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="67288-425">将数据库中的列类型设置为 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="67288-425">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="67288-426">数据库生成有序的行版本号，该版本号随着每次行的更新递增。</span><span class="sxs-lookup"><span data-stu-id="67288-426">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="67288-427">在 `Update` 或 `Delete` 命令中，`Where` 子句包括提取的行版本值。</span><span class="sxs-lookup"><span data-stu-id="67288-427">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="67288-428">如果要更新的行在提取之后已更改：</span><span class="sxs-lookup"><span data-stu-id="67288-428">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="67288-429">当前的行版本值与提取值不相匹配。</span><span class="sxs-lookup"><span data-stu-id="67288-429">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="67288-430">`Update` 或 `Delete` 命令不查找行，因为 `Where` 子句会查找提取行的版本值。</span><span class="sxs-lookup"><span data-stu-id="67288-430">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="67288-431">引发一个 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-431">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="67288-432">以下代码显示更新 Department 名称时由 EF Core 生成的部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="67288-432">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="67288-433">前面突出显示的代码显示包含 `RowVersion` 的 `WHERE` 子句。</span><span class="sxs-lookup"><span data-stu-id="67288-433">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="67288-434">如果数据库 `RowVersion` 不等于 `RowVersion` 参数 (`@p2`)，则不更新行。</span><span class="sxs-lookup"><span data-stu-id="67288-434">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="67288-435">以下突出显示的代码显示验证更新哪一行的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="67288-435">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="67288-436">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。</span><span class="sxs-lookup"><span data-stu-id="67288-436">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="67288-437">如果没有更新行，EF Core 会引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-437">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="67288-439">对于 SQLite 数据库，定义为字节数组的实体属性上的 `[Timestamp]` 特性：</span><span class="sxs-lookup"><span data-stu-id="67288-439">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="67288-440">使列包含在 DELETE 和 UPDATE WHERE 子句中。</span><span class="sxs-lookup"><span data-stu-id="67288-440">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="67288-441">映射到 BLOB 列类型。</span><span class="sxs-lookup"><span data-stu-id="67288-441">Maps to a BLOB column type.</span></span>

<span data-ttu-id="67288-442">每当行更新时，数据库触发器都会将 RowVersion 列更新为新的随机字节数组。</span><span class="sxs-lookup"><span data-stu-id="67288-442">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="67288-443">在 `Update` 或 `Delete` 命令中，`Where` 子句包括 RowVersion 列的提取值。</span><span class="sxs-lookup"><span data-stu-id="67288-443">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="67288-444">如果要更新的行在提取之后已更改：</span><span class="sxs-lookup"><span data-stu-id="67288-444">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="67288-445">当前的行版本值与提取值不相匹配。</span><span class="sxs-lookup"><span data-stu-id="67288-445">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="67288-446">`Update` 或 `Delete` 命令不查找行，因为 `Where` 子句会查找原始行版本值。</span><span class="sxs-lookup"><span data-stu-id="67288-446">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="67288-447">引发一个 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-447">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="67288-448">更新数据库</span><span class="sxs-lookup"><span data-stu-id="67288-448">Update the database</span></span>

<span data-ttu-id="67288-449">添加 `RowVersion` 属性可更改需要迁移的数据库模型。</span><span class="sxs-lookup"><span data-stu-id="67288-449">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="67288-450">生成项目。</span><span class="sxs-lookup"><span data-stu-id="67288-450">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="67288-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-451">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="67288-452">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-452">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-453">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-453">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="67288-454">在终端中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-454">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="67288-455">此命令：</span><span class="sxs-lookup"><span data-stu-id="67288-455">This command:</span></span>

* <span data-ttu-id="67288-456">创建 Migrations/{time stamp}_RowVersion.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="67288-456">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="67288-457">更新 Migrations/SchoolContextModelSnapshot.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="67288-457">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="67288-458">此次更新将以下突出显示的代码添加到 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="67288-458">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="67288-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="67288-460">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-460">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="67288-462">打开 `Migrations/<timestamp>_RowVersion.cs` 文件，并添加以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-462">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="67288-463">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-463">The preceding code:</span></span>

  * <span data-ttu-id="67288-464">将现有行更新为随机 blob 值。</span><span class="sxs-lookup"><span data-stu-id="67288-464">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="67288-465">添加数据库触发器，该触发器在行更新时将 RowVersion 列设置为随机 blob 值。</span><span class="sxs-lookup"><span data-stu-id="67288-465">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="67288-466">在终端中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-466">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="67288-467">搭建“院系”页面的基架</span><span class="sxs-lookup"><span data-stu-id="67288-467">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="67288-469">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="67288-469">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="67288-470">创建“Pages/Departments”文件夹。</span><span class="sxs-lookup"><span data-stu-id="67288-470">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="67288-471">将 `Department` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="67288-471">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="67288-472">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="67288-472">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-473">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-473">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="67288-474">创建“Pages/Departments”文件夹。</span><span class="sxs-lookup"><span data-stu-id="67288-474">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="67288-475">运行以下命令，搭建“院系”页的基架。</span><span class="sxs-lookup"><span data-stu-id="67288-475">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="67288-476">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="67288-476">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="67288-477">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="67288-477">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="67288-478">生成项目。</span><span class="sxs-lookup"><span data-stu-id="67288-478">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="67288-479">更新“索引”页</span><span class="sxs-lookup"><span data-stu-id="67288-479">Update the Index page</span></span>

<span data-ttu-id="67288-480">基架工具为“索引”页创建了 `RowVersion` 列，但生产应用中不会显示该字段。</span><span class="sxs-lookup"><span data-stu-id="67288-480">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="67288-481">本教程中显示 `RowVersion` 的最后一个字节，以帮助展示并发处理的工作原理。</span><span class="sxs-lookup"><span data-stu-id="67288-481">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="67288-482">无法保证最后一个字节本身是唯一的。</span><span class="sxs-lookup"><span data-stu-id="67288-482">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="67288-483">更新 Pages\Departments\Index.cshtml 页：</span><span class="sxs-lookup"><span data-stu-id="67288-483">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="67288-484">用院系替换索引。</span><span class="sxs-lookup"><span data-stu-id="67288-484">Replace Index with Departments.</span></span>
* <span data-ttu-id="67288-485">更改包含 `RowVersion` 的代码，以便只显示字节数组的最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="67288-485">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="67288-486">将 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="67288-486">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="67288-487">以下代码显示更新后的页面：</span><span class="sxs-lookup"><span data-stu-id="67288-487">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="67288-488">更新编辑页模型</span><span class="sxs-lookup"><span data-stu-id="67288-488">Update the Edit page model</span></span>

<span data-ttu-id="67288-489">使用以下代码更新 Pages\Departments\Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="67288-489">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="67288-490">当在 `OnGetAsync` 方法中提取时，<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> 是使用实体中的 `rowVersion` 值进行更新。</span><span class="sxs-lookup"><span data-stu-id="67288-490">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGetAsync` method.</span></span> <span data-ttu-id="67288-491">EF Core 使用包含原始 `RowVersion` 值的 WHERE 子句生成 SQL UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="67288-491">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="67288-492">如果没有行受到 UPDATE 命令影响（没有行具有原始 `RowVersion` 值），将引发 `DbUpdateConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-492">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="67288-493">在上述突出显示的代码中：</span><span class="sxs-lookup"><span data-stu-id="67288-493">In the preceding highlighted code:</span></span>

* <span data-ttu-id="67288-494">`Department.RowVersion` 中的值是最初在“编辑”页的 Get 请求中所提取的实体中的值。</span><span class="sxs-lookup"><span data-stu-id="67288-494">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="67288-495">通过 Razor 页面中显示将要编辑的实体的隐藏字段将该值提供给 `OnPost` 方法。</span><span class="sxs-lookup"><span data-stu-id="67288-495">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="67288-496">模型绑定器将隐藏字段值复制到 `Department.RowVersion`。</span><span class="sxs-lookup"><span data-stu-id="67288-496">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="67288-497">`OriginalValue` 是 EF Core 将用于 Where 子句的值。</span><span class="sxs-lookup"><span data-stu-id="67288-497">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="67288-498">在执行突出显示的代码行之前，`OriginalValue` 具有在此方法中调用 `FirstOrDefaultAsync` 时数据库中的值，该值可能与“编辑”页面上所显示的值不同。</span><span class="sxs-lookup"><span data-stu-id="67288-498">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="67288-499">突出显示的代码可确保 EF Core 使用原始 `RowVersion` 值，该值来自于 SQL UPDATE 语句的 Where 子句中所显示的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="67288-499">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="67288-500">发生并发错误时，以下突出显示的代码会获取客户端值（发布到此方法的值）和数据库值。</span><span class="sxs-lookup"><span data-stu-id="67288-500">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="67288-501">以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：</span><span class="sxs-lookup"><span data-stu-id="67288-501">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="67288-502">以下突出显示的代码将 `RowVersion` 值设置为从数据库检索的新值。</span><span class="sxs-lookup"><span data-stu-id="67288-502">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="67288-503">用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。</span><span class="sxs-lookup"><span data-stu-id="67288-503">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="67288-504">`ModelState` 具有旧的 `RowVersion` 值，因此需使用 `ModelState.Remove` 语句。</span><span class="sxs-lookup"><span data-stu-id="67288-504">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="67288-505">在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。</span><span class="sxs-lookup"><span data-stu-id="67288-505">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="67288-506">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="67288-506">Update the Edit page</span></span>

<span data-ttu-id="67288-507">使用以下代码更新 Pages/Departments/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="67288-507">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="67288-508">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="67288-508">The preceding code:</span></span>

* <span data-ttu-id="67288-509">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="67288-509">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="67288-510">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-510">Adds a hidden row version.</span></span> <span data-ttu-id="67288-511">必须添加 `RowVersion`，以便回发能够绑定值。</span><span class="sxs-lookup"><span data-stu-id="67288-511">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="67288-512">显示 `RowVersion` 的最后一个字节以进行调试。</span><span class="sxs-lookup"><span data-stu-id="67288-512">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="67288-513">将 `ViewData` 替换为强类型 `InstructorNameSL`。</span><span class="sxs-lookup"><span data-stu-id="67288-513">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="67288-514">使用编辑页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-514">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="67288-515">在英语系打开编辑的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="67288-515">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="67288-516">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="67288-516">Run the app and select Departments.</span></span>
* <span data-ttu-id="67288-517">右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="67288-517">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="67288-518">在第一个选项卡中，单击英语系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="67288-518">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="67288-519">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="67288-519">The two browser tabs display the same information.</span></span>

<span data-ttu-id="67288-520">在第一个浏览器选项卡中更改名称，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-520">Change the name in the first browser tab and click **Save**.</span></span>

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="67288-522">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="67288-522">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="67288-523">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="67288-523">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="67288-524">在第二个浏览器选项卡中更改不同字段。</span><span class="sxs-lookup"><span data-stu-id="67288-524">Change a different field in the second browser tab.</span></span>

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="67288-526">单击“保存” 。</span><span class="sxs-lookup"><span data-stu-id="67288-526">Click **Save**.</span></span> <span data-ttu-id="67288-527">可看见所有不匹配数据库值的字段的错误消息：</span><span class="sxs-lookup"><span data-stu-id="67288-527">You see error messages for all fields that don't match the database values:</span></span>

![“院系编辑”页错误消息](concurrency/_static/edit-error30.png)

<span data-ttu-id="67288-529">此浏览器窗口将不会更改名称字段。</span><span class="sxs-lookup"><span data-stu-id="67288-529">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="67288-530">将当前值（语言）复制并粘贴到名称字段。</span><span class="sxs-lookup"><span data-stu-id="67288-530">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="67288-531">退出选项卡。客户端验证将删除错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-531">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="67288-532">再次单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-532">Click **Save** again.</span></span> <span data-ttu-id="67288-533">保存在第二个浏览器选项卡中输入的值。</span><span class="sxs-lookup"><span data-stu-id="67288-533">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="67288-534">在索引页中可以看到保存的值。</span><span class="sxs-lookup"><span data-stu-id="67288-534">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="67288-535">更新“删除”页面模型</span><span class="sxs-lookup"><span data-stu-id="67288-535">Update the Delete page model</span></span>

<span data-ttu-id="67288-536">使用以下代码更新 Pages/Departments/Delete.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="67288-536">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="67288-537">删除页检测提取实体并更改时的并发冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-537">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="67288-538">提取实体后，`Department.RowVersion` 为行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-538">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="67288-539">EF Core 创建 SQL DELETE 命令时，它包括具有 `RowVersion` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="67288-539">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="67288-540">如果 SQL DELETE 命令导致零行受影响：</span><span class="sxs-lookup"><span data-stu-id="67288-540">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="67288-541">SQL DELETE 命令中的 `RowVersion` 与数据库中的 `RowVersion` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="67288-541">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="67288-542">引发 DbUpdateConcurrencyException 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-542">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="67288-543">使用 `concurrencyError` 调用 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="67288-543">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="67288-544">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="67288-544">Update the Delete page</span></span>

<span data-ttu-id="67288-545">使用以下代码更新 Pages/Departments/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="67288-545">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="67288-546">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="67288-546">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="67288-547">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="67288-547">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="67288-548">添加错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-548">Adds an error message.</span></span>
* <span data-ttu-id="67288-549">将“管理员”字段中的 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="67288-549">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="67288-550">更改 `RowVersion` 以显示最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="67288-550">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="67288-551">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-551">Adds a hidden row version.</span></span> <span data-ttu-id="67288-552">必须添加 `RowVersion`，以便回发能够绑定值。</span><span class="sxs-lookup"><span data-stu-id="67288-552">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="67288-553">测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-553">Test concurrency conflicts</span></span>

<span data-ttu-id="67288-554">创建测试系。</span><span class="sxs-lookup"><span data-stu-id="67288-554">Create a test department.</span></span>

<span data-ttu-id="67288-555">在测试系打开删除的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="67288-555">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="67288-556">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="67288-556">Run the app and select Departments.</span></span>
* <span data-ttu-id="67288-557">右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="67288-557">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="67288-558">单击测试系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="67288-558">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="67288-559">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="67288-559">The two browser tabs display the same information.</span></span>

<span data-ttu-id="67288-560">在第一个浏览器选项卡中更改预算，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-560">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="67288-561">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="67288-561">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="67288-562">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="67288-562">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="67288-563">从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。</span><span class="sxs-lookup"><span data-stu-id="67288-563">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="67288-564">单击“删除”删除实体，除非 `RowVersion` 已更新。</span><span class="sxs-lookup"><span data-stu-id="67288-564">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67288-565">其他资源</span><span class="sxs-lookup"><span data-stu-id="67288-565">Additional resources</span></span>

* [<span data-ttu-id="67288-566">EF Core 中的并发令牌</span><span class="sxs-lookup"><span data-stu-id="67288-566">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="67288-567">EF Core 中的并发处理</span><span class="sxs-lookup"><span data-stu-id="67288-567">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="67288-568">调试 ASP.NET Core 2.x 源</span><span class="sxs-lookup"><span data-stu-id="67288-568">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="67288-569">后续步骤</span><span class="sxs-lookup"><span data-stu-id="67288-569">Next steps</span></span>

<span data-ttu-id="67288-570">这是本系列的最后一个教程。</span><span class="sxs-lookup"><span data-stu-id="67288-570">This is the last tutorial in the series.</span></span> <span data-ttu-id="67288-571">[本系列教程的 MVC 版本](xref:data/ef-mvc/index)中介绍了其他主题。</span><span class="sxs-lookup"><span data-stu-id="67288-571">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="67288-572">上一个教程</span><span class="sxs-lookup"><span data-stu-id="67288-572">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="67288-573">本教程介绍如何处理多个用户并发更新同一实体（同时）时出现的冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-573">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="67288-574">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="67288-574">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="67288-575">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="67288-575">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="67288-576">并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-576">Concurrency conflicts</span></span>

<span data-ttu-id="67288-577">在以下情况下，会发生并发冲突：</span><span class="sxs-lookup"><span data-stu-id="67288-577">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="67288-578">用户导航到实体的编辑页面。</span><span class="sxs-lookup"><span data-stu-id="67288-578">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="67288-579">第一个用户的更改还未写入数据库之前，另一用户更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="67288-579">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="67288-580">如果未启用并发检测，当发生并发更新时：</span><span class="sxs-lookup"><span data-stu-id="67288-580">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="67288-581">最后一个更新优先。</span><span class="sxs-lookup"><span data-stu-id="67288-581">The last update wins.</span></span> <span data-ttu-id="67288-582">也就是最后一个更新的值保存至数据库。</span><span class="sxs-lookup"><span data-stu-id="67288-582">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="67288-583">第一个并发更新将会丢失。</span><span class="sxs-lookup"><span data-stu-id="67288-583">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="67288-584">开放式并发</span><span class="sxs-lookup"><span data-stu-id="67288-584">Optimistic concurrency</span></span>

<span data-ttu-id="67288-585">乐观并发允许发生并发冲突，并在并发冲突发生时作出正确反应。</span><span class="sxs-lookup"><span data-stu-id="67288-585">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="67288-586">例如，Jane 访问院系编辑页面，将英语系的预算从 350,000.00 美元更改为 0.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-586">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![将预算更改为零](concurrency/_static/change-budget.png)

<span data-ttu-id="67288-588">在 Jane 单击“保存”之前，John 访问了相同页面，并将开始日期字段从 2007/1/9 更改为 2013/1/9。</span><span class="sxs-lookup"><span data-stu-id="67288-588">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![将开始日期更改为 2013](concurrency/_static/change-date.png)

<span data-ttu-id="67288-590">Jane 先单击“保存”，并在浏览器显示索引页时看到她的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-590">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![预算已更改为零](concurrency/_static/budget-zero.png)

<span data-ttu-id="67288-592">John 单击“编辑”页面上的“保存”，但页面的预算仍显示为 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-592">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="67288-593">接下来的情况取决于并发冲突的处理方式。</span><span class="sxs-lookup"><span data-stu-id="67288-593">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="67288-594">乐观并发包括以下选项：</span><span class="sxs-lookup"><span data-stu-id="67288-594">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="67288-595">可以跟踪用户已修改的属性，并仅更新数据库中相应的列。</span><span class="sxs-lookup"><span data-stu-id="67288-595">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="67288-596">在这种情况下，数据不会丢失。</span><span class="sxs-lookup"><span data-stu-id="67288-596">In the scenario, no data would be lost.</span></span> <span data-ttu-id="67288-597">两个用户更新了不同的属性。</span><span class="sxs-lookup"><span data-stu-id="67288-597">Different properties were updated by the two users.</span></span> <span data-ttu-id="67288-598">下次有人浏览英语系时，将看到 Jane 和 John 两个人的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-598">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="67288-599">这种更新方法可以减少导致数据丢失的冲突数。</span><span class="sxs-lookup"><span data-stu-id="67288-599">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="67288-600">这种方法：</span><span class="sxs-lookup"><span data-stu-id="67288-600">This approach:</span></span>
 
  * <span data-ttu-id="67288-601">无法避免数据丢失，如果对同一属性进行竞争性更改的话。</span><span class="sxs-lookup"><span data-stu-id="67288-601">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="67288-602">通常不适用于 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="67288-602">Is generally not practical in a web app.</span></span> <span data-ttu-id="67288-603">它需要维持重要状态，以便跟踪所有提取值和新值。</span><span class="sxs-lookup"><span data-stu-id="67288-603">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="67288-604">维持大量状态可能影响应用性能。</span><span class="sxs-lookup"><span data-stu-id="67288-604">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="67288-605">可能会增加应用复杂性（与实体上的并发检测相比）。</span><span class="sxs-lookup"><span data-stu-id="67288-605">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="67288-606">可让 John 的更改覆盖 Jane 的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-606">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="67288-607">下次有人浏览英语系时，将看到 2013/9/1 和提取的值 350,000.00 美元。</span><span class="sxs-lookup"><span data-stu-id="67288-607">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="67288-608">这种方法称为“客户端优先”或“最后一个优先”方案 。</span><span class="sxs-lookup"><span data-stu-id="67288-608">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="67288-609">（客户端的所有值优先于数据存储的值。）如果不对并发处理进行任何编码，则自动执行“客户端优先”。</span><span class="sxs-lookup"><span data-stu-id="67288-609">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="67288-610">可以阻止在数据库中更新 John 的更改。</span><span class="sxs-lookup"><span data-stu-id="67288-610">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="67288-611">应用通常会：</span><span class="sxs-lookup"><span data-stu-id="67288-611">Typically, the app would:</span></span>

  * <span data-ttu-id="67288-612">显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-612">Display an error message.</span></span>
  * <span data-ttu-id="67288-613">显示数据的当前状态。</span><span class="sxs-lookup"><span data-stu-id="67288-613">Show the current state of the data.</span></span>
  * <span data-ttu-id="67288-614">允许用户重新应用更改。</span><span class="sxs-lookup"><span data-stu-id="67288-614">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="67288-615">这称为“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="67288-615">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="67288-616">（数据存储值优先于客户端提交的值。）本教程实施“存储优先”方案。</span><span class="sxs-lookup"><span data-stu-id="67288-616">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="67288-617">此方法可确保用户在未收到警报时不会覆盖任何更改。</span><span class="sxs-lookup"><span data-stu-id="67288-617">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="67288-618">处理并发</span><span class="sxs-lookup"><span data-stu-id="67288-618">Handling concurrency</span></span>

<span data-ttu-id="67288-619">当属性配置为[并发令牌](/ef/core/modeling/concurrency)时：</span><span class="sxs-lookup"><span data-stu-id="67288-619">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="67288-620">EF Core 验证提取属性后是否未更改属性。</span><span class="sxs-lookup"><span data-stu-id="67288-620">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="67288-621">调用 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 或 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 时会执行此检查。</span><span class="sxs-lookup"><span data-stu-id="67288-621">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="67288-622">如果提取属性后更改了属性，将引发 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)。</span><span class="sxs-lookup"><span data-stu-id="67288-622">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="67288-623">数据库和数据模型必须配置为支持引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-623">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="67288-624">检测属性的并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-624">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="67288-625">可使用 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 特性在属性级别检测并发冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-625">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="67288-626">该特性可应用于模型上的多个属性。</span><span class="sxs-lookup"><span data-stu-id="67288-626">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="67288-627">有关详细信息，请参阅[数据注释 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="67288-627">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="67288-628">本教程中不使用 `[ConcurrencyCheck]` 特性。</span><span class="sxs-lookup"><span data-stu-id="67288-628">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="67288-629">检测行的并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-629">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="67288-630">要检测并发冲突，请将 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 跟踪列添加到模型。</span><span class="sxs-lookup"><span data-stu-id="67288-630">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="67288-631">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="67288-631">`rowversion` :</span></span>

* <span data-ttu-id="67288-632">是 SQL Server 特定的。</span><span class="sxs-lookup"><span data-stu-id="67288-632">Is SQL Server specific.</span></span> <span data-ttu-id="67288-633">其他数据库可能无法提供类似功能。</span><span class="sxs-lookup"><span data-stu-id="67288-633">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="67288-634">用于确定从数据库提取实体后未更改实体。</span><span class="sxs-lookup"><span data-stu-id="67288-634">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="67288-635">数据库生成 `rowversion` 序号，该数字随着每次行的更新递增。</span><span class="sxs-lookup"><span data-stu-id="67288-635">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="67288-636">在 `Update` 或 `Delete` 命令中，`Where` 子句包括 `rowversion` 的提取值。</span><span class="sxs-lookup"><span data-stu-id="67288-636">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="67288-637">如果要更新的行已更改：</span><span class="sxs-lookup"><span data-stu-id="67288-637">If the row being updated has changed:</span></span>

* <span data-ttu-id="67288-638">`rowversion` 不匹配提取值。</span><span class="sxs-lookup"><span data-stu-id="67288-638">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="67288-639">`Update` 或 `Delete` 命令不能找到行，因为 `Where` 子句包含提取的 `rowversion`。</span><span class="sxs-lookup"><span data-stu-id="67288-639">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="67288-640">引发一个 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-640">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="67288-641">在 EF Core 中，如果未通过 `Update` 或 `Delete` 命令更新行，则引发并发异常。</span><span class="sxs-lookup"><span data-stu-id="67288-641">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="67288-642">向 Department 实体添加跟踪属性</span><span class="sxs-lookup"><span data-stu-id="67288-642">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="67288-643">在 Models/Department.cs 中，添加名为 RowVersion 的跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="67288-643">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="67288-644">[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 特性指定此列包含在 `Update` 和 `Delete` 命令的 `Where` 子句中。</span><span class="sxs-lookup"><span data-stu-id="67288-644">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="67288-645">该特性称为 `Timestamp`，因为之前版本的 SQL Server 在 SQL `rowversion` 类型将其替换之前使用 SQL `timestamp` 数据类型。</span><span class="sxs-lookup"><span data-stu-id="67288-645">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="67288-646">Fluent API 还可指定跟踪属性：</span><span class="sxs-lookup"><span data-stu-id="67288-646">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="67288-647">以下代码显示更新 Department 名称时由 EF Core 生成的部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="67288-647">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="67288-648">前面突出显示的代码显示包含 `RowVersion` 的 `WHERE` 子句。</span><span class="sxs-lookup"><span data-stu-id="67288-648">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="67288-649">如果数据库 `RowVersion` 不等于 `RowVersion` 参数（`@p2`），则不更新行。</span><span class="sxs-lookup"><span data-stu-id="67288-649">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="67288-650">以下突出显示的代码显示验证更新哪一行的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="67288-650">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="67288-651">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 返回受上一语句影响的行数。</span><span class="sxs-lookup"><span data-stu-id="67288-651">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="67288-652">在没有行更新的情况下，EF Core 引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="67288-652">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="67288-653">在 Visual Studio 的输出窗口中可看见 EF Core 生成的 T-SQL。</span><span class="sxs-lookup"><span data-stu-id="67288-653">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="67288-654">更新数据库</span><span class="sxs-lookup"><span data-stu-id="67288-654">Update the DB</span></span>

<span data-ttu-id="67288-655">添加 `RowVersion` 属性可更改数据库模型，这需要迁移。</span><span class="sxs-lookup"><span data-stu-id="67288-655">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="67288-656">生成项目。</span><span class="sxs-lookup"><span data-stu-id="67288-656">Build the project.</span></span> <span data-ttu-id="67288-657">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="67288-657">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="67288-658">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="67288-658">The preceding commands:</span></span>

* <span data-ttu-id="67288-659">添加 Migrations/{time stamp}_RowVersion.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="67288-659">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="67288-660">更新 Migrations/SchoolContextModelSnapshot.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="67288-660">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="67288-661">此次更新将以下突出显示的代码添加到 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="67288-661">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="67288-662">运行迁移以更新数据库。</span><span class="sxs-lookup"><span data-stu-id="67288-662">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="67288-663">构架院系模型</span><span class="sxs-lookup"><span data-stu-id="67288-663">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67288-664">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67288-664">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="67288-665">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明操作，并对模型类使用 `Department`。</span><span class="sxs-lookup"><span data-stu-id="67288-665">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="67288-666">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="67288-666">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="67288-667">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="67288-667">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="67288-668">上述命令为 `Department` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="67288-668">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="67288-669">在 Visual Studio 中打开项目。</span><span class="sxs-lookup"><span data-stu-id="67288-669">Open the project in Visual Studio.</span></span>

<span data-ttu-id="67288-670">生成项目。</span><span class="sxs-lookup"><span data-stu-id="67288-670">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="67288-671">更新院系索引页</span><span class="sxs-lookup"><span data-stu-id="67288-671">Update the Departments Index page</span></span>

<span data-ttu-id="67288-672">基架引擎为索引页创建 `RowVersion` 列，但不应显示该字段。</span><span class="sxs-lookup"><span data-stu-id="67288-672">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="67288-673">本教程中显示 `RowVersion` 的最后一个字节，以帮助理解并发。</span><span class="sxs-lookup"><span data-stu-id="67288-673">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="67288-674">不能保证最后一个字节是唯一的。</span><span class="sxs-lookup"><span data-stu-id="67288-674">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="67288-675">实际应用不会显示 `RowVersion` 或 `RowVersion` 的最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="67288-675">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="67288-676">更新索引页：</span><span class="sxs-lookup"><span data-stu-id="67288-676">Update the Index page:</span></span>

* <span data-ttu-id="67288-677">用院系替换索引。</span><span class="sxs-lookup"><span data-stu-id="67288-677">Replace Index with Departments.</span></span>
* <span data-ttu-id="67288-678">将包含 `RowVersion` 的标记替换为 `RowVersion` 的最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="67288-678">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="67288-679">将 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="67288-679">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="67288-680">以下标记显示更新后的页面：</span><span class="sxs-lookup"><span data-stu-id="67288-680">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="67288-681">更新编辑页模型</span><span class="sxs-lookup"><span data-stu-id="67288-681">Update the Edit page model</span></span>

<span data-ttu-id="67288-682">使用以下代码更新 Pages\Departments\Edit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="67288-682">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="67288-683">要检测并发问题，请使用来自所提取实体的 `rowVersion` 值更新 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)。</span><span class="sxs-lookup"><span data-stu-id="67288-683">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="67288-684">EF Core 使用包含原始 `RowVersion` 值的 WHERE 子句生成 SQL UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="67288-684">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="67288-685">如果没有行受到 UPDATE 命令影响（没有行具有原始 `RowVersion` 值），将引发 `DbUpdateConcurrencyException` 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-685">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="67288-686">在前面的代码中，`Department.RowVersion` 为实体提取后的值。</span><span class="sxs-lookup"><span data-stu-id="67288-686">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="67288-687">使用此方法调用 `FirstOrDefaultAsync` 时，`OriginalValue` 为数据库中的值。</span><span class="sxs-lookup"><span data-stu-id="67288-687">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="67288-688">以下代码获取客户端值（向此方法发布的值）和数据库值：</span><span class="sxs-lookup"><span data-stu-id="67288-688">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="67288-689">以下代码为每列添加自定义错误消息，这些列中的数据库值与发布到 `OnPostAsync` 的值不同：</span><span class="sxs-lookup"><span data-stu-id="67288-689">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="67288-690">以下突出显示的代码将 `RowVersion` 值设置为从数据库检索的新值。</span><span class="sxs-lookup"><span data-stu-id="67288-690">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="67288-691">用户下次单击“保存”时，将仅捕获最后一次显示编辑页后发生的并发错误。</span><span class="sxs-lookup"><span data-stu-id="67288-691">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="67288-692">`ModelState` 具有旧的 `RowVersion` 值，因此需使用 `ModelState.Remove` 语句。</span><span class="sxs-lookup"><span data-stu-id="67288-692">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="67288-693">在 Razor 页面中，当两者都存在时，字段的 `ModelState` 值优于模型属性值。</span><span class="sxs-lookup"><span data-stu-id="67288-693">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="67288-694">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="67288-694">Update the Edit page</span></span>

<span data-ttu-id="67288-695">使用以下标记更新 Pages/Departments/Edit.cshtml：</span><span class="sxs-lookup"><span data-stu-id="67288-695">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="67288-696">前面的标记：</span><span class="sxs-lookup"><span data-stu-id="67288-696">The preceding markup:</span></span>

* <span data-ttu-id="67288-697">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="67288-697">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="67288-698">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-698">Adds a hidden row version.</span></span> <span data-ttu-id="67288-699">必须添加 `RowVersion`，以便回发绑定值。</span><span class="sxs-lookup"><span data-stu-id="67288-699">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="67288-700">显示 `RowVersion` 的最后一个字节以进行调试。</span><span class="sxs-lookup"><span data-stu-id="67288-700">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="67288-701">将 `ViewData` 替换为强类型 `InstructorNameSL`。</span><span class="sxs-lookup"><span data-stu-id="67288-701">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="67288-702">使用编辑页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-702">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="67288-703">在英语系打开编辑的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="67288-703">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="67288-704">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="67288-704">Run the app and select Departments.</span></span>
* <span data-ttu-id="67288-705">右键单击英语系的“编辑”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="67288-705">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="67288-706">在第一个选项卡中，单击英语系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="67288-706">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="67288-707">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="67288-707">The two browser tabs display the same information.</span></span>

<span data-ttu-id="67288-708">在第一个浏览器选项卡中更改名称，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-708">Change the name in the first browser tab and click **Save**.</span></span>

![更改后的“院系编辑”页 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="67288-710">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="67288-710">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="67288-711">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="67288-711">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="67288-712">在第二个浏览器选项卡中更改不同字段。</span><span class="sxs-lookup"><span data-stu-id="67288-712">Change a different field in the second browser tab.</span></span>

![更改后的“院系编辑”页 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="67288-714">单击“保存” 。</span><span class="sxs-lookup"><span data-stu-id="67288-714">Click **Save**.</span></span> <span data-ttu-id="67288-715">可看见所有不匹配数据库值的字段的错误消息：</span><span class="sxs-lookup"><span data-stu-id="67288-715">You see error messages for all fields that don't match the DB values:</span></span>

![“院系编辑”页错误消息 1](concurrency/_static/edit-error.png)

<span data-ttu-id="67288-717">此浏览器窗口将不会更改名称字段。</span><span class="sxs-lookup"><span data-stu-id="67288-717">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="67288-718">将当前值（语言）复制并粘贴到名称字段。</span><span class="sxs-lookup"><span data-stu-id="67288-718">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="67288-719">退出选项卡。客户端验证将删除错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-719">Tab out. Client-side validation removes the error message.</span></span>

![“院系编辑”页错误消息 2](concurrency/_static/cv.png)

<span data-ttu-id="67288-721">再次单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-721">Click **Save** again.</span></span> <span data-ttu-id="67288-722">保存在第二个浏览器选项卡中输入的值。</span><span class="sxs-lookup"><span data-stu-id="67288-722">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="67288-723">在索引页中可以看到保存的值。</span><span class="sxs-lookup"><span data-stu-id="67288-723">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="67288-724">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="67288-724">Update the Delete page</span></span>

<span data-ttu-id="67288-725">使用以下代码更新“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="67288-725">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="67288-726">删除页检测提取实体并更改时的并发冲突。</span><span class="sxs-lookup"><span data-stu-id="67288-726">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="67288-727">提取实体后，`Department.RowVersion` 为行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-727">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="67288-728">EF Core 创建 SQL DELETE 命令时，它包括具有 `RowVersion` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="67288-728">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="67288-729">如果 SQL DELETE 命令导致零行受影响：</span><span class="sxs-lookup"><span data-stu-id="67288-729">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="67288-730">SQL DELETE 命令中的 `RowVersion` 与数据库中的 `RowVersion` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="67288-730">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="67288-731">引发 DbUpdateConcurrencyException 异常。</span><span class="sxs-lookup"><span data-stu-id="67288-731">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="67288-732">使用 `concurrencyError` 调用 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="67288-732">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="67288-733">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="67288-733">Update the Delete page</span></span>

<span data-ttu-id="67288-734">使用以下代码更新 Pages/Departments/Delete.cshtml：</span><span class="sxs-lookup"><span data-stu-id="67288-734">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="67288-735">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="67288-735">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="67288-736">将 `page` 指令从 `@page` 更新为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="67288-736">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="67288-737">添加错误消息。</span><span class="sxs-lookup"><span data-stu-id="67288-737">Adds an error message.</span></span>
* <span data-ttu-id="67288-738">将“管理员”字段中的 FirstMidName 替换为 FullName。</span><span class="sxs-lookup"><span data-stu-id="67288-738">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="67288-739">更改 `RowVersion` 以显示最后一个字节。</span><span class="sxs-lookup"><span data-stu-id="67288-739">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="67288-740">添加隐藏的行版本。</span><span class="sxs-lookup"><span data-stu-id="67288-740">Adds a hidden row version.</span></span> <span data-ttu-id="67288-741">必须添加 `RowVersion`，以便回发绑定值。</span><span class="sxs-lookup"><span data-stu-id="67288-741">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="67288-742">使用删除页测试并发冲突</span><span class="sxs-lookup"><span data-stu-id="67288-742">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="67288-743">创建测试系。</span><span class="sxs-lookup"><span data-stu-id="67288-743">Create a test department.</span></span>

<span data-ttu-id="67288-744">在测试系打开删除的两个浏览器实例：</span><span class="sxs-lookup"><span data-stu-id="67288-744">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="67288-745">运行应用，然后选择“院系”。</span><span class="sxs-lookup"><span data-stu-id="67288-745">Run the app and select Departments.</span></span>
* <span data-ttu-id="67288-746">右键单击测试系的“删除”超链接，然后选择“在新选项卡中打开” 。</span><span class="sxs-lookup"><span data-stu-id="67288-746">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="67288-747">单击测试系的“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="67288-747">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="67288-748">两个浏览器选项卡显示相同信息。</span><span class="sxs-lookup"><span data-stu-id="67288-748">The two browser tabs display the same information.</span></span>

<span data-ttu-id="67288-749">在第一个浏览器选项卡中更改预算，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="67288-749">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="67288-750">浏览器显示更改值并更新 rowVersion 标记后的索引页。</span><span class="sxs-lookup"><span data-stu-id="67288-750">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="67288-751">请注意更新后的 rowVersion 标记，它在其他选项卡的第二回发中显示。</span><span class="sxs-lookup"><span data-stu-id="67288-751">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="67288-752">从第二个选项卡中删除测试部门。并发错误显示来自数据库的当前值。</span><span class="sxs-lookup"><span data-stu-id="67288-752">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="67288-753">单击“删除”删除实体，除非 `RowVersion` 已更新。</span><span class="sxs-lookup"><span data-stu-id="67288-753">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="67288-754">请参阅[继承](xref:data/ef-mvc/inheritance)了解如何继承数据模型。</span><span class="sxs-lookup"><span data-stu-id="67288-754">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="67288-755">其他资源</span><span class="sxs-lookup"><span data-stu-id="67288-755">Additional resources</span></span>

* [<span data-ttu-id="67288-756">EF Core 中的并发令牌</span><span class="sxs-lookup"><span data-stu-id="67288-756">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="67288-757">EF Core 中的并发处理</span><span class="sxs-lookup"><span data-stu-id="67288-757">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="67288-758">本教程的 YouTube 版本（处理并发冲突）</span><span class="sxs-lookup"><span data-stu-id="67288-758">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="67288-759">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="67288-759">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="67288-760">本教程的 YouTube 版本（第 3 部分）</span><span class="sxs-lookup"><span data-stu-id="67288-760">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="67288-761">上一篇</span><span class="sxs-lookup"><span data-stu-id="67288-761">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end
