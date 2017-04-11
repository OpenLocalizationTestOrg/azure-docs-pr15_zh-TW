<properties
    pageTitle="什麼是 SQL 資料庫？ SQL 資料庫簡介 |Microsoft Azure"
    description="介紹 SQL 資料庫︰ 技術的詳細資料和功能 Microsoft 的關聯式資料庫雲端中的管理系統 (RDBMS)。"
    keywords="什麼是 sql 資料庫的 sql 簡介，sql 簡介"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>什麼是 SQL 資料庫？ SQL 資料庫的簡介

SQL 資料庫是根據市場前置字元的 Microsoft SQL Server 引擎，與重要功能的雲端關聯式資料庫服務。 SQL 資料庫的形式提供如預期呈現效能，沒有停機時間、 業務連續性與資料保護與延展性，所有的零附近管理。 您可以專注於快速應用程式開發和加速時間上市，而非管理虛擬機器和基礎結構。 因為它依據[SQL Server](https://msdn.microsoft.com/library/bb545450.aspx)引擎，SQL 資料庫支援現有 SQL Server 工具、 文件庫及 Api，可以方便您移動及擴充至雲端。

本文是 SQL 資料庫的核心概念和效能、 延展性及管理，以探索詳細資料的連結與相關功能的簡介。 如果您已準備好跳中，您可以[建立您的第一個 SQL 資料庫](sql-database-get-started.md)或[建立彈性的資料庫資料庫](sql-database-elastic-pool-create-portal.md)以分鐘為單位。 若要更深入的不動產，請觀看此 30 分鐘視訊。

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>調整效能與不停機時間的縮放比例

使用基本、 標準和進階版*服務層*在 SQL 資料庫。 每個服務層級支援輕量型重量級資料庫工作量提供[不同層級的效能和功能](sql-database-service-tiers.md)。 您可以建立您的第一個應用程式小型資料庫上的幾個月，然後[變更服務層級](sql-database-scale-up.md)的 bucks 手動或以程式設計方式隨時為您的應用程式，就病毒全球，一定會為您的應用程式或您的客戶停機時間。

適用於眾多企業及應用程式，也可以建立資料庫，並視需要撥號單一資料庫效能向上或向下即足以，特別是如果使用模式是相當如預期呈現。 但如果您有無法預期的使用模式時，它可以很難管理成本和商務模型。

[彈性的集區](sql-database-elastic-pool.md)SQL 資料庫中解決這個問題。 概念非常簡單。 您配置效能到資料庫，並支付集區的整體效能，而不是單一資料庫效能。 您不需要向上或向下，撥資料庫效能。 在資料庫中，稱為*彈性的資料庫*，資料庫會自動調整] 和 [下符合需求。 彈性的資料庫使用，但不超過集區的限制，以便您成本維持如預期呈現，即使沒有資料庫的使用方式。 可以[新增和移除的集區的資料庫](sql-database-elastic-pool-manage-portal.md)，請調整您的應用程式從少數幾個資料庫千分位，所有您控制預算。 若要進一步瞭解使用彈性的集區 SaaS 應用程式的設計模式，請參閱[Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

您兩種 — 單一或彈性，您不鎖定狀態。 您可以混合彈性的資料庫資料庫的單一資料庫，並變更服務層的單一資料庫及建立創新設計集區。 此外，power 和 Azure 範圍，您可以混合符合 Azure 服務，以符合您唯一的新式應用程式的設計需求、 驅動成本和資源的效率，並解除鎖定新的商機 SQL 資料庫。

但您要如何比較相關的資料庫及資料庫集區效能？ 如何知道以滑鼠右鍵按一下 [停止當您在向上和向下撥？ 答案是單一資料庫的資料庫交易單位 (DTU) 和彈性的資料庫及資料庫集區彈性 DTU (eDTU)。 請參閱[SQL 資料庫選項和效能︰ 瞭解什麼是用於每個服務層級](sql-database-service-tiers.md)如需詳細資訊。

## <a name="keep-your-app-and-business-running"></a>讓應用程式與企業

Azure 的產業前置 99.99%顯示狀態服務等級協定[(SLA)](http://azure.microsoft.com/support/legal/sla/)，由 Microsoft 管理資料中心，全域網路有助於保持執行 24/7 應用程式。 每個 SQL 資料庫]，與您善用內建資料保護、 容錯和原本設計、 購買、 建立及管理資料保護。 即使是這樣，根據您的業務需求，您可能會要求其他圖層的保護，以確保您的應用程式和企業版您可以快速時修復損毀、 錯誤，或其他項目。 從 [SQL 資料庫]，每個服務層級會提供不同的功能，您可以使用來運用及執行並保持功能表的方式。 若要回到先前的狀態，早 35 天的資料庫，您可以使用時間點還原。 此外，如果主控您的資料庫資料中心體驗中斷，可以在不同區域中的資料庫複本的容錯移轉。 或者，您可以使用 [複本的升級或重新配置到不同的區域。

![SQL 資料庫地理複寫](./media/sql-database-technical-overview/azure_sqldb_map.png)


使用不同的服務層不同業務連續性功能的相關詳細資料，請參閱[業務連續性](sql-database-business-continuity.md)。

## <a name="secure-your-data"></a>保護您的資料
SQL Server 有傳統的實心資料安全性 SQL 資料庫 upholds 的限制存取、 保護資料，並協助您監控活動功能。 如概要必須 SQL 資料庫中的安全性選項，請參閱[設定安全性 SQL 資料庫](sql-database-security.md)。 請參閱[SQL Server 資料庫引擎和 SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)的安全性功能的完整檢視。 然後，請造訪[Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)的 Azure 的平台安全性的詳細資訊。

## <a name="next-steps"></a>後續步驟
既然您已閱讀的簡介 SQL 資料庫，並回答問題 」 功能 SQL 資料庫]，您已準備好︰

- 請參閱[價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)單一資料庫和彈性的資料庫成本比較計算機。
- 深入了解[彈性的資料庫](sql-database-elastic-pool.md)。
- 開始建立[您的第一個資料庫](sql-database-get-started.md)。
- [連線並 SSMS 的查詢](sql-database-connect-query-ssms.md)
- 建立您在 C#、 Java、 Node.js、 PHP、 Python 或 [注音標示的第一個應用程式︰ [SQL 資料庫與 SQL Server 的連線庫](sql-database-libraries.md)
- 請參閱標題和描述的[所有主題 Azure sql 資料庫服務](sql-database-index-all-articles.md)的索引。
