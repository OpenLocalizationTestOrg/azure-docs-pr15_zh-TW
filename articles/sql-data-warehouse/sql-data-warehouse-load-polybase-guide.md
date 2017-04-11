<properties
   pageTitle="使用 SQL Data Warehouse PolyBase 指南 |Microsoft Azure"
   description="指導方針，使用 PolyBase 在 SQL Data Warehouse 案例中的建議。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>使用 SQL Data Warehouse PolyBase 指南

本指南可提供的 SQL Data Warehouse 中使用 PolyBase 實用的資訊。

若要開始，請參閱[使用 PolyBase 載入資料][]的教學課程。


## <a name="rotating-storage-keys"></a>旋轉儲存金鑰

不時您要變更您的 blob 儲存體基於安全性理由便捷鍵。

若要執行這項工作的最佳方式是依照稱為 「 旋轉按鍵 」 的程序。 您可能已經注意到您 blob 儲存體帳戶有兩個儲存索引鍵。 如此一來，您可以轉換

旋轉 Azure 儲存體帳戶索引鍵是簡單的三個步驟的程序

1. 建立第二個範圍的資料庫認證根據次要儲存便捷鍵
2. 建立第二個外部資料來源] 以關閉此新的認證
3. 卸除，並建立外部指向新的外部資料來源的資料表。

當您有移轉所有您外部資料表至新的外部資料來源，然後您可以執行清除工作︰

1. 卸除第一份外部資料來源
2. 首的第一個資料庫範圍主要儲存便捷鍵為根據的認證
3. 登入 Azure 並重新產生準備好進行下一次主要便捷鍵

## <a name="query-azure-blob-storage-data"></a>查詢 Azure blob 儲存體資料
外部資料表查詢只使用資料表名稱，如同它是關聯式表格。

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] 外部資料表查詢，可能會失敗*」 查詢中止-最大值拒絕閾值時從外部來源的讀取達到 」*錯誤。 這表示您的外部資料，包含*已變更*的記錄。 資料記錄會被視為 「 變更 」，如果實際的資料類型/欄數不相符的外部資料表的資料行定義，或如果資料不符合指定的外部檔案格式。 若要修正此問題，請確定您的外部表格與外部的檔案格式定義正確，並這些定義是否符合您的外部資料。 萬一外部資料的記錄子集合有問題，您可以選擇拒絕這些記錄查詢中建立外部表格 DDL 使用 [拒絕] 選項。


## <a name="load-data-from-azure-blob-storage"></a>從 Azure blob 儲存體載入資料
此範例會從 Azure blob 儲存體資料載入到 SQL Data Warehouse 資料庫。

直接儲存資料中移除查詢的資料傳輸時間。 儲存 columnstore 索引的資料可提升最多 10 個 x 的分析查詢的查詢效能。

此範例會使用載入資料建立表格另存新檔 SELECT 陳述式。 新的資料表繼承具名查詢中的資料行。 從外部表格定義繼承這些資料行的資料類型。

建立資料表另存新檔選取是高度效能 SQL 陳述式載入平行您 SQL Data Warehouse 的所有運算節點中的資料。  它的原始開發分析平台系統中的人連線平行處理 (MPP) 引擎並現已在 SQL Data Warehouse。

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

請參閱[建立表格另存新檔選取 (TRANSACT-SQL)][]。

## <a name="create-statistics-on-newly-loaded-data"></a>建立新載入資料的統計資料

Azure SQL Data Warehouse 尚未支援自動建立，或自動更新統計資料。  若要取得最佳效能，從您的查詢，請務必所有資料表的所有資料行建立統計資料之後第一個載入, 或出現在資料中的任何大幅變更。  統計資料的詳細說明，請參閱[統計資料][]主題開發] 群組中的主題。  以下是如何建立資料表的統計資料載入在此範例中的簡單範例。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>將資料匯出至 Azure blob 儲存體
本節說明如何從 SQL Data Warehouse 匯出資料至 Azure blob 儲存體。 此範例使用建立外部表格另存新檔選取 [這是高度效能 SQL 陳述式從所有運算節點匯出平行中的資料。

下列範例會建立使用資料行定義和 dbo 資料的外部表格 Weblogs2014。網誌資料表。 外部表格定義會儲存在 SQL Data Warehouse，匯出 」 / 封存/log2014 / 」 下目錄指定資料來源的 blob 容器的 SELECT 陳述式的結果。 匯出資料的指定的文字檔案格式。

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>解決 PolyBase utf-8 需求
在簡報 PolyBase 支援載入已 utf-8 編碼的資料檔案。 Utf-8 使用相同的字元編碼方式為 ASCII PolyBase 會也 ASCII 編碼的支援載入資料。 不過，PolyBase 不支援其他字元編碼，例如 utf-16 / Unicode 或擴充 ASCII 字元。 延伸的 ASCII 包括重音符號，例如這是常見的德文的母音變化記號的字元。

若要解決此需求得到最佳解答是重新寫入到 utf-8 編碼。

有數種方式可執行此動作。 以下是使用 Powershell 的兩種方法︰

### <a name="simple-example-for-small-files"></a>簡單範例小的檔案

以下是簡易一行建立檔案的 Powershell 指令碼。

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

不過，而這是一個簡單的方法，重新編碼資料是不是最有效。 下面這個 io 串流範例，更快速，而達成相同的結果。

### <a name="io-streaming-example-for-larger-files"></a>IO 串流範例較大的檔案

以下是較為複雜，但時，會傳送到目標其來源的資料列會更有效率。 使用此方法在較大的檔案。

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>後續步驟
若要進一步瞭解如何將資料移到 SQL Data Warehouse，請參閱[資料移轉概觀][]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[載入 PolyBase 的資料]: ./sql-data-warehouse-get-started-load-with-polybase.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[資料移轉概觀]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[建立外部的檔案格式 (TRANSACT-SQL)]: https://msdn.microsoft.com/library/dn935026).aspx [建立外部表格 (TRANSACT-SQL)]: https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[建立表格另存新檔選取 (TRANSACT-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
