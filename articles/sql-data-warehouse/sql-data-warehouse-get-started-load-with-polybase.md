<properties
   pageTitle="在教學課程中的資料倉庫 SQL PolyBase |Microsoft Azure"
   description="瞭解 PolyBase 功能，以及如何使用它倉儲案例的資料。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>在 SQL Data Warehouse PolyBase 載入資料

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [資料工廠](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)

本教學課程中會顯示如何使用 AzCopy 和 PolyBase 將資料載入 SQL Data Warehouse。 完成後，您就會知道如何︰

- 使用 AzCopy 將資料複製到 Azure blob 儲存體
- 建立資料庫物件，以定義的資料
- 執行的資料載入 T SQL 查詢

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>必要條件

若要在逐步執行本教學課程中，您需要

- SQL Data Warehouse 資料庫。
- Azure 儲存的帳戶類型標準本機多餘的儲存 (標準-LRS) 標準地理多餘儲存空間 (標準-GRS)] 或 [標準的讀取權限地理多餘儲存空間 (標準 RAGRS)。
- AzCopy 命令列公用程式。 下載並安裝[最新版本的 AzCopy][]這與 Microsoft Azure 儲存工具安裝。

    ![Azure 儲存工具](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>步驟 1︰ 新增範例資料至 Azure blob 儲存體

若要載入資料，我們要放 Azure blob 儲存體中的一些範例資料。 在此步驟中，我們會填入 Azure 儲存體 blob，含有範例資料。 更新版本中，我們將使用 PolyBase，此範例資料載入 SQL Data Warehouse 資料庫。

### <a name="a-prepare-a-sample-text-file"></a>答︰ 準備的範例文字檔案

準備的範例文字檔案︰

1. 開啟 [記事本]，然後將下列行的資料複製到新的檔案。 將儲存此 %temp%\dimdate2.txt 為您的本機 temp 目錄。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B。 尋找您 blob 服務端點

若要尋找您 blob 服務端點︰

1. 從 Azure 入口網站上，選取 [**瀏覽** > **儲存帳戶**。
2. 按一下您想要使用的儲存空間帳戶。
3. 在儲存帳戶刀中，按一下 [二進位大型物件

    ![按一下 [二進位大型物件](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. 將您 blob 服務端點 URL 儲存以供下次使用。

    ![Blob 服務端點](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C。 尋找金鑰 Azure 儲存體

若要尋找您的 Azure 儲存金鑰︰

1. 從 Azure] 入口網站中，選取 [**瀏覽** > **儲存帳戶**。
2. 按一下您想要使用的儲存空間帳戶。
3. 選取 [**所有設定** > **便捷鍵**。
4. 按一下其中一個您便捷鍵複製到剪貼簿的 [副本] 方塊。

    ![複製 Azure 儲存機碼](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D 鍵。 將範例檔案複製到 Azure blob 儲存體

若要將資料複製到 Azure blob 儲存體中︰

1. 開啟命令提示字元，並變更目錄 AzCopy 安裝目錄。 這個命令會變更為預設的 64 位元 Windows 用戶端上安裝目錄。

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. 執行下列命令以上傳檔案。 指定您 blob 服務端點 URL<blob service endpoint URL>和 < azure_storage_account_key > 您 Azure 儲存體帳戶金鑰。

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

另請參閱[快速入門 AzCopy 命令列公用程式][]。

### <a name="e-explore-your-blob-storage-container"></a>E。 探索您 blob 儲存容器

若要查看檔案您上傳到 blob 儲存體︰

1. 回到您 Blob 服務刀。
2. 在容器，連按兩下**datacontainer**。
3. 若要瀏覽至您的資料路徑按一下資料夾**datedimension** ，您會看到您上傳的檔案**DimDate2.txt**。
4. 若要檢視內容，請按一下**DimDate2.txt**。
5. 請注意，Blob 屬性刀中，您可以下載或刪除檔案。

    ![檢視儲存 Azure blob](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>步驟 2︰ 建立外部表格的範例資料

此區段中，我們會建立外部定義的範例資料的資料表。

PolyBase 會使用外部表格來存取 Azure blob 儲存體中的資料。 資料不會儲存在 SQL Data Warehouse，因為 PolyBase 處理外部資料驗證使用資料庫範圍的認證。

在此步驟中的範例會使用下列 SQL 陳述式來建立外部表格。

- 若要將加密的資料庫密碼[建立主索引鍵 (SQL)][]範圍認證。
- 若要指定 Azure 儲存體帳戶驗證資訊[建立資料庫範圍認證 (SQL)][] 。
- [建立外部資料來源 (SQL)][] ，以指定您 Azure blob 儲存體的位置。
- 若要指定資料的格式[建立外部檔案格式 (SQL)][] 。
- [建立外部表格 (SQL)][] ，以指定的表格定義和資料的位置。

針對 SQL Data Warehouse 資料庫執行這個查詢。 它會建立外部中名為 「 DimDate2External dbo 結構描述指向 Azure blob 儲存體中 DimDate2.txt 範例資料的資料表。


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


SQL Server 物件總管 Visual Studio 中，您可以看到外部檔案格式、 外部資料來源，以及 DimDate2External 表格。

![檢視外部表格](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>步驟 3︰ 將資料載入 SQL Data Warehouse

外部表格建立之後，您可以將資料載入到新的資料表，或將它插入現有的資料表。

- 若要將資料載入到新的資料表中，執行[建立資料表另存新檔選取 (SQL)][]陳述式。 新的資料表必須具名查詢中的資料行。 外部表格定義中的資料類型相符的資料行的資料類型。
- 若要將資料載入到現有的資料表，使用[插入...選取 (SQL)][]陳述式。

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>步驟 4︰ 建立新載入資料的統計資料

SQL Data Warehouse 不會自動建立或自動更新統計資料。 因此，若要達到高的查詢效能，很重要建立每個資料表的每一欄的第一個負載之後統計資料。 也很重要更新 [統計資料後大量資料中的變更。

此範例會建立新的 DimDate2 資料表單一資料行的統計資料。

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

若要深入瞭解，請參閱[統計資料][]。  


## <a name="next-steps"></a>後續步驟
請參閱[PolyBase 指南][]如需進一步瞭解您應該知道在開發使用 PolyBase 的解決方案。

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[PolyBase 指南]: ./sql-data-warehouse-load-polybase-guide.md
[快速入門 AzCopy 命令列公用程式]: ../storage/storage-use-azcopy.md
[最新版本的 AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[建立外部資料來源 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[建立外部的檔案格式 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[建立外部表格 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[建立表格另存新檔選取 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[插入...選取 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[建立主索引鍵 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[建立資料庫 SCOPED 認證 (TRANSACT-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
