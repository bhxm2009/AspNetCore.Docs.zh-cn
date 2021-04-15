---
title: 第 4 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 迁移
author: rick-anderson
description: Razor 页面和实体框架教程系列第 4 部分。
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
uid: data/ef-rp/migrations
ms.openlocfilehash: 7f1741e06a6e43fd80176113fbe4f249f6eeaa2b
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957493"
---
# <a name="part-4-razor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="48d54-103">第 4 部分，ASP.NET Core 中的 Razor 页面和 EF Core 迁移</span><span class="sxs-lookup"><span data-stu-id="48d54-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="48d54-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48d54-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48d54-105">本教程介绍管理数据模型更改的 EF Core 迁移功能。</span><span class="sxs-lookup"><span data-stu-id="48d54-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="48d54-106">开发新应用时，数据模型会频繁更改。</span><span class="sxs-lookup"><span data-stu-id="48d54-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="48d54-107">每当模型发生更改时，都无法与数据库进行同步。</span><span class="sxs-lookup"><span data-stu-id="48d54-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="48d54-108">本教程从配置实体框架以创建数据库（如果不存在）开始。</span><span class="sxs-lookup"><span data-stu-id="48d54-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="48d54-109">数据模型每次发生更改时，必须删除该数据库。</span><span class="sxs-lookup"><span data-stu-id="48d54-109">Each time the data model changes, the database needs to be dropped.</span></span> <span data-ttu-id="48d54-110">下次应用运行时，对 `EnsureCreated` 的调用将重新创建数据库以匹配新的数据模型。</span><span class="sxs-lookup"><span data-stu-id="48d54-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="48d54-111">然后 `DbInitializer` 类将运行以设定新数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="48d54-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="48d54-112">这种使 DB 与数据模型保持同步的方法非常适用，但需要将应用部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="48d54-112">This approach to keeping the DB in sync with the data model works well until the app needs to be deployed to production.</span></span> <span data-ttu-id="48d54-113">当应用在生产环境中运行时，应用通常会存储需要保留的数据。</span><span class="sxs-lookup"><span data-stu-id="48d54-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="48d54-114">每当发生更改（例如添加新列）时，应用都无法在具有测试 DB 的环境下启动。</span><span class="sxs-lookup"><span data-stu-id="48d54-114">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="48d54-115">EF Core 迁移功能可通过使 EF Core 更新 DB 架构（而不是创建新数据库）来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="48d54-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new database.</span></span>

<span data-ttu-id="48d54-116">数据模型更改时，迁移不会删除并重新创建数据库，而是更新架构并保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="48d54-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="48d54-117">删除数据库</span><span class="sxs-lookup"><span data-stu-id="48d54-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48d54-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48d54-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48d54-119">使用 SQL Server 对象资源管理器 (SSOX) 删除数据库或在包管理器控制台 (PMC) 中运行以下命令 ：</span><span class="sxs-lookup"><span data-stu-id="48d54-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="48d54-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48d54-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="48d54-121">在命令提示符下运行以下命令以安装 EF CLI：</span><span class="sxs-lookup"><span data-stu-id="48d54-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="48d54-122">在命令提示符下，导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="48d54-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="48d54-123">项目文件夹包含 ContosoUniversity.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="48d54-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="48d54-124">删除 CU.db 文件，或运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="48d54-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="48d54-125">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="48d54-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48d54-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48d54-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48d54-127">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="48d54-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
 
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="48d54-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48d54-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48d54-129">确保命令提示符位于项目文件夹中，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="48d54-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
 
```

---

### <a name="remove-ensurecreated"></a><span data-ttu-id="48d54-130">删除 EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="48d54-130">Remove EnsureCreated</span></span>

<span data-ttu-id="48d54-131">本系列教程从使用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 开始。</span><span class="sxs-lookup"><span data-stu-id="48d54-131">This tutorial series started by using [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) .</span></span> <span data-ttu-id="48d54-132">`EnsureCreated` 不创建迁移历史记录表，因此不能与迁移一起使用。</span><span class="sxs-lookup"><span data-stu-id="48d54-132">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="48d54-133">它专门用于在频繁删除并重新创建 DB 的情况下进行测试或快速制作原型。</span><span class="sxs-lookup"><span data-stu-id="48d54-133">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="48d54-134">从这个角度来看，教程将使用迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-134">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="48d54-135">在 Program.cs 中，删除以下行：</span><span class="sxs-lookup"><span data-stu-id="48d54-135">In *Program.cs*, delete the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="48d54-136">运行应用并验证数据库是否已设定种子。</span><span class="sxs-lookup"><span data-stu-id="48d54-136">Run the app and verify that the database is seeded.</span></span>

## <a name="up-and-down-methods"></a><span data-ttu-id="48d54-137">Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="48d54-137">Up and Down methods</span></span>

<span data-ttu-id="48d54-138">EF Core `migrations add` 命令已生成用于创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="48d54-138">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="48d54-139">此迁移代码位于 Migrations\<timestamp>_InitialCreate.cs 文件中。</span><span class="sxs-lookup"><span data-stu-id="48d54-139">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="48d54-140">`InitialCreate` 类的 `Up` 方法创建与数据模型实体集对应的数据库表。</span><span class="sxs-lookup"><span data-stu-id="48d54-140">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="48d54-141">`Down` 方法删除这些表，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="48d54-141">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="48d54-142">前面的代码适用于初始迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-142">The preceding code is for the initial migration.</span></span> <span data-ttu-id="48d54-143">代码：</span><span class="sxs-lookup"><span data-stu-id="48d54-143">The code:</span></span>

* <span data-ttu-id="48d54-144">由 `migrations add InitialCreate` 命令生成。</span><span class="sxs-lookup"><span data-stu-id="48d54-144">Was generated by the `migrations add InitialCreate` command.</span></span>
* <span data-ttu-id="48d54-145">由 `database update` 命令执行。</span><span class="sxs-lookup"><span data-stu-id="48d54-145">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="48d54-146">为数据库上下文类指定的数据模型创建数据库。</span><span class="sxs-lookup"><span data-stu-id="48d54-146">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="48d54-147">迁移名称参数（本示例中为 `InitialCreate`）用于指定文件名。</span><span class="sxs-lookup"><span data-stu-id="48d54-147">The migration name parameter (`InitialCreate` in the example) is used for the file name.</span></span> <span data-ttu-id="48d54-148">迁移名称可以是任何有效的文件名。</span><span class="sxs-lookup"><span data-stu-id="48d54-148">The migration name can be any valid file name.</span></span> <span data-ttu-id="48d54-149">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="48d54-149">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="48d54-150">例如，添加了系表的迁移可称为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="48d54-150">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="48d54-151">迁移历史记录表</span><span class="sxs-lookup"><span data-stu-id="48d54-151">The migrations history table</span></span>

* <span data-ttu-id="48d54-152">使用 SSOX 或 SQLite 工具检查数据库。</span><span class="sxs-lookup"><span data-stu-id="48d54-152">Use SSOX or SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="48d54-153">请注意，增加了 `__EFMigrationsHistory` 表。</span><span class="sxs-lookup"><span data-stu-id="48d54-153">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="48d54-154">`__EFMigrationsHistory` 表跟踪已应用到数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-154">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="48d54-155">查看 `__EFMigrationsHistory` 表中的数据。</span><span class="sxs-lookup"><span data-stu-id="48d54-155">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="48d54-156">它显示第一次迁移的行。</span><span class="sxs-lookup"><span data-stu-id="48d54-156">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="48d54-157">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="48d54-157">The data model snapshot</span></span>

<span data-ttu-id="48d54-158">迁移会在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据模型的快照 。</span><span class="sxs-lookup"><span data-stu-id="48d54-158">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="48d54-159">添加迁移时，EF 会通过将当前数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="48d54-159">When add a migration is added, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="48d54-160">由于快照文件跟踪数据模型的状态，因此不能通过删除 `<timestamp>_<migrationname>.cs` 文件来删除迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-160">Because the snapshot file tracks the state of the data model, a migration cannot be deleted by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="48d54-161">若要返回最近的迁移，必须使用 [`migrations remove`](/ef/core/managing-schemas/migrations/managing#remove-a-migration) 命令。</span><span class="sxs-lookup"><span data-stu-id="48d54-161">To back out the most recent migration, use the [`migrations remove`](/ef/core/managing-schemas/migrations/managing#remove-a-migration) command.</span></span> <span data-ttu-id="48d54-162">`migrations remove` 删除迁移，并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="48d54-162">`migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="48d54-163">有关详细信息，请参阅 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="48d54-163">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

<span data-ttu-id="48d54-164">若要删除所有迁移，请参阅[重置所有迁移](/ef/core/miscellaneous/cli/dotnet#resetting-all-migrations)</span><span class="sxs-lookup"><span data-stu-id="48d54-164">See [Resetting all migrations ](/ef/core/miscellaneous/cli/dotnet#resetting-all-migrations) to remove all migrations</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="48d54-165">在生产环境中应用迁移</span><span class="sxs-lookup"><span data-stu-id="48d54-165">Applying migrations in production</span></span>

<span data-ttu-id="48d54-166">不建议生产应用在应用程序启动时调用 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)。</span><span class="sxs-lookup"><span data-stu-id="48d54-166">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="48d54-167">`Migrate` 不应从部署到服务器场的应用中调用。</span><span class="sxs-lookup"><span data-stu-id="48d54-167">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="48d54-168">如果应用横向扩展到多个服务器实例，则很难确保多个服务器不会发生数据库架构更新，或者这些更新不会与读/写访问冲突。</span><span class="sxs-lookup"><span data-stu-id="48d54-168">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="48d54-169">应在部署过程中以受控的方式执行数据库迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-169">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="48d54-170">生产数据库迁移方法包括：</span><span class="sxs-lookup"><span data-stu-id="48d54-170">Production database migration approaches include:</span></span>

* <span data-ttu-id="48d54-171">使用迁移创建 SQL 脚本，并在部署过程中使用 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="48d54-171">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="48d54-172">在受控的环境中运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="48d54-172">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="48d54-173">疑难解答</span><span class="sxs-lookup"><span data-stu-id="48d54-173">Troubleshooting</span></span>

<span data-ttu-id="48d54-174">如果应用使用 SQL Server LocalDB 并显示以下异常：</span><span class="sxs-lookup"><span data-stu-id="48d54-174">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="48d54-175">解决方案可能是在命令提示符下运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="48d54-175">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="48d54-176">其他资源</span><span class="sxs-lookup"><span data-stu-id="48d54-176">Additional resources</span></span>

* <span data-ttu-id="48d54-177">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="48d54-177">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="48d54-178">dotnet ef 迁移 CLI 命令</span><span class="sxs-lookup"><span data-stu-id="48d54-178">dotnet ef migrations CLI commands</span></span>](/ef/core/miscellaneous/cli/dotnet)
* [<span data-ttu-id="48d54-179">包管理器控制台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="48d54-179">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="48d54-180">后续步骤</span><span class="sxs-lookup"><span data-stu-id="48d54-180">Next steps</span></span>

<span data-ttu-id="48d54-181">下一个教程将生成数据模型，并添加实体属性和新实体。</span><span class="sxs-lookup"><span data-stu-id="48d54-181">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="48d54-182">[上一个教程](xref:data/ef-rp/sort-filter-page)
> [下一个教程](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="48d54-182">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48d54-183">本教程使用 EF Core 迁移功能管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="48d54-183">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="48d54-184">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="48d54-184">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="48d54-185">开发新应用时，数据模型会频繁更改。</span><span class="sxs-lookup"><span data-stu-id="48d54-185">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="48d54-186">每当模型发生更改时，都无法与数据库进行同步。</span><span class="sxs-lookup"><span data-stu-id="48d54-186">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="48d54-187">本教程首先配置 Entity Framework 以创建数据库（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="48d54-187">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="48d54-188">每当数据模型发生更改时：</span><span class="sxs-lookup"><span data-stu-id="48d54-188">Each time the data model changes:</span></span>

* <span data-ttu-id="48d54-189">DB 都会被删除。</span><span class="sxs-lookup"><span data-stu-id="48d54-189">The DB is dropped.</span></span>
* <span data-ttu-id="48d54-190">EF 都会创建一个新数据库来匹配该模型。</span><span class="sxs-lookup"><span data-stu-id="48d54-190">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="48d54-191">应用使用测试数据为 DB 设定种子。</span><span class="sxs-lookup"><span data-stu-id="48d54-191">The app seeds the DB with test data.</span></span>

<span data-ttu-id="48d54-192">这种使 DB 与数据模型保持同步的方法非常适用，但需要将应用部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="48d54-192">This approach to keeping the DB in sync with the data model works well until the app needs to be deployed to production.</span></span> <span data-ttu-id="48d54-193">当应用在生产环境中运行时，应用通常会存储需要保留的数据。</span><span class="sxs-lookup"><span data-stu-id="48d54-193">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="48d54-194">每当发生更改（例如添加新列）时，应用都无法在具有测试 DB 的环境下启动。</span><span class="sxs-lookup"><span data-stu-id="48d54-194">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="48d54-195">EF Core 迁移功能可通过使 EF Core 更新 DB 架构而不是创建新 DB 来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="48d54-195">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="48d54-196">数据模型发生更改时，迁移将更新架构并保留现有数据，而无需删除或重新创建 DB。</span><span class="sxs-lookup"><span data-stu-id="48d54-196">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="48d54-197">删除数据库</span><span class="sxs-lookup"><span data-stu-id="48d54-197">Drop the database</span></span>

<span data-ttu-id="48d54-198">使用 SQL Server 对象资源管理器 (SSOX) 或 `database drop` 命令：</span><span class="sxs-lookup"><span data-stu-id="48d54-198">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48d54-199">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48d54-199">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48d54-200">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="48d54-200">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="48d54-201">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="48d54-201">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48d54-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48d54-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="48d54-203">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="48d54-203">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="48d54-204">项目文件夹包含 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="48d54-204">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="48d54-205">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="48d54-205">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="48d54-206">创建初始迁移并更新 DB</span><span class="sxs-lookup"><span data-stu-id="48d54-206">Create an initial migration and update the DB</span></span>

<span data-ttu-id="48d54-207">生成项目并创建第一个迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-207">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48d54-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48d54-208">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="48d54-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48d54-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="48d54-210">了解 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="48d54-210">Examine the Up and Down methods</span></span>

<span data-ttu-id="48d54-211">EF Core `migrations add` 命令已生成用于创建 DB 的代码。</span><span class="sxs-lookup"><span data-stu-id="48d54-211">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="48d54-212">此迁移代码位于 Migrations\<timestamp>_InitialCreate.cs 文件中。</span><span class="sxs-lookup"><span data-stu-id="48d54-212">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="48d54-213">`InitialCreate` 类的 `Up` 的方法创建与数据模型实体集相对应的 DB 表。</span><span class="sxs-lookup"><span data-stu-id="48d54-213">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="48d54-214">`Down` 方法删除这些表，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="48d54-214">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="48d54-215">迁移调用 `Up` 方法为迁移实现数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="48d54-215">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="48d54-216">输入用于回退更新的命令时，迁移调用 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="48d54-216">When a command is entered to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="48d54-217">前面的代码适用于初始迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-217">The preceding code is for the initial migration.</span></span> <span data-ttu-id="48d54-218">该代码是运行 `migrations add InitialCreate` 命令时创建的。</span><span class="sxs-lookup"><span data-stu-id="48d54-218">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="48d54-219">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名。</span><span class="sxs-lookup"><span data-stu-id="48d54-219">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="48d54-220">迁移名称可以是任何有效的文件名。</span><span class="sxs-lookup"><span data-stu-id="48d54-220">The migration name can be any valid file name.</span></span> <span data-ttu-id="48d54-221">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="48d54-221">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="48d54-222">例如，添加了系表的迁移可称为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="48d54-222">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="48d54-223">如果创建了初始迁移并且存在 DB：</span><span class="sxs-lookup"><span data-stu-id="48d54-223">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="48d54-224">会生成 DB 创建代码。</span><span class="sxs-lookup"><span data-stu-id="48d54-224">The DB creation code is generated.</span></span>
* <span data-ttu-id="48d54-225">DB 创建代码不需要运行，因为 DB 已与数据模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="48d54-225">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="48d54-226">即使 DB 创建代码运行也不会做出任何更改，因为 DB 已与数据模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="48d54-226">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="48d54-227">如果将应用部署到新环境，则必须运行 DB 创建代码才能创建 DB。</span><span class="sxs-lookup"><span data-stu-id="48d54-227">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="48d54-228">先前删除了 DB，因此已不存在，所以迁移会创建新的 DB。</span><span class="sxs-lookup"><span data-stu-id="48d54-228">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="48d54-229">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="48d54-229">The data model snapshot</span></span>

<span data-ttu-id="48d54-230">迁移在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据库架构的快照 。</span><span class="sxs-lookup"><span data-stu-id="48d54-230">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="48d54-231">添加迁移时，EF 会通过将数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="48d54-231">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="48d54-232">若要删除迁移，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="48d54-232">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48d54-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48d54-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48d54-234">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="48d54-234">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48d54-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48d54-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="48d54-236">有关详细信息，请参阅 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="48d54-236">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="48d54-237">删除迁移命令会删除迁移并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="48d54-237">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="48d54-238">删除 EnsureCreated 并测试应用</span><span class="sxs-lookup"><span data-stu-id="48d54-238">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="48d54-239">早期开发使用了 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="48d54-239">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="48d54-240">本教程将使用迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-240">In this tutorial, migrations are used.</span></span> <span data-ttu-id="48d54-241">`EnsureCreated` 具有以下限制：</span><span class="sxs-lookup"><span data-stu-id="48d54-241">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="48d54-242">绕过迁移并创建 DB 和架构。</span><span class="sxs-lookup"><span data-stu-id="48d54-242">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="48d54-243">不会创建迁移表。</span><span class="sxs-lookup"><span data-stu-id="48d54-243">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="48d54-244">不能与迁移一起使用。</span><span class="sxs-lookup"><span data-stu-id="48d54-244">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="48d54-245">专门用于在频繁删除并重新创建 DB 的情况下进行测试或快速制作原型。</span><span class="sxs-lookup"><span data-stu-id="48d54-245">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="48d54-246">删除 `EnsureCreated`：</span><span class="sxs-lookup"><span data-stu-id="48d54-246">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="48d54-247">运行应用并验证 DB 设定为种子。</span><span class="sxs-lookup"><span data-stu-id="48d54-247">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="48d54-248">检查数据库</span><span class="sxs-lookup"><span data-stu-id="48d54-248">Inspect the database</span></span>

<span data-ttu-id="48d54-249">使用 SQL Server 对象资源管理器检查 DB。</span><span class="sxs-lookup"><span data-stu-id="48d54-249">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="48d54-250">请注意，增加了 `__EFMigrationsHistory` 表。</span><span class="sxs-lookup"><span data-stu-id="48d54-250">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="48d54-251">`__EFMigrationsHistory` 表跟踪已应用到 DB 的迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-251">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="48d54-252">查看 `__EFMigrationsHistory` 表中的数据，其中显示对应初始迁移的一行数据。</span><span class="sxs-lookup"><span data-stu-id="48d54-252">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="48d54-253">上面的 CLI 输出示例中最后部分的日志显示了创建此行的 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="48d54-253">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="48d54-254">运行应用并验证一切正常运行。</span><span class="sxs-lookup"><span data-stu-id="48d54-254">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="48d54-255">在生产环境中应用迁移</span><span class="sxs-lookup"><span data-stu-id="48d54-255">Applying migrations in production</span></span>

<span data-ttu-id="48d54-256">不建议生产应用在应用程序启动时调用 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)。</span><span class="sxs-lookup"><span data-stu-id="48d54-256">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="48d54-257">不应从服务器场中的应用调用 `Migrate`。</span><span class="sxs-lookup"><span data-stu-id="48d54-257">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="48d54-258">例如，已将应用在云中部署为横向扩展（运行应用的多个示例）的情况。</span><span class="sxs-lookup"><span data-stu-id="48d54-258">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="48d54-259">应在部署过程中以受控的方式执行数据库迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-259">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="48d54-260">生产数据库迁移方法包括：</span><span class="sxs-lookup"><span data-stu-id="48d54-260">Production database migration approaches include:</span></span>

* <span data-ttu-id="48d54-261">使用迁移创建 SQL 脚本，并在部署过程中使用 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="48d54-261">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="48d54-262">在受控的环境中运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="48d54-262">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="48d54-263">EF Core 使用 `__MigrationsHistory` 表查看是否需要运行任何迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-263">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="48d54-264">如果 DB 已是最新，则无需运行迁移。</span><span class="sxs-lookup"><span data-stu-id="48d54-264">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="48d54-265">疑难解答</span><span class="sxs-lookup"><span data-stu-id="48d54-265">Troubleshooting</span></span>

<span data-ttu-id="48d54-266">下载[已完成应用](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)。</span><span class="sxs-lookup"><span data-stu-id="48d54-266">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="48d54-267">应用会生成以下异常：</span><span class="sxs-lookup"><span data-stu-id="48d54-267">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="48d54-268">解决方案：运行 `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="48d54-268">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="48d54-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="48d54-269">Additional resources</span></span>

* [<span data-ttu-id="48d54-270">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="48d54-270">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="48d54-271">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="48d54-271">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="48d54-272">包管理器控制台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="48d54-272">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="48d54-273">[上一页](xref:data/ef-rp/sort-filter-page)
> [下一页](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="48d54-273">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end
