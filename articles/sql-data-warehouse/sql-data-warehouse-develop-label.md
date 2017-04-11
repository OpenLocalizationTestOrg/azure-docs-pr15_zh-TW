<properties
   pageTitle="使用 SQL Data Warehouse 樂器查詢的標籤 |Microsoft Azure"
   description="使用標籤樂器查詢中 Azure SQL Data Warehouse 開發解決方案的秘訣。"
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>使用 SQL Data Warehouse 樂器查詢的標籤
SQL Data Warehouse 支援稱為查詢標籤的概念。 之前移到任何深度現在就讓我們看看範例，其中一個︰

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

此最後一行標籤至查詢的字串 「 我的查詢標籤 」。 查詢可透過 Dmv 標籤現狀，這是特別有用。 這會提供我們以追蹤問題查詢並協助您識別 ETL 執行的進行機制。

以下是真正幫助很好的命名慣例。 類似，例如 「 專案︰ 程序︰ 陳述式︰ 註解 ' 協助唯一識別出在來源控制項中的所有程式碼中的查詢。

若要搜尋的標籤中，您可以使用下列查詢，使用動態管理檢視︰

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] 請務必查詢時，您將方括號或雙引號括住的文字標籤。 標籤是保留的字，並會造成的錯誤，如果未分隔。


## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
