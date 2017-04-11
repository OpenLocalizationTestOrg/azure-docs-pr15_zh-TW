<properties
    pageTitle="規劃升級 SQL 資料庫 V12 |Microsoft Azure"
    description="準備工作，並限制升級至新版 Azure SQL 資料庫 V12 的相關說明。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>規劃及準備升級至 SQL 資料庫 V12


本主題說明規劃與準備工作，您必須執行從版本 V11 升級 V12 Azure SQL 資料庫。


使用支援 V12 升級新[Azure 入口網站](https://portal.azure.com/)。


下表列出 V12 的其他說明主題。


| 標題及連結 | 內容的描述 |
| :--- | :--- |
| [在 SQL 資料庫 V12 中的新功能](sql-database-v12-whats-new.md) | 說明 V12 如何 Azure SQL 資料庫近為完整的不一致性致歉與 Microsoft SQL Server 的詳細資料。 |
| [建立資料庫的 SQL 資料庫 V12](sql-database-get-started.md) | 說明如何建立新的 Azure SQL 資料庫在 V12 的版本。 說明各種不同的選項，除了只是空的資料庫。 |


## <a name="plan-ahead"></a>規劃


下面子區段說明的學習及您必須先採取動作趨近於您的 Azure SQL 資料庫升級為 V12 解決的決策。

### <a name="version-clarification"></a>版本釐清


這份文件與 Microsoft Azure SQL 資料庫可從版本 V11 升級到 V12。 正式的版本號碼會關閉下列兩個值，報告 SQL 陳述式**選取@@version;**:


- 12.0.2000.8 *（或更新版本，元 V12）*
- 11.0.9228.18 *(V11)*
 - 有時 V11 已也稱為瓦 v2。

### <a name="service-tier-planning"></a>服務層規劃


開始使用 V12，Azure SQL 資料庫會支援只名為基本、 標準和進階版服務層。 V12 不支援的網頁和企業版服務層。 因此，如果您打算要升級您目前是在 Web 或企業版服務層級的 Azure SQL 資料庫，您必須決定其新的服務層應該是。


如需詳細瞭解基本、 標準和進階版服務層級的詳細資訊，請參閱︰

- [SQL 資料庫服務層](sql-database-service-tiers.md)
- [將 SQL 資料庫 Web/Business 資料庫升級為新的服務層](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>檢閱地理複寫設定


如果 Azure SQL 資料庫地理複寫的設定，您應該文件其目前的設定，並開始升級準備動作之前停止地理複寫。 升級完成之後，您必須重新資料庫設定地理複寫。


策略是保留來源和測試資料庫的複本。


## <a name="preparation-actions"></a>準備動作


規劃在您完成之後，您可以執行的動作所述的步驟來準備的最後一個升級階段下面子區段中。


最後的升級階段的詳細的說明可在 [連結至在本說明主題頂端的 [說明] 主題。


### <a name="service-tier-actions"></a>服務層動作


不支援 V12 價格層的網頁和企業版服務。


如果 V11 Azure SQL 資料庫網頁或企業版的資料庫，升級程序提供將您的資料庫切換成的支援層。 升級建議層符合您的資料庫的工作量歷程記錄]。 不過，您可以選擇任何您喜歡的支援的層。


您可以切換離開 Web 商務層 V11 資料庫，開始升級之前，以減少升級期間所需的步驟。 您可以使用新的[Azure 入口網站](https://portal.azure.com/)。


如果您不確定要切換至哪些服務層級，S2 層級的標準層可能直覺式的初始選項。 任何較低層會有資源少於網頁和商務層之前。


### <a name="suspend-geo-replication-during-upgrade"></a>升級期間暫停地理複寫


升級到 V12 地理複寫會使用您的資料庫中無法執行。 您必須先重新設定您的資料庫，若要停止使用地理複寫。


升級完成之後，您可以設定您的資料庫，再使用 [地理複寫。


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>在用戶端連線 Azure VM 開啟連接埠


如果您的用戶端程式連線到 SQL 資料庫 V12 時您的用戶端執行 Azure 虛擬機器 (VM) 上時，您必須開啟下列連接埠範圍 VM 上︰

- 11000 11999
- 14000 14999


按一下[這裡](sql-database-develop-direct-route-ports-adonet-v12.md)的詳細資料的 SQL 資料庫 V12 的連接埠。 連接埠所需的 SQL 資料庫 V12 中的效能增強功能。


##<a id="limitations"></a>期間和 V12 升級之後的限制


### <a name="portals-for-v12"></a>入口網站的 V12


有三個入口網站的 Azure，各有不同的功能，有關 SQL 資料庫 V12。


- [http://portal.azure.com/](https://portal.azure.com/)<br/>此 Azure 入口網站的新和仍在預覽狀態。 此入口網站還不想在完整的一般可用性 (GA)。 此入口網站︰
 - 可以管理您的 V12 伺服器及資料庫。
 - 可以升級 V12 V11 資料庫。


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>此 Azure 傳統入口網站最後可能清出。 此入口網站︰
 - 可以管理您的 V12 伺服器及資料庫。
 - 可以*升級您 V11 資料庫至 V12* 。


- (http://*yourservername*。 database.windows.net)<br/>Azure SQL 資料庫傳統入口網站︰
 - 可以*不*管理 V12 伺服器。


我們建議您連線至 Azure SQL 資料庫的 Visual Studio 2015 (VS2015)。 VS2015 可用於下列工作︰


- 若要執行的 SQL 陳述式。
- 設計結構描述。
- 若要開發線上或離線資料庫。


或相反地，免費您可以下載[Visual Studio 社群 2015年](https://www.visualstudio.com/vs/community/)，也就是 VS2015 的完整功能版本。


在舊版 Azure 傳統入口網站，在資料庫頁面上，您可以按一下**Visual Studio 中開啟**以啟動 VS2015 Azure SQL 資料庫連線的電腦上。


另一個方法，您可以使用 SQL Server 管理 Studio (SSMS) 2014 [CU6](http://support.microsoft.com/kb/3031047/)連線至 Azure SQL 資料庫。 更多詳細資料是在此部落格文章︰<br/>[用戶端工具更新 Azure SQL 資料庫](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)。


> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


### <a name="limitation-during-upgrade-to-v12"></a>限制*在*升級成 V12


您仍然可以使用資料存取 V12 升級期間內使用 V11 資料庫。 還沒有要考慮的一些限制。


| 限制 | 描述 |
| :--- | :--- |
| 升級的持續時間 | 升級的工期而定的大小、 edition 與資料庫伺服器中的數字。 升級程序可以執行小時，特別是針對含有資料庫伺服器的天的伺服器︰<br/><br/>*大於 50 GB，或<br/>*在非進階版服務層<br/><br/>建立新的資料庫升級期間伺服器上也可以增加升級的持續時間。 |
| 地理複寫 | 地理複寫不支援從 V11 升級目前涉及 V12 伺服器上。 |
| 資料庫無法簡要 V12 升級的最後一個階段中 | 升級程序時，仍然可以使用屬於 V11 伺服器的資料庫。 不過，連線到伺服器，並在資料庫無法使用簡要中的最後一個階段，當透過切換開始從 V11 好 V12。<br/><br/>一段期間切換的範圍可從 40 秒為 5 分鐘。 大多數的伺服器，透過切換預期 90 秒內完成。 一段時間的參數會增加有大量的資料庫，或當資料庫有重寫工作負載的伺服器。 |


### <a name="limitation-after-upgrade-to-v12"></a>限制*之後*升級至 V12


| 限制 | 描述 |
| :--- | :--- |
| 無法還原為 V11 | 升級就地之後, 無法還原或復原結果。 |
| 網站或商務層 | 一旦在升級啟動新的 V12 資料庫伺服器不再可以辨識的內容，或接受網頁或企業版服務層級。 |



### <a name="export-and-import-after-upgrade-to-v12"></a>匯出與匯入*後*升級，以 V12


您可以匯出或匯入 V12 資料庫使用[Azure 入口網站](https://portal.azure.com/)。 或者，您可以匯出或匯入使用下列工具︰


- SQL Server Management Studio (SSMS)
- Visual Studio 2015
- 資料層應用程式架構 (DacFx)


不過，若要使用的工具，您必須先擁有或其最新的更新，以確保其所支援功能的新 V12 安裝︰


- [SQL Server Management Studio 2014 累計更新 6](http://support2.microsoft.com/kb/3031047)
- [SQL Server 資料庫工具 Visual Studio 2013 中的 2015 年 2 月更新](https://msdn.microsoft.com/data/hh297027)
- [2015 年 2 月資料層應用程式架構 (DacFx) Azure SQL 資料庫 V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 上述的工具連結或 2015 年 3 月 2 日之後更新。 我們建議您使用這些工具這些較新的更新。


#### <a name="automated-export"></a>自動化的匯出


自動化的匯出繼續會提供預覽。  使用 [自動完成匯出時，不所需任何用戶端工具更新。  如果您選擇產生的檔案並匯入至內部部署伺服器，列於上方的工具更新所需成功匯入。


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>還原已刪除的 V11 資料庫的 V12

下列案例說明 V12 Azure SQL 資料庫伺服器上還原已刪除的 V11 Azure SQL 資料庫。

1. 假設您有 V11 Azure SQL 資料庫伺服器。 <br/> 在伺服器上您要在過時的 Web 商務服務層有一個資料庫。
2. 您要刪除的資料庫。
3. 您可以升級伺服器至 V12。
4. 下一步您還原資料庫伺服器。 <br/> 資料庫藉此升級為 V12，S0 層級的標準服務層。
5. 如果 S0 不是您的喜好設定，您可以切換到任何受支援的服務層，資料庫。


### <a name="powershell-cmdlets"></a>PowerShell cmdlet


PowerShell cmdlet 可啟動、 停止或監控升級至 Azure SQL 資料庫 V12，從 V11 或其他類型的前置 V12 版本。

- [使用 PowerShell 以 SQL 資料庫 V12 升級](sql-database-upgrade-server-powershell.md)

如需瞭解這些 PowerShell cmdlet 參照文件，請參閱︰


- [取得 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [開始 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [停止 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


取消、 暫停，就表示停駐點指令程式。 有任何繼續升級時，不是一次從頭開始。 停止指令程式會，然後釋放適當的所有資源。


## <a name="failure-resolution"></a>失敗解析度


如果在升級失敗任何奇數頁的原因，請 V11 資料庫會保持 active 和提供標準。


## <a name="related-links"></a>相關的連結


- Microsoft Azure[預覽功能](https://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1
