<properties
    pageTitle="使用 Windows 上 JDBC Java 來連線到 SQL 資料庫 |Microsoft Azure"
    description="將呈現可用來連線到 Azure SQL 資料庫的 Java 程式碼範例。 此範例使用 JDBC，並在 Windows 用戶端電腦上執行。"
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>使用 Windows 上 JDBC Java 來連線到 SQL 資料庫


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


本主題提供可用來連線到 Azure SQL 資料庫的 Java 程式碼範例。 Java 範例是在 Java 開發套件 (JDK) 版本 1.8。 範例會使用 JDBC 驅動程式連線至 Azure SQL 資料庫。

## <a name="step-1--configure-development-environment"></a>步驟 1︰ 設定的開發環境

[設定 Java 開發的開發環境](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>步驟 2︰ 建立 SQL 資料庫

請參閱[快速入門] 頁面](sql-database-get-started.md)，瞭解如何建立資料庫。  

## <a name="step-3-get-connection-string"></a>步驟 3︰ 取得連線字串

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] 如果您使用的 JTDS JDBC 驅動程式，然後您會需要新增 「 ssl = 需要 「 url 的連線字串，而您需要設定下列選項 JVM 」-Djsse.enableCBCProtection=false 」。 這個選項，JVM 停用的安全性漏洞修正，因此請確定您了解哪些風險之前設定此選項。

## <a name="step-4-run-sample-code"></a>步驟 4︰ 執行程式碼範例

* [連線至 SQL 使用 Java 的概念驗證](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>後續步驟

* 請造訪[Java 開發人員中心](/develop/java/)。
* 檢閱[SQL 資料庫開發概觀](sql-database-develop-overview.md)
* [Microsoft SQL Server 的 JDBC 驅動程式](https://msdn.microsoft.com/library/mt484311.aspx)的詳細資訊

## <a name="additional-resources"></a>其他資源 

* [Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有的[SQL 資料庫的功能](https://azure.microsoft.com/services/sql-database/)
