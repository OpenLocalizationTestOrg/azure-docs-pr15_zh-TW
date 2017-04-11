<properties
    pageTitle="SQL 資料庫開發概觀 |Microsoft Azure"
    description="進一步瞭解可用的連線文件庫，應用程式連線至 SQL 資料庫的最佳作法。"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>SQL 資料庫開發概觀
本文將引導開發人員時應該知道的撰寫程式碼來連線到 Azure SQL 資料庫的基本考量。

## <a name="language-and-platform"></a>語言與平台
沒有可用的各種程式設計語言與平台版的程式碼範例。 您可以找到在程式碼範例的連結︰ 

* 詳細資訊︰ [SQL 資料庫與 SQL Server 的連線庫](sql-database-libraries.md)

## <a name="resource-limitations"></a>資源限制
Azure SQL 資料庫管理才能使用這兩個不同機制資料庫的資源︰ 資源治理與強制執行的限制。

* [Azure SQL 資料庫資源限制](sql-database-resource-limits.md)的詳細資訊︰

## <a name="security"></a>安全性
Azure SQL 資料庫提供資源限制存取，保護資料，並監控 SQL 資料庫上的活動。

* [保護您的 SQL 資料庫](sql-database-security.md)的詳細資訊︰

## <a name="authentication"></a>驗證
* Azure SQL 資料庫支援的 SQL Server 驗證使用者並登入]，以及[Azure Active Directory 驗證](sql-database-aad-authentication.md)使用者和登入。
* 您要指定特定的資料庫，而不是預設為*主要*資料庫。
* 您無法使用 SQL 資料庫中的**使用 myDatabaseName;**的 SQL 陳述式切換到另一個資料庫。
* 詳細資訊︰ [SQL 資料庫安全性︰ 管理資料庫存取和登入安全性](sql-database-manage-logins.md)

## <a name="resiliency"></a>恢復功能
連線至 SQL 資料庫時的暫時性錯誤時，您的程式碼應該重試通話。  我們建議您的重試邏輯使用輪詢邏輯，讓它不會不會使不勝負荷 SQL 資料庫與重試同時的多個用戶端。

* 錯誤碼範例︰ 說明的程式碼範例重試邏輯，請參閱在您所選擇的語言的範例︰ [SQL 資料庫與 SQL Server 的連線庫](sql-database-libraries.md)
* 詳細資訊︰ [SQL 資料庫用戶端程式的錯誤訊息](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>管理連線
* 在您的用戶端連線邏輯，覆寫預設為 30 秒的逾時。  15 秒的預設值是屬於在網際網路上太簡短的連線。
* 如果您使用[連接共用](http://msdn.microsoft.com/library/8xx3tyca.aspx)，請務必關閉連線立即您的程式沒有在使用它，，不準備重複使用它。

## <a name="network-considerations"></a>網路考量
* 在電腦上主控您的用戶端程式，確保防火牆可讓連接埠 1433年外寄的 TCP 通訊。  詳細資訊︰[設定 Azure SQL 資料庫防火牆](sql-database-configure-firewall-settings.md)
* 如果您的用戶端執行 Azure 虛擬機器 (VM) 上時，您的用戶端程式會連線到 SQL 資料庫 V12，您必須開啟 VM 特定連接埠範圍。 詳細資訊︰[除了 1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)
* 用戶端連線到 Azure SQL 資料庫 V12 有時候會略過 proxy，而直接與資料庫互動。 1433 以外的連接埠變得很重要。 詳細資訊︰[除了 1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>資料 Sharding 彈性的比例
彈性的比例可簡化縮放比例查看 （和） 程的序。 

* [Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)
* [Azure SQL 資料庫彈性的縮放比例 Preview 快速入門](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>後續步驟

瀏覽所有的[SQL 資料庫的功能](https://azure.microsoft.com/services/sql-database/)。
