<properties
    pageTitle="在 Windows 上使用 PHP 連線到 SQL 資料庫 |Microsoft Azure"
    description="顯示範例 PHP 程式，以從 Windows 用戶端連線到 Azure SQL 資料庫，並提供連結所需的用戶端軟體必要元件。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-php-on-windows"></a>在 Windows 上使用 PHP 連線到 SQL 資料庫


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


本主題說明如何您可以從連線至 Azure SQL 資料庫撰寫 PHP 在 Windows 上執行的用戶端應用程式。

## <a name="step-1--configure-development-environment"></a>步驟 1︰ 設定的開發環境

[設定 PHP 開發的開發環境](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>步驟 2︰ 建立 SQL 資料庫

請參閱[快速入門] 頁面](sql-database-get-started.md)，瞭解如何建立範例資料庫。  請務必在您依照建立**資料庫範本的 AdventureWorks**指南。 使用**AdventureWorks 結構描述**所示的範例。


## <a name="step-3-get-connection-details"></a>步驟 3︰ 取得連線的詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## <a name="step-4-run-sample-code"></a>步驟 4︰ 執行程式碼範例

* [連線至 SQL 使用 PHP 的概念驗證](https://msdn.microsoft.com/library/mt720665.aspx)
* [連線至 SQL 與 PHP 的 resiliently](https://msdn.microsoft.com/library/mt720667.aspx)


## <a name="next-steps"></a>後續步驟

* 檢閱[SQL 資料庫開發概觀](sql-database-develop-overview.md)
* [Microsoft SQL Server 的 PHP 驅動程式](https://msdn.microsoft.com/library/dn865013.aspx)的詳細資訊
* 如需有關 PHP 安裝及使用方式的詳細資訊，請參閱[使用 PHP 存取 SQL Server 資料庫](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx)。

## <a name="additional-resources"></a>其他資源 

* [Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有的[SQL 資料庫的功能](https://azure.microsoft.com/services/sql-database/)
