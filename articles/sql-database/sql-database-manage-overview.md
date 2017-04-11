<properties
    pageTitle="概觀︰ 管理工具 SQL 資料庫 |Microsoft Azure"
    description="比較工具和管理 Azure SQL 資料庫的選項"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>概觀︰ 管理工具 SQL 資料庫

本主題探討並比較工具和管理 Azure SQL 資料庫，因此您可以挑選適當的工具，用於工作企業與您的選項。 選擇適當的工具，取決於多少資料庫您管理，該工作，並執行工作的頻率。

## <a name="azure-portal"></a>Azure 入口網站

[Azure 入口網站](https://portal.azure.com)是 web 應用程式位置建立、 更新和刪除資料庫及邏輯伺服器及監視資料庫的活動。 如果您才剛開始使用 Azure 管理幾個資料庫，或需要執行一些快速，相當適合這項工具。

如需有關如何使用入口網站的詳細資訊，請參閱[管理 SQL 資料庫使用 Azure 入口網站](sql-database-manage-portal.md)。

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio 和 SQL Server Data Tools Visual Studio 中

SQL Server 管理 Studio (SSMS) 和 SQL Server Data Tools (SSDT) 是在您的電腦，管理，以及開發資料庫在雲端執行的用戶端工具。 如果您熟悉 Visual Studio 或其他整合式的開發環境 (Ide)，[請嘗試使用 Visual Studio 中的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)應用程式開發人員。 許多資料庫系統管理員熟悉 SSMS，可以使用 Azure SQL 資料庫。 [下載最新版的 SSMS](https://msdn.microsoft.com/library/mt238290)並使用 Azure SQL 資料庫時，一律使用最新版本。 如需有關如何管理與 SSMS Azure SQL 資料庫的詳細資訊，請參閱[管理 SQL 資料庫使用 SSMS](sql-database-manage-azure-ssms.md)。

> [AZURE.IMPORTANT] 一律使用最新版本的[SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290)和[SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)維持同步處理與更新 Microsoft Azure SQL 資料庫。


## <a name="powershell"></a>PowerShell

您可以使用 PowerShell 來管理資料庫及彈性的資料庫集區，並自動化 Azure 資源部署。 Microsoft 建議管理大量資料庫和自動化部署及生產環境中的資源變更這項工具。

如需詳細資訊，請參閱[使用 PowerShell 管理 SQL 資料庫](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>彈性的資料庫工具
若要執行的動作，例如使用的彈性的資料庫工具 

* 執行 T SQL 指令碼針對一組資料庫使用的[彈性的工作](sql-database-elastic-jobs-overview.md)
* 移動多租用戶模型資料庫[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)單一租用戶模型
* 管理單一租用戶模型或使用[彈性的縮放比例用戶端文件庫](sql-database-elastic-database-client-library.md)的多租用戶模型中的資料庫。
 

## <a name="additional-resources"></a>其他資源

- [Azure 資源管理員](https://azure.microsoft.com/features/resource-manager/)
- [Azure 自動化](https://azure.microsoft.com/documentation/services/automation/)
- [Azure 排程器](https://azure.microsoft.com/documentation/services/scheduler/)