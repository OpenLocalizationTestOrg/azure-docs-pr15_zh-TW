<properties
   pageTitle="管理資料庫中 Azure SQL Data Warehouse |Microsoft Azure"
   description="管理 SQL Data Warehouse 資料庫的概觀。 包含管理工具，DWUs 及擴充效能，疑難排解查詢效能，建立好的安全性原則，以及從資料損毀或地區的資料，請還原資料庫。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>管理 Azure SQL Data Warehouse 的資料庫

SQL Data Warehouse 會自動執行各種管理您的資料庫。 例如，若要不按比例縮放效能您只需要調整和支付計算資源的正確層級，然後讓 SQL Data Warehouse 擴展和重新調整所有處理。 

您一定會想要監控您的工作量來識別您效能的需求，以及疑難排解長期執行查詢。 您也必須執行幾個安全性工作管理使用者與登入的權限。

本概觀涵蓋這些層面的管理 SQL Data Warehouse。

- 管理工具
- 小數位數計算
- 暫停和履歷表
- 效能最佳作法
- 查詢監控
- 安全性
- 備份與還原

## <a name="management-tools"></a>管理工具

您可以使用各種不同的工具來管理 SQL Data Warehouse 的資料庫。 當您管理資料庫時，您會開發工具喜好設定，針對每一種您需要執行的工作。

### <a name="azure-portal"></a>Azure 入口網站
[Azure 入口網站][]是網頁式入口網站位置建立、 更新和刪除資料庫及監視資料庫的資源。 這項工具相當適合您才剛開始使用 Azure 管理少量的資料倉庫資料庫，或需要快速進行操作。

若要開始使用 Azure 入口網站，請參閱[建立 SQL Data Warehouse （Azure 入口網站）][]。

### <a name="sql-server-data-tools-in-visual-studio"></a>在 Visual Studio 中的 SQL Server Data Tools
[SQL Server Data Tools][](SSDT) 在 Visual Studio 中可讓您連線至，管理及開發資料庫。 如果您熟悉 Visual Studio 或其他整合式的開發環境 (Ide) 應用程式開發人員，請嘗試使用 Visual Studio SSDT。

SSDT 包含 SQL Server 物件總管] 可讓您以視覺化效果呈現與連線，請執行 SQL Data Warehouse 資料庫的指令碼。 若要快速連線至 SQL Data Warehouse，您只要按一下命令列中的 [ **Visual Studio 中開啟**] 按鈕時 Azure 傳統入口網站中檢視資料庫的詳細資訊。  

若要開始使用 SSDT Visual Studio 中，請參閱[查詢 Azure SQL Data Warehouse Visual studio][]。

### <a name="command-line-tools"></a>命令列工具
命令列工具非常適合自動執行您的工作量。  PowerShell 和 sqlcmd 兩個絕佳方法來自動化程序。  我們建議您這些工具來管理大量邏輯伺服器及部署資源生產環境中的變更，可以建立指令碼並然後自動化所需的工作。

### <a name="dynamic-management-views"></a>動態管理檢視 

Dmv 是管理 SQL Data Warehouse 麵包與奶油。 執行幾乎所有在入口網站中會呈現的資訊依賴 Dmv。 若要查看的 SQL 資料倉庫 Dmv 清單，請參閱[SQL Data Warehouse 系統檢視][]。

若要開始，請參閱[連線] 和 [sqlcmd 查詢][]，和[建立資料庫 (PowerShell)][]。

## <a name="scale-compute"></a>小數位數計算

在 SQL Data Warehouse，您可以快速縮放或回以增加或減少的 CPU、 記憶體和 I/O 頻寬計算資源的效能。 若要縮放效能，您只需要是調整 SQL Data Warehouse 配置至您的資料庫的資料倉庫單位 (DWUs) 的數目。 SQL Data Warehouse 快速進行變更，以及處理所有的基礎變更硬體或軟體。

若要進一步瞭解縮放 DWUs 」 的資訊，請參閱[比例效能][]。

##  <a name="pause-and-resume"></a>暫停和履歷表

若要儲存成本，您可以將游標暫停於，並繼續計算資源視。 比方說，如果您不使用資料庫夜間和週末，可以將游標暫留在這些，並繼續在白天。 您不會針對 DWUs，暫停資料庫時。

如需詳細資訊，請參閱[暫停計算][]及[計算履歷表][]。

## <a name="performance-best-practices"></a>效能最佳作法

開始使用新的技術，探索秘訣和竅門工作最佳從頭開始，可以儲存您很多時間。  您會發現整個許多我們主題的最佳作法。

若要開發您的工作量時，請查看最重要的考量許多摘要，請參閱[SQL 資料倉庫最佳作法][]。

## <a name="query-monitoring"></a>查詢監控

有時查詢太長，但是您不確定的哪一個是 [問題。 SQL Data Warehouse 具有動態管理檢視 (Dmv)，找出哪些查詢時間太長，您可以使用。 

若要尋找長期執行查詢，請參閱[監控您使用 Dmv 的工作量][]。

## <a name="security"></a>安全性

若要維持系統安全，必須是在通知，並防止未經授權任何的存取類型。 安全性系統需求，以確保防火牆規則，只允許經授權的位置中的 IP 位址可以連線。 需要使用者認證的適當的驗證。 使用者連線至資料庫後，使用者只應有的權限執行動作的最小的數字。 若要保護資料，您可以使用加密。 請務必也有稽核及追蹤，您可以追溯事件，如果有任何可疑的活動。

若要瞭解如何管理移到的安全性，不對至[安全性概觀][]。

## <a name="backup-and-restore"></a>備份與還原

有可靠 backps 的資料是任何生產資料庫中不可或缺的一部分。 SQL Data Warehouse 保留您的資料安全自動備份定期您使用的資料庫。 這些備份可讓您要復原您已在此損毀的資料，或不小心刪除您的資料或資料庫的案例。  資料備份排程，保留原則，以及如何還原資料庫，請參閱[還原的快照][]。

## <a name="next-steps"></a>後續步驟
使用原則會更容易管理您的資料庫 SQL Data Warehouse 良好的資料庫設計。 若要瞭解更多]，不對移至[開發概觀][]。

<!--Image references-->

<!--Article references-->
[建立 SQL Data Warehouse （Azure 入口網站）]: sql-data-warehouse-get-started-provision.md
[建立資料庫 (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[使用 Visual Studio 查詢 Azure SQL Data Warehouse]: sql-data-warehouse-query-visual-studio.md
[連線並 sqlcmd 的查詢]: sql-data-warehouse-get-started-connect-sqlcmd.md
[開發概觀]: sql-data-warehouse-overview-develop.md
[監控您使用 Dmv 的工作量]: sql-data-warehouse-manage-monitor.md
[暫停運算]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[還原快照]: sql-data-warehouse-restore-database-overview.md
[履歷表運算]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[縮放比例效能]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[安全性概觀]: sql-data-warehouse-overview-manage-security.md
[SQL 資料倉庫最佳作法]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse 系統檢視]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure 入口網站]: http://portal.azure.com/
