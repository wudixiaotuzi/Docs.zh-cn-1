---
title: "ASP.NET 核心 MVC 与 EF 核心-读取相关的数据-10 6"
author: tdykstra
description: "在本教程中将读取并显示相关的数据-即，实体框架将加载到导航属性的数据。"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 1321cb00a432669b4a97ad20063b6cf9ea75f24c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="reading-related-data---ef-core-with-aspnet-core-mvc-tutorial-6-of-10"></a><span data-ttu-id="66012-103">读取与相关的数据的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (6 的 10)</span><span class="sxs-lookup"><span data-stu-id="66012-103">Reading related data - EF Core with ASP.NET Core MVC tutorial (6 of 10)</span></span>

<span data-ttu-id="66012-104">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66012-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="66012-105">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="66012-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="66012-106">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="66012-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="66012-107">在前面的教程，你将完成 School 数据模型。</span><span class="sxs-lookup"><span data-stu-id="66012-107">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="66012-108">在本教程中，将读取并显示相关的数据-即，实体框架将加载到导航属性的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-108">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="66012-109">下图显示了您将使用的页。</span><span class="sxs-lookup"><span data-stu-id="66012-109">The following illustrations show the pages that you'll work with.</span></span>

![课程索引页](read-related-data/_static/courses-index.png)

![教师索引页](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="66012-112">Eager、 显式，和延迟加载的相关数据</span><span class="sxs-lookup"><span data-stu-id="66012-112">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="66012-113">有以下几种对象关系映射 (ORM) 软件，就如实体框架可以将相关的数据加载到实体的导航属性：</span><span class="sxs-lookup"><span data-stu-id="66012-113">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="66012-114">预先加载。</span><span class="sxs-lookup"><span data-stu-id="66012-114">Eager loading.</span></span> <span data-ttu-id="66012-115">实体中读取时，会随之检索相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-115">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="66012-116">这通常会导致检索所有具有所需的数据的单一联接查询。</span><span class="sxs-lookup"><span data-stu-id="66012-116">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="66012-117">你通过使用实体框架核心中指定预先加载`Include`和`ThenInclude`方法。</span><span class="sxs-lookup"><span data-stu-id="66012-117">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="66012-119">你可以检索一些单独的查询中的数据并 EF"修复"的导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-119">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="66012-120">也就是说，EF 会自动将它们所属的单独检索到的实体添加以前检索到实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-120">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="66012-121">对于查询以检索相关的数据，你可以使用`Load`方法而不是返回的列表或对象，如方法`ToList`或`Single`。</span><span class="sxs-lookup"><span data-stu-id="66012-121">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![单独的查询示例](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="66012-123">显式加载。</span><span class="sxs-lookup"><span data-stu-id="66012-123">Explicit loading.</span></span> <span data-ttu-id="66012-124">当第一次读取实体时，不检索相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-124">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="66012-125">你将编写检索相关的数据的代码需要它。</span><span class="sxs-lookup"><span data-stu-id="66012-125">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="66012-126">如下所示预先加载使用单独的查询的情况下，显式加载在多个查询的结果发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="66012-126">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="66012-127">不同之处在于，使用显式加载，该代码指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-127">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="66012-128">你可以使用实体框架核心 1.1`Load`方法执行显式加载。</span><span class="sxs-lookup"><span data-stu-id="66012-128">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="66012-129">例如:</span><span class="sxs-lookup"><span data-stu-id="66012-129">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="66012-131">延迟加载。</span><span class="sxs-lookup"><span data-stu-id="66012-131">Lazy loading.</span></span> <span data-ttu-id="66012-132">当第一次读取实体时，不检索相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-132">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="66012-133">但是，第一次尝试访问的导航属性，将自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-133">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="66012-134">每次尝试首次从一个导航属性获取数据时，查询是发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="66012-134">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="66012-135">实体框架核心 1.0 不支持延迟加载。</span><span class="sxs-lookup"><span data-stu-id="66012-135">Entity Framework Core 1.0 does not support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="66012-136">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="66012-136">Performance considerations</span></span>

<span data-ttu-id="66012-137">如果你知道需要相关的数据检索的每个实体，预先加载通常提供最佳性能，因为发送到数据库的单个查询通常比用于检索每个实体的单独查询效率更高。</span><span class="sxs-lookup"><span data-stu-id="66012-137">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="66012-138">例如，假设每个部门都有十个相关的课程。</span><span class="sxs-lookup"><span data-stu-id="66012-138">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="66012-139">预先加载了相关的所有数据将导致仅单 （联接） 查询和单个往返到数据库。</span><span class="sxs-lookup"><span data-stu-id="66012-139">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="66012-140">对于每个部门的课程单独的查询中，将导致到数据库的十一个往返过程。</span><span class="sxs-lookup"><span data-stu-id="66012-140">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="66012-141">与数据库额外的往返延迟较高时尤其是对性能造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="66012-141">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="66012-142">另一方面，在某些情况下的单独查询会更加高效。</span><span class="sxs-lookup"><span data-stu-id="66012-142">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="66012-143">预先加载了一个查询中的所有相关数据可能会导致非常复杂的联接为其生成 SQL Server 无法有效地处理它。</span><span class="sxs-lookup"><span data-stu-id="66012-143">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="66012-144">或者，如果需要访问仅针对要处理的实体集的子集的实体的导航属性，当单独的查询因为预先加载了所有内容预先将检索比你需要更多的数据，可能更好地执行。</span><span class="sxs-lookup"><span data-stu-id="66012-144">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="66012-145">如果性能非常重要，则最好测试以便做出最佳选择这两种方式的性能。</span><span class="sxs-lookup"><span data-stu-id="66012-145">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page-that-displays-department-name"></a><span data-ttu-id="66012-146">创建显示部门名称的课程页</span><span class="sxs-lookup"><span data-stu-id="66012-146">Create a Courses page that displays Department name</span></span>

<span data-ttu-id="66012-147">课程实体包括一个包含过程分配给部门的部门实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-147">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="66012-148">若要显示分配的部门的名称的课程，列表中，你需要获取的 Name 属性中的部门实体从`Course.Department`导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-148">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that is in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="66012-149">创建名为课程的实体类型，使用的相同选项 CoursesController 的控制器**使用实体框架的包含视图的 MVC 控制器**你此前学生控制器中所示的基架下图：</span><span class="sxs-lookup"><span data-stu-id="66012-149">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![添加课程控制器](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="66012-151">打开*CoursesController.cs*并检查`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="66012-151">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="66012-152">自动的基架已指定为预先加载`Department`导航属性使用`Include`方法。</span><span class="sxs-lookup"><span data-stu-id="66012-152">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="66012-153">替换`Index`方法替换为以下代码使用更合适的名称为`IQueryable`返回课程实体 (`courses`而不是`schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="66012-153">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="66012-154">打开*Views/Courses/Index.cshtml*和模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="66012-154">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="66012-155">突出显示所做的更改：</span><span class="sxs-lookup"><span data-stu-id="66012-155">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="66012-156">你已对基架代码进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="66012-156">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="66012-157">从索引的标题更改为课程。</span><span class="sxs-lookup"><span data-stu-id="66012-157">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="66012-158">添加**数**显示的列`CourseID`属性值。</span><span class="sxs-lookup"><span data-stu-id="66012-158">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="66012-159">默认情况下，主键不基架，因为它们通常是向最终用户无意义。</span><span class="sxs-lookup"><span data-stu-id="66012-159">By default, primary keys aren't scaffolded because normally they are meaningless to end users.</span></span> <span data-ttu-id="66012-160">但是，在这种情况下的主键有意义，以及你想要对其进行说明。</span><span class="sxs-lookup"><span data-stu-id="66012-160">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="66012-161">更改**部门**列以显示部门名称。</span><span class="sxs-lookup"><span data-stu-id="66012-161">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="66012-162">该代码将显示`Name`加载到的部门实体属性`Department`导航属性：</span><span class="sxs-lookup"><span data-stu-id="66012-162">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="66012-163">运行应用并选择**课程**选项卡以查看使用部门名称的列表。</span><span class="sxs-lookup"><span data-stu-id="66012-163">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![课程索引页](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="66012-165">创建显示 Courses，并将注册一个教师页面</span><span class="sxs-lookup"><span data-stu-id="66012-165">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="66012-166">在此部分中，可以创建一个控制器和视图 Instructor 实体为了显示教师页：</span><span class="sxs-lookup"><span data-stu-id="66012-166">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![教师索引页](read-related-data/_static/instructors-index.png)

<span data-ttu-id="66012-168">此页读取，并通过以下方式显示相关的数据：</span><span class="sxs-lookup"><span data-stu-id="66012-168">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="66012-169">教师的列表显示来自 OfficeAssignment 实体的相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-169">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="66012-170">教师和 OfficeAssignment 实体都将位于对零或一一个关系。</span><span class="sxs-lookup"><span data-stu-id="66012-170">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="66012-171">你将为 OfficeAssignment 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="66012-171">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="66012-172">如前面所述，预先加载是通常更高效，您需要为所有检索行的主表的相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-172">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="66012-173">在这种情况下，你想要显示的所有显示教师 office 分配。</span><span class="sxs-lookup"><span data-stu-id="66012-173">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="66012-174">当用户选择一个教师时，将显示相关的课程实体。</span><span class="sxs-lookup"><span data-stu-id="66012-174">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="66012-175">教师和课程实体都将位于多对多关系。</span><span class="sxs-lookup"><span data-stu-id="66012-175">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="66012-176">你将使用预先加载的课程实体和其相关的部门实体。</span><span class="sxs-lookup"><span data-stu-id="66012-176">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="66012-177">在这种情况下，因为你只为所选教师需要课程，单独的查询可能会更有效。</span><span class="sxs-lookup"><span data-stu-id="66012-177">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="66012-178">但是，此示例演示如何使用预先加载用于本身是导航属性中的实体内的导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-178">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="66012-179">当用户选择某一课程时，显示来自注册实体集的相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-179">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="66012-180">当然，注册实体都将位于一个对多关系。</span><span class="sxs-lookup"><span data-stu-id="66012-180">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="66012-181">对于注册实体和其相关的学生实体，你将使用单独的查询。</span><span class="sxs-lookup"><span data-stu-id="66012-181">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="66012-182">创建教师索引视图的视图模型</span><span class="sxs-lookup"><span data-stu-id="66012-182">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="66012-183">教师页显示三个不同的表中的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-183">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="66012-184">因此，你将创建包括三个属性，每个保存的表中的一个数据视图模型。</span><span class="sxs-lookup"><span data-stu-id="66012-184">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="66012-185">在*SchoolViewModels*文件夹中，创建*InstructorIndexData.cs*和替换为以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="66012-185">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="66012-186">创建教师控制器和视图</span><span class="sxs-lookup"><span data-stu-id="66012-186">Create the Instructor controller and views</span></span>

<span data-ttu-id="66012-187">创建一个教师控制器具有 EF 读/写操作，如下面的插图中所示：</span><span class="sxs-lookup"><span data-stu-id="66012-187">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![添加教师控制器](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="66012-189">打开*InstructorsController.cs*和添加 using 语句 Viewmodel 命名空间：</span><span class="sxs-lookup"><span data-stu-id="66012-189">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="66012-190">Index 方法替换为以下代码以执行预先加载了相关的数据并将其置于视图模型。</span><span class="sxs-lookup"><span data-stu-id="66012-190">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="66012-191">该方法将接受可选路由数据 (`id`) 和查询字符串参数 (`courseID`) 提供的所选的教师和所选的过程的 ID 值。</span><span class="sxs-lookup"><span data-stu-id="66012-191">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="66012-192">参数提供的**选择**网页超链接。</span><span class="sxs-lookup"><span data-stu-id="66012-192">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="66012-193">代码首先通过在创建视图模型的实例并在其中放置教师的列表。</span><span class="sxs-lookup"><span data-stu-id="66012-193">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="66012-194">该代码指定为预先加载`Instructor.OfficeAssignment`和`Instructor.CourseAssignments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-194">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="66012-195">在`CourseAssignments`属性，`Course`属性是在其中已加载，和，则`Enrollments`和`Department`属性是在每个已加载，和`Enrollment`实体`Student`加载属性。</span><span class="sxs-lookup"><span data-stu-id="66012-195">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="66012-196">由于视图始终要求 OfficeAssignment 实体，它会更加高效，若要在同一查询中提取的。</span><span class="sxs-lookup"><span data-stu-id="66012-196">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="66012-197">在 web 页中，选择一个教师，仅当与某一课程比选择而无需更多时候显示页面，因此单个查询多个查询更好时，过程实体是必需的。</span><span class="sxs-lookup"><span data-stu-id="66012-197">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="66012-198">代码重复`CourseAssignments`和`Course`因为你需要从两个属性`Course`。</span><span class="sxs-lookup"><span data-stu-id="66012-198">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="66012-199">第一个字符串`ThenInclude`调用获取`CourseAssignment.Course`， `Course.Enrollments`，和`Enrollment.Student`。</span><span class="sxs-lookup"><span data-stu-id="66012-199">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="66012-200">此时，在代码中，另一个`ThenInclude`将用于导航属性的`Student`，不需要的。</span><span class="sxs-lookup"><span data-stu-id="66012-200">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="66012-201">但调用`Include`通过开头`Instructor`属性，因此你必须再次执行链，此时间指定`Course.Department`而不是`Course.Enrollments`。</span><span class="sxs-lookup"><span data-stu-id="66012-201">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="66012-202">在选择了一个教师时，将执行下面的代码。</span><span class="sxs-lookup"><span data-stu-id="66012-202">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="66012-203">所选的教师检索从教师视图模型中的列表中。</span><span class="sxs-lookup"><span data-stu-id="66012-203">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="66012-204">视图模型`Courses`属性然后加载和课程实体从该教师`CourseAssignments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-204">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="66012-205">`Where`方法将返回一个集合，但条件在此情况下传递到方法导致仅单个 Instructor 实体返回。</span><span class="sxs-lookup"><span data-stu-id="66012-205">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="66012-206">`Single`方法将集合转换为单个 Instructor 实体，使你可以访问该实体的`CourseAssignments`属性。</span><span class="sxs-lookup"><span data-stu-id="66012-206">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="66012-207">`CourseAssignments`属性包含`CourseAssignment`从中您想仅相关的实体`Course`实体。</span><span class="sxs-lookup"><span data-stu-id="66012-207">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="66012-208">你使用`Single`时你知道该集合的集合上的方法将有只有一项。</span><span class="sxs-lookup"><span data-stu-id="66012-208">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="66012-209">如果传递给它的集合为空或没有多个项，单个方法将引发异常。</span><span class="sxs-lookup"><span data-stu-id="66012-209">The Single method throws an exception if the collection passed to it is empty or if there's more than one item.</span></span> <span data-ttu-id="66012-210">替代方法是`SingleOrDefault`，它返回默认值 (在此情况下 null) 如果该集合为空。</span><span class="sxs-lookup"><span data-stu-id="66012-210">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="66012-211">但是，在这种情况下，仍会生成异常 (从尝试查找`Courses`上的 null 引用的属性)，和异常消息将不太清楚地指示问题的原因。</span><span class="sxs-lookup"><span data-stu-id="66012-211">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="66012-212">当调用`Single`方法，你还可以传递在 Where 条件而不是调用`Where`方法单独：</span><span class="sxs-lookup"><span data-stu-id="66012-212">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="66012-213">而不是：</span><span class="sxs-lookup"><span data-stu-id="66012-213">Instead of:</span></span>

```csharp
.Where(I => i.ID == id.Value).Single()
```

<span data-ttu-id="66012-214">接下来，如果选择某一课程，从列表中的课程，视图模型中检索所选的课程。</span><span class="sxs-lookup"><span data-stu-id="66012-214">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="66012-215">然后视图模型的`Enrollments`属性加载和注册实体从该过程`Enrollments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-215">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="66012-216">修改教师索引视图</span><span class="sxs-lookup"><span data-stu-id="66012-216">Modify the Instructor Index view</span></span>

<span data-ttu-id="66012-217">在*Views/Instructors/Index.cshtml*，模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="66012-217">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="66012-218">突出显示所做的更改。</span><span class="sxs-lookup"><span data-stu-id="66012-218">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="66012-219">你已对现有代码做出以下更改：</span><span class="sxs-lookup"><span data-stu-id="66012-219">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="66012-220">更改到模型类`InstructorIndexData`。</span><span class="sxs-lookup"><span data-stu-id="66012-220">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="66012-221">更改页标题从**索引**到**教师**。</span><span class="sxs-lookup"><span data-stu-id="66012-221">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="66012-222">添加**Office**显示的列`item.OfficeAssignment.Location`才`item.OfficeAssignment`不为 null。</span><span class="sxs-lookup"><span data-stu-id="66012-222">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` is not null.</span></span> <span data-ttu-id="66012-223">（由于这是一个对零或一一个关系，因此可能不具备相关的 OfficeAssignment 实体。）</span><span class="sxs-lookup"><span data-stu-id="66012-223">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="66012-224">添加**课程**通过每个教师讲授显示课程的列。</span><span class="sxs-lookup"><span data-stu-id="66012-224">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="66012-225">请参阅[显式行转换与`@:`](xref:mvc/views/razor#explicit-line-transition-with-)有关此 razor 语法的详细信息。</span><span class="sxs-lookup"><span data-stu-id="66012-225">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="66012-226">添加动态添加的代码`class="success"`到`tr`的所选教师的元素。</span><span class="sxs-lookup"><span data-stu-id="66012-226">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="66012-227">此设置使用 Bootstrap 类所选行的背景色。</span><span class="sxs-lookup"><span data-stu-id="66012-227">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="66012-228">添加新的超链接标记为**选择**立即之前每个行中的其他链接，这将导致所选的教师 ID 发送到`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="66012-228">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="66012-229">运行应用并选择**教师**选项卡。没有相关的 OfficeAssignment 实体时，该页将显示相关的 OfficeAssignment 实体以及的空表单元格的 Location 属性。</span><span class="sxs-lookup"><span data-stu-id="66012-229">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![未选择任何项教师索引页](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="66012-231">在*Views/Instructors/Index.cshtml*文件之后结束表元素 （在文件末尾），, 添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="66012-231">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="66012-232">此代码将显示一个教师选中与一个教师相关的课程的列表。</span><span class="sxs-lookup"><span data-stu-id="66012-232">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="66012-233">此代码读取`Courses`要显示的课程列表的视图模型的属性。</span><span class="sxs-lookup"><span data-stu-id="66012-233">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="66012-234">它还提供了**选择**超链接，以便将发送到所选课程 ID`Index`操作方法。</span><span class="sxs-lookup"><span data-stu-id="66012-234">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="66012-235">刷新页面并选择一个教师。</span><span class="sxs-lookup"><span data-stu-id="66012-235">Refresh the page and select an instructor.</span></span> <span data-ttu-id="66012-236">此时你会看到一个网格，其中显示分配给所选教师的课程，对于每个课程中，你将看到分配部门的名称。</span><span class="sxs-lookup"><span data-stu-id="66012-236">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![选择教师索引页教师](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="66012-238">之后你刚添加的代码块，添加下面的代码。</span><span class="sxs-lookup"><span data-stu-id="66012-238">After the code block you just added, add the following code.</span></span> <span data-ttu-id="66012-239">当选中该过程时，此值显示课程中注册的学生的列表。</span><span class="sxs-lookup"><span data-stu-id="66012-239">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="66012-240">此代码读取为了显示学生课程中注册的列表视图模型的注册属性。</span><span class="sxs-lookup"><span data-stu-id="66012-240">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="66012-241">再次刷新页面并选择一个教师。</span><span class="sxs-lookup"><span data-stu-id="66012-241">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="66012-242">然后选择要查看的已注册的学生和其年级列表的课程。</span><span class="sxs-lookup"><span data-stu-id="66012-242">Then select a course to see the list of enrolled students and their grades.</span></span>

![教师索引页教师和所选课程](read-related-data/_static/instructors-index.png)

## <a name="explicit-loading"></a><span data-ttu-id="66012-244">显式加载</span><span class="sxs-lookup"><span data-stu-id="66012-244">Explicit loading</span></span>

<span data-ttu-id="66012-245">检索的讲师列表时*InstructorsController.cs*，指定为预先加载`CourseAssignments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-245">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="66012-246">假设你预期用户只是偶尔想要查看所选的教师和课程中的注册。</span><span class="sxs-lookup"><span data-stu-id="66012-246">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="66012-247">在这种情况下，你可能想要加载的注册数据才会在被请求。</span><span class="sxs-lookup"><span data-stu-id="66012-247">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="66012-248">若要查看有关如何执行显式加载操作的示例，请替换`Index`方法替换为以下代码，这将移除注册为预先加载并显式加载该属性。</span><span class="sxs-lookup"><span data-stu-id="66012-248">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="66012-249">突出显示代码更改。</span><span class="sxs-lookup"><span data-stu-id="66012-249">The code changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="66012-250">新代码将删除*ThenInclude*方法的代码中检索 instructor 实体调用注册数据。</span><span class="sxs-lookup"><span data-stu-id="66012-250">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="66012-251">如果选择教师和课程，突出显示的代码检索所选的课程，注册实体和每个注册的学生实体。</span><span class="sxs-lookup"><span data-stu-id="66012-251">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="66012-252">运行应用程序，请转到教师索引页现在和你将看到任何区别中的页上，显示的内容，不过你已更改如何检索的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-252">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="summary"></a><span data-ttu-id="66012-253">摘要</span><span class="sxs-lookup"><span data-stu-id="66012-253">Summary</span></span>

<span data-ttu-id="66012-254">你现在使用预先加载与一个查询和多个查询相关的数据读入导航属性。</span><span class="sxs-lookup"><span data-stu-id="66012-254">You've now used eager loading with one query and with multiple queries to read related data into navigation properties.</span></span> <span data-ttu-id="66012-255">在下一教程你将学习如何更新相关的数据。</span><span class="sxs-lookup"><span data-stu-id="66012-255">In the next tutorial you'll learn how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="66012-256">[上一页](complex-data-model.md)
>[下一页](update-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="66012-256">[Previous](complex-data-model.md)
[Next](update-related-data.md)</span></span>  