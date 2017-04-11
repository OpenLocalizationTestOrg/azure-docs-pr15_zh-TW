<properties
   pageTitle="從 Azure blob 儲存體載入 Azure SQL Data Warehouse （Azure 資料工廠） |Microsoft Azure"
   description="瞭解如何載入 Azure 資料工廠的資料"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-from-azure-blob-storage-into-azure-sql-data-warehouse-azure-data-factory"></a>從 Azure blob 儲存體中載入 Azure SQL Data Warehouse （Azure 資料工廠） 的資料

> [AZURE.SELECTOR]
- [資料工廠](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 本教學課程中會顯示如何建立管線中，將資料從 Azure 儲存體 Blob SQL Data Warehouse Azure 資料工廠。 您會使用下列步驟︰

+ 設定中 Azure 儲存體 Blob 的範例資料。
+ 連線至 Azure 資料工廠的資源。
+ 建立管線若要將資料移 SQL Data Warehouse Blob 儲存體。

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>開始之前

自我熟悉 Azure 資料工廠，請參閱[Azure 資料工廠簡介][]。

### <a name="create-or-identify-resources"></a>建立或識別資源

在開始之前本教學課程中，您需要有下列資源。

   + **Azure 儲存體 Blob**︰ 本教學課程中使用的是 Azure 儲存體 Blob 做為資料來源的 Azure 資料工廠管道的郵件，且，因此您必須具備一個可用來儲存的範例資料。 如果您沒有，瞭解如何[建立儲存的帳戶][]。

   + **SQL Data Warehouse**︰ 本教學課程中將資料從 Azure 儲存體 Blob 移到 SQL Data Warehouse 和，因此需要資料倉庫線上載入 AdventureWorksDW 範例資料。 如果您還沒有資料倉庫，瞭解如何[佈建一][Create a SQL Data Warehouse]。 如果您有資料倉庫，但未提供的範例資料，您可以[手動載入][Load sample data into SQL Data Warehouse]。

   + **Azure 資料工廠**︰ Azure 資料工廠會完成的實際負載和，因此您必須具備一個可供您建立資料移動管道的郵件。如果您沒有，瞭解如何[開始使用 Azure 資料工廠 （資料工廠編輯器）][]的步驟 1 中建立。

   + **AZCopy**︰ 若要將本機的用戶端中的範例資料複製到您 Azure 儲存體 Blob AZCopy 必須。 如需安裝指示，請參閱[AZCopy 文件][]。

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>步驟 1︰ 將範例資料複製到 Azure 儲存體 Blob

一旦您有所有好的部分，您準備好將範例資料複製到您 Azure 儲存體 Blob。

1. [下載範例資料][]。 此資料會將三年的銷售資料加入 AdventureWorksDW 範例資料。

2. 使用此 AZCopy 命令，將三年的資料複製到您 Azure 儲存體 Blob。

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>步驟 2︰ 連線至 Azure 資料工廠的資源

資料是依位置我們可以建立，將資料從 Azure blob 儲存體移到 SQL Data Warehouse Azure 資料工廠管道的郵件。

若要開始，開啟[Azure 入口網站][]，然後從左側功能表中選取您資料的工廠。

### <a name="step-21-create-linked-service"></a>步驟 2.1︰ 建立連結的服務

連結您資料的工廠您 Azure 儲存體帳戶和 SQL Data Warehouse。  

1. 首先，您資料的工廠的 [連結的服務] 區段，即可開始註冊程序，然後按一下 [「 新的資料存放 」。 選擇一個名稱，以註冊 azure 底下，選取 Azure 儲存體儲存空間，為您的類型，然後輸入 [客戶名稱] 和 [帳戶金鑰]。

2. 登錄 SQL Data Warehouse 瀏覽至 [撰寫及部署] 區段中，選取 [新增資料存放區 」，然後 ' Azure SQL Data Warehouse 」。 複製並貼上此範本中，然後填入您的特定資訊。

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### <a name="step-22-define-the-dataset"></a>步驟 2.2︰ 定義資料集

之後建立連結的服務，我們會有定義資料集。  以下這表示定義會從您的儲存空間移到您的資料倉庫資料的結構。  您可以閱讀更多關於建立

1. 瀏覽至您的資料工廠的 [作者及部署] 區段中開始此程序。

2. 按一下 [新資料集]，然後 'Azure Blob 儲存體] 連結至您的資料工廠的儲存空間。  您可以使用以下 Azure Blob 儲存體中定義資料的指令碼︰

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


3. 現在我們也會定義 SQL Data Warehouse 資料集。  我們開始以相同的方式，按一下 [新資料集]，然後 ' Azure SQL Data Warehouse 」。

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## <a name="step-3-create-and-run-your-pipeline"></a>步驟 3︰ 建立和執行您的管線

最後，我們將會設定並執行管線 Azure 資料工廠。  這是將完成的實際資料移動作業。  您可以尋找完整的作業，您可以使用 SQL Data Warehouse 和 Azure 資料工廠完成檢視[以下][Move data to and from Azure SQL Data Warehouse using Azure Data Factory]。

在 [撰寫及部署] 區段中立即按一下 [其他命令]，然後 「 新的管線 」。  建立管線之後，您可以使用以下程式碼，將資料傳送至您的資料倉庫︰

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請先檢視︰

- [Azure 資料工廠學習路徑][]。
- [Azure SQL 資料倉儲連接器][]。 這是專為使用 Azure SQL Data Warehouse Azure 資料工廠的核心參考主題。


下列主題提供 Azure 資料工廠的詳細的資訊。 他們討論 Azure SQL 資料庫或 HDinsight，但資訊也適用於 Azure SQL Data Warehouse。

- [教學課程︰ 開始使用 Azure 資料工廠][]這是以處理資料與 Azure 資料工廠核心教學課程。 在本教學課程中，您會建立您使用 HDInsight 轉換及分析網頁記錄檔，每個月的第一個管線。 請注意，在此教學課程中都沒有複製活動。
- [教學課程︰ 資料複製到 Azure SQL 資料庫 Azure 儲存體 Blob][]。 在本教學課程中，您將建立在 Azure 資料工廠，將資料複製到 Azure SQL 資料庫 Azure 儲存體 Blob 的管線。


<!--Image references-->

<!--Article references-->
[AZCopy 文件]: ../storage/storage-use-azcopy.md
[Azure SQL 資料倉庫連接器]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[建立儲存的帳戶]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[開始使用 Azure 資料工廠 （資料工廠編輯器）]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Azure 資料工廠簡介]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[教學課程︰ 將資料從 Azure 儲存體 Blob 複製到 Azure SQL 資料庫]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[教學課程︰ 開始使用 Azure 資料工廠]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure 資料工廠學習路徑]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure 入口網站]: https://portal.azure.com
[下載範例資料]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
