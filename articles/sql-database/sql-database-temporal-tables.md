<properties
   pageTitle="快速入門 Azure SQL 資料庫中的暫時資料表 |Microsoft Azure"
   description="瞭解如何開始使用 Azure SQL 資料庫中的暫時表格。"
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
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>快速入門暫時 Azure SQL 資料庫中的資料表

暫時資料表是程式設計的新功能，可讓您追蹤及分析資料，而不需要使用自訂程式碼中變更的完整記錄的 Azure SQL 資料庫。 暫時資料表保留資料以便儲存的都可能會解讀密切相關的時間內容為有效只在特定期間內。 此屬性的暫時資料表可以有效率以時間為基礎的分析和從資料功用取得深入資訊。

##<a name="temporal-scenario"></a>暫時案例

本文將說明在應用程式的案例中利用暫時資料表的步驟。 假設您想要追蹤使用者活動，或您想要的使用者活動分析擴充的現有網站上從頭開發的新網站。 在此簡化的範例中，我們假設瀏覽網頁時一段時間的數字是需要擷取及監視網站資料庫是裝載於 Azure SQL 資料庫中的標記。 使用者活動的歷史分析的目標是要取得輸入重新設計網站，並提供訪客更好的體驗。

這種情況的資料庫模型是非常簡單，因此使用者活動公制表示使用單一整數欄位， **PageVisited**，以及使用者設定檔的基本資訊擷取。 此外，時間分析您想將一系列的資料列的每個使用者，其中的每一列代表的特定使用者瀏覽過的特定期間內的頁數。

![結構描述](./media/sql-database-temporal-tables/AzureTemporal1.png)

所幸，您不需要進行任何努力維護此活動資訊應用程式中。 暫時表格，此程序自動化-可讓您將焦點集中在本身的資料分析的 [網站設計和更多時間期間的完整彈性。 您必須執行是以確保**WebSiteInfo**資料表已被設定為[暫時系統版本](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0)。 在這個案例中利用暫時資料表的確切步驟如下所述。

##<a name="step-1-configure-tables-as-temporal"></a>步驟 1︰ 設定為暫時的表格

根據您是從開始新的開發或升級現有的應用程式，您會建立暫時資料表，或修改現有的藉由新增暫時屬性。 在 [一般情況下，您的狀況可以是這兩個選項的。 執行下列動作，使用[SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS)， [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) 或任何其他 TRANSACT-SQL 開發工具。


> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


###<a name="create-new-table"></a>建立新資料表

使用 SSMS 物件總管] 中的內容功能表項目 」 新的版本系統資料表]，以開啟查詢編輯器] 中的暫時表格範本指令碼，然後使用 「 指定值的範本參數 」 (Ctrl + Shift + M) 填入範本︰

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

將新項目新增至資料庫專案時，請在 SSDT，選擇 「 暫時表格 （系統建立版本） 」 範本。 會開啟 [資料表設計工具並讓您輕鬆地指定 [表格版面配置︰

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

您也可以建立暫時表格直接，指定 SQL 陳述式，在下面的範例所示。 請注意，每個暫時資料表的必要項目期間的定義和 SYSTEM_VERSIONING 子句的另一個使用者資料表，將儲存的版本歷程記錄的列參考︰

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

當您建立系統建立版本的暫時表格時，會自動建立的預設設定的歷程記錄表格。 預設記錄資料表啟用頁面壓縮包含叢集的 B 樹狀目錄索引期間的資料行 （結束，開始）。 此設定是大部分的案例中暫時資料表用於，尤其是[稽核資料](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0)的最佳選擇。 

在此案例中，我們的目標執行以時間為基礎的趨勢分析，在較長的資料歷程記錄與較大的資料集，記錄資料表的儲存空間選擇為您的直的 columnstore 索引。 群組直的 columnstore 提供很好的壓縮和分析查詢的效能。 暫時表格提供的彈性，完全獨立地設定目前和暫時資料表上的索引。 

**附註**︰ Columnstore 索引中才有進階版服務層級。

下列指令碼會顯示如何在歷程記錄] 表格的預設索引可以變更為直 columnstore:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

暫時表格在物件總管] 中，以更容易識別] 中的特定圖示表示時做為子節點顯示其歷程記錄的資料表。

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>若要暫時變更現有資料表

讓我們來覆蓋替代的案例中 WebsiteUserInfo 資料表已經存在，但不設計用來將變更的歷程記錄。 在此情況下，您可以只需要延伸現有的資料表，成為暫時，如下列範例所示︰

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>步驟 2︰ 執行您的工作量定期

暫時資料表的主要優點是您不需要變更，或調整您的網站，以任何方式執行追蹤修訂。 建立後，暫時資料表無障礙會保留舊版的列，每次執行修改資料。 

以運用自動變更追蹤此特定案例中，我們只欄**PagesVisited**每次更新使用者結束 her/his 在網站上的工作階段時︰

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

請務必請注意，不需要更新查詢實際作業發生時，也無法歷程記錄的資料會保留供未來分析如何知道精確的時間。 Azure SQL 資料庫會自動處理兩個部分。 下圖說明如何在每次更新產生歷程記錄的資料。

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>步驟 3︰ 執行歷程記錄的資料分析

現在暫時系統版本設定已啟用，歷程記錄的資料分析時，只要離開您的查詢。 本文中，我們會提供一些範例的地址常見分析案例-若要瞭解所有的詳細資訊，請瀏覽介紹[FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)具有各種不同的選項。

若要查看的網頁瀏覽到前一個小時數排序前 10 個使用者，請執行此查詢︰

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

您可以輕鬆修改此查詢來分析網站瀏覽到前一天的前一個月或在您想在過去的任何點。

若要執行基本的統計分析的前一天，請使用下列範例︰

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

若要搜尋特定的使用者的活動中一段時間，使用包含 IN 子句︰

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

您可以趨勢及模式中顯示直覺方式很容易圖形的視覺效果為特別方便暫時查詢︰

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>不斷發展的資料表結構描述

一般而言，您必須變更正在執行應用程式開發暫時資料表結構描述。 只要執行一般 ALTER TABLE 陳述式和 Azure SQL 資料庫將適當地變更傳播至記錄的資料表。 下列指令碼會顯示如何新增追蹤的其他屬性︰

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

同樣地，您可以在您的工作量作用中時，變更資料行定義︰

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

最後，您可以移除不不再需要的資料行。

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
若要變更的暫時資料表結構描述您在連線至資料庫 （線上模式） 或資料庫專案 （離線模式） 的一部分，或者，使用最新[SSDT](https://msdn.microsoft.com/library/mt204009.aspx) 。

##<a name="controlling-retention-of-historical-data"></a>控制保留歷程資料

使用系統建立版本的暫時表格，記錄資料表可能會增加資料庫大小，多個一般的資料表。 大型和不斷歷程記錄表格可成為由於純粹儲存空間的成本，以及意效能稅的暫時查詢的問題。 因此，開發管理記錄資料表中的資料的資料保留原則是重要的規劃及管理每個暫時資料表的生命週期。 Azure SQL 資料庫，您有下列方式來管理歷史暫時資料表中的資料︰

- [分割表格](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [自訂清除指令碼](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>後續步驟

暫時表格的詳細資訊，請參閱[MSDN 文件](https://msdn.microsoft.com/library/dn935015.aspx)。
請造訪聆聽[實際的客戶暫時實作成功的故事](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)及觀看[live 暫時示範](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)頻道 9。
