<properties
   pageTitle="Azure SQL 資料庫解決方案快速開始 |Microsoft Azure"
   description="深入了解 Azure SQL 資料庫解決方案"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>探索 Azure SQL 資料庫解決方案快速入門

本文概述 Azure SQL 資料庫解決方案快速入門。 這些快速啟動位於 GitHub SQL Server 範例存放庫，並示範如何根據真實案例完整的解決方案中使用 SQL 資料庫。 簡單逐步解說教學課程，示範使用特定的 SQL 資料庫功能，請參閱[探索 Azure SQL 資料庫教學課程](sql-database-explore-tutorials.md)。

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>請嘗試 WingTipTickets 示範並實際進行操作

[Azure SQL 資料庫 WingTipTickets](https://github.com/microsoft/wingtiptickets)示範和實際進行操作示範 Azure SQL 資料庫及用來銷售運作票證 Azure 搜尋為基礎的範例應用程式。


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>收集並監控跨多個集區的 [資源使用狀況

[解決方案快速入門︰ 使用 PowerShell 彈性的資料庫遙測](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)收集和監視 SQL 資料庫資源使用狀況跨多個訂閱集提供的解決方案。 當您在訂閱中有大量的資料庫時，將會很難分別監控每個彈性的資料庫。

若要解決此問題，您可以結合 SQL 資料庫 PowerShell cmdlet 和 T SQL 查詢從多個集區和其資料庫收集資源使用狀況。 這可協助您監視及更有效率地分析資源使用狀況。

此快速入門提供 PowerShell 指令碼和 T SQL 查詢，以及文件的一的組解決方案的功能，以及如何實作它。

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>快速入門 SaaS 情境中操作的彈性的資料庫

 [解決方案快速入門︰ 彈性的集區自訂儀表板 SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard)軟體為-的-方案 (SaaS) 案例運用 SaaS 應用程式提供效益和擴充資料庫後端 SQL 資料庫的彈性的資料庫功能提供的解決方案。

在本方案中，您將逐步 web 應用程式的實作。 此 web 應用程式可讓您以視覺化方式呈現負載所使用自訂的儀表板的補充 Azure 入口網站的載入產生器建立彈性的資料庫。

此快速入門提供載入產生器和監視 web 應用程式，以及相關應用程式的功能，以及如何使用文件。

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>使用程式碼第一個開發和實體架構建立 Azure SQL 資料庫

影片與範例中[的程式碼第一個新的資料庫](https://msdn.microsoft.com/data/jj193542.aspx)提供目標為新資料庫的程式碼第一個開發的簡介。 這種情況下的目標的資料庫的不存在，但其所建立的第一個程式碼。 或者，此案例會建立空的資料庫對其程式碼第一個新增新的資料表。

程式碼第一次可讓您可使用 C# 或 Visual Basic.NET 類別定義您的模型。 使用類別和內容的屬性，或使用 fluent 的 API，您可以執行選擇性的其他設定。

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>彈性的資料庫工具整合的實體架構應用程式

[彈性的資料庫的實體架構的用戶端文件庫](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)範例會顯示您需要的實體架構應用程式以整合[彈性的資料庫工具](sql-database-elastic-scale-get-started.md)進行的變更。 焦點會放在撰寫[晶怪對應管理](sql-database-elastic-scale-shard-map-management.md)及[資料而異路由](sql-database-elastic-scale-data-dependent-routing.md)的實體架構的程式碼第一種方法。

[程式碼第一個新的資料庫範例的 EF](http://msdn.microsoft.com/data/jj193542.aspx)做為範例在這個範例來說執行。 上述這份文件的範例是彈性的資料庫工具組 Visual Studio 程式碼範例中的範例。

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>彈性的資料庫工具整合列層級安全性

[有彈性的資料庫的工具和列層級安全性 multitenant 應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)會顯示所做的變更，您需要進行的實體架構應用程式[列層級安全性](https://msdn.microsoft.com/library/dn765131)與整合[彈性的資料庫工具](sql-database-elastic-scale-get-started.md)。 這個範例說明如何建立具有支援 multitenant 擊碎彈性的資料層的應用程式搭配使用這些技術。

使用 ADO.NET SqlClient 或實體架構執行此動作。 此範例會新增 multitenant 晶怪資料庫的支援擴充[彈性的資料庫的實體架構的用戶端文件庫](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)。
建置四個租用戶與兩個 multitenant 晶怪資料庫建立部落格和文章的簡單主控台應用程式。

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>建立線上調查 Tailspin 問卷應用程式

此[Tailspin 問卷範例應用程式](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)是 multitenant web 應用程式，稱為問卷，可讓使用者建立線上問卷調查。 範例地址有關如何管理 multitenant 應用程式，包括註冊、 驗證、 授權、 和應用程式角色的使用者身分識別的一些重要考量。

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>深入瞭解 Contoso 診所示範應用程式的 SQL 資料庫的最新的安全性功能

此[Contoso 診所示範應用程式](https://github.com/Microsoft/azure-sql-security-sample)會展示的最新的安全性功能的 SQL 資料庫。

## <a name="next-steps"></a>後續步驟

[探索 Azure SQL 資料庫教學課程](sql-database-explore-tutorials.md)
