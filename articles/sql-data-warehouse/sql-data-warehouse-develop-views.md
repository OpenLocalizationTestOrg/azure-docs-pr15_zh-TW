<properties
   pageTitle="檢視的 SQL Data Warehouse |Microsoft Azure"
   description="使用 TRANSACT-SQL 中的檢視 Azure SQL Data Warehouse 開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>SQL Data Warehouse 中的檢視

在 SQL Data Warehouse 檢視都特別有用。 他們可以用於數種不同的方式來改善您的方案的品質。  本文會醒目提示豐富您的方案與檢視，以及需要考慮的限制的一些的範例。

> [AZURE.NOTE] 語法`CREATE VIEW`不本文中所討論。 請參閱 MSDN 上這項參考資訊[建立檢視][]文件。

## <a name="architectural-abstraction"></a>建築抽象
常見的應用程式模式是重新建立使用建立表格另存新檔選取 (CTAS) 後面接著物件重新命名模式，而載入資料的資料表。

下列範例會將新的日期記錄新增 date 維度。 請注意新 tabble，DimDate_New，如何是第一次建立，然後重新命名為取代表格的原始版本。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

不過，這種方法可以產生資料表出現，並從使用者的檢視，以及 「 資料表不存在 」 錯誤訊息消失。 檢視可為使用者提供一致的簡報圖層，而基礎物件會重新命名。 為使用者提供透過檢視資料的存取權，就表示使用者不需要的基礎資料表可見性。 確保資料倉儲設計者可以發展資料模型和效能最大化資料載入程序期間使用 CTAS 時，這會提供一致的使用者體驗。    

## <a name="performance-optimization"></a>效能最佳化
檢視您也可以利用強制執行最佳化效能資料表之間的連接。 例如，檢視可以加入多餘的通訊群組索引鍵屬於聯結的準則，以最小化移動資料。  若要強制特定的查詢或聯結的提示，可能是檢視的另一個優點。 使用檢視，以這種方式可確保聯結會永遠執行免除使用者記住其聯結正確架構的最佳方式。

## <a name="limitations"></a>限制
檢視的 SQL Data Warehouse 是只中繼資料。  因此不提供下列選項︰

-   沒有結構描述繫結] 選項
-   無法更新基底表格，透過檢視
-   暫存資料表上無法建立檢視
-   不支援的 [展開 / NOEXPAND 提示
-   在 SQL Data Warehouse 沒有索引的檢視


## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。
針對`CREATE VIEW`語法，請參閱[建立檢視][]。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse 開發概觀]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[建立檢視]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
