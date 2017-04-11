<properties 
    pageTitle="將資料從 Amazon 使用資料工廠簡單儲存服務移 |Microsoft Azure" 
    description="瞭解如何將資料從 Amazon 簡單儲存服務 (S3) 使用 Azure 資料工廠。" 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>將資料從 Amazon 簡單儲存服務使用 Azure 資料工廠移動

本文概述如何使用複製活動 Azure 資料工廠移動從 Amazon 簡單儲存服務 (S3) 到另一個資料儲存的資料。 本文是根據複製活動呈現資料移動的一般概觀和支援的來源接收資料存放區的清單[資料移動活動](data-factory-data-movement-activities.md)文件。  

資料工廠目前支援只移動資料從 Amazon S3 其他資料儲存區中，但無法將資料從其他資料儲存區到 Amazon S3。

## <a name="required-permissions"></a>必要的權限

若要從 Amazon S3 複製資料，請確定您已獲授予他人下方權限︰

- **s3:GetObject**和**s3:GetObjectVersion** Amazon S3 物件作業
- **s3:ListBucket**和**s3:ListAllMyBuckets** （用於只複製精靈） Amazon S3 連結作業 

您可以從[指定的權限原則中](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)尋找 Amazon S3 權限的詳細資料的完整清單。

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Amazon S3 複製最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 它會顯示您如何將資料複製到 Azure Blob 儲存體 Amazon S3。 不過，可以接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)的任何複製資料。

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>範例︰ 從 Amazon S3 資料複製到 Azure Blob
此範例會示範如何 Amazon S3 將資料複製到 Azure Blob 儲存體。 不過，資料可以複製**直接**至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  
 
樣本具有下列資料工廠實體︰

- 連結的類型[AwsAccessKey](#linked-service-properties)的服務。
- 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
- 輸入的[資料集](data-factory-create-datasets.md)的類型[AmazonS3](#dataset-type-properties)。
- 輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
- [管線](data-factory-create-pipelines.md)與使用[FileSystemSource](#copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從 Amazon S3 到 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

**Amazon S3 連結服務**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
            }
        }
    }

**Azure 連結的儲存服務**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Amazon S3 輸入資料集**

設定**「 外部 」: true**通知資料工廠服務資料集外部資料 factory，並不會所產生的資料工廠中的活動。 設定此屬性設為 true 上不會產生管道活動所輸入資料集。

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }



**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**含複製活動的管線**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **FileSystemSource** ，**接收**類型] 設定為**BlobSink**。 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>連結的服務的內容

下表提供 Amazon S3 JSON 項目的特定的描述 (**AwsAccessKey**) 連結服務。

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | 私人便捷鍵的識別碼。 | 字串 | [是] |
| secretAccessKey | 私人便捷鍵本身。 | 加密私人字串 | [是] | 


## <a name="dataset-type-properties"></a>資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性及原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 類型**AmazonS3** （包括 Amazon S3 資料集） 的資料集 typeProperties 區段有下列屬性

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------- | ------ | 
| bucketName | S3 值區名稱。 | 字串 | [是] |
| 索引鍵 | S3 物件索引鍵。 | 字串 | 無 | 
| 前置詞 | S3 物件索引鍵的前置字元。 選取其鍵開始此前置詞的物件。 適用於僅當機碼是空的。 | 字串 | 無 | 
| 版本 | 如果 S3 版本設定已啟用的 S3 物件的版本。 | 字串 | 無 |  
| 格式 | 支援下列格式類型︰ **TextFormat** **AvroFormat**、 **JsonFormat**、 **OrcFormat**、 **ParquetFormat**。 [格式] 下的 [**類型**] 屬性設其中一個值。 請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat)，以及[指定 ParquetFormat](#specifying-parquetformat)區段，如需詳細資訊。 如果您想要複製檔案另存為-是檔案式存放區之間 （二進位複本），您可以略過兩邊輸入與輸出資料集的定義中的 [格式] 區段。| 無
| 壓縮 | 指定類型和層級壓縮的資料。 支援的類型︰ **GZip**、**結實**，及**BZip2**和支援層級是︰ **Optimal**和**最快**。 目前，壓縮設定不支援**AvroFormat**或**OrcFormat**中的資料。 如需詳細資訊，請參閱[壓縮支援](#compression-support)一節。  | 無 |

> [AZURE.NOTE] bucketName + 鍵指定 S3 物件位置連結 S3 物件的根容器，而鍵會 S3 物件的完整路徑的位置。

### <a name="sample-dataset-with-prefix"></a>使用前稱謂的範例資料集

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>範例資料集 （含版本）

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>動態 S3 路徑

在範例中，我們會使用固定的值為索引鍵和 bucketName Amazon S3 資料集。 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

您可以讓資料工廠使用系統變數，例如 SliceStart 計算索引鍵和 bucketName 動態在執行階段。

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

您可以執行相同的 Amazon S3 資料集的 [前置字元] 屬性。 如需受支援的函數和變數的清單，請參閱[資料工廠函數和系統變數](data-factory-functions-variables.md)。 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 針對所有類型的活動有內容，例如名稱、 描述、 輸入與輸出資料表及原則。 

另一方面活動**typeProperties**一節中提供的屬性會隨著每一個活動類型。 複製活動，而有所不同的來源和接收類型。

當來源複製活動的類型**FileSystemSource** （包括 Amazon S3） 時，會提供下列屬性 typeProperties] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- | 
| 遞迴 | 指定是否要遞迴列出 S3 目錄下的物件。 | true/false | 無 | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。

## <a name="next-steps"></a>後續步驟
請參閱下列文章︰ 
- 如需逐步指示建立含複製活動的管線[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 
