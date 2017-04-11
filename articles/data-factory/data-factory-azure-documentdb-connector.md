<properties 
    pageTitle="將資料移到或從 DocumentDB |Microsoft Azure" 
    description="瞭解如何將資料移到或從使用 Azure 資料工廠 Azure DocumentDB 集合" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>移動資料 DocumentDB 使用 Azure 資料工廠

本文概述如何使用複製活動 Azure 資料工廠移動資料 Azure DocumentDB 從另一個資料儲存及將資料從 DocumentDB 移到另一個資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

下列範例會示範如何將資料複製 Azure DocumentDB 和 Azure Blob 儲存體。 不過，資料可以複製**直接**從任何來源至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  

> [AZURE.NOTE] 複製資料的上部署/Azure IaaS 資料會儲存至 Azure DocumentDB 反之亦然支援及資料管理閘道器 2.1 版與上方。

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>範例︰ 資料複製 DocumentDB 到 Azure Blob

下列範例顯示︰

1. 連結的類型[DocumentDb](#azure-documentdb-linked-service-properties)的服務。
2. 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。 
3. 輸入的[資料集](data-factory-create-datasets.md)的類型[DocumentDbCollection](#azure-documentdb-dataset-type-properties)。 
4. 輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4. [管線](data-factory-create-pipelines.md)與使用[DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例複製到 Azure Blob Azure DocumentDB 資料。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。

**Azure DocumentDB 連結服務︰**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob 儲存體連結服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure 文件 DB 輸入資料集︰**

範例假設您有集合命名 Azure DocumentDB 資料庫中的**人員**。
 
設定 「 外部 」: 「 true 」，並指定 externalData 原則 Azure 資料工廠服務的資訊表格外部資料 factory 不所產生的資料工廠中的活動。

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Azure Blob 輸出資料集︰**

資料會複製到新的 blob 反映的特定的日期時間的小時資料粒度 blob 的每小時的路徑。

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

範例 DocumentDB 資料庫中的人員集合中的 JSON 文件︰ 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB 支援透過階層式 JSON 文件中使用像語法的 SQL 查詢的文件。 

範例︰ 選取 Person.PersonId，Person.Name.First 另存新檔名字 Person.Name.Middle 為唯讀 Person.Name.Last 另存新檔姓氏在前的人

下列管線會將資料從 DocumentDB 資料庫中的人員集合到 Azure blob。 已指定為組件的複本活動輸入與輸出資料集。  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>範例︰ 從 Azure Blob 資料複製到 Azure DocumentDB

下列範例顯示︰

1. 連結的類型[DocumentDb](#azure-documentdb-linked-service-properties)的服務。
2. 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3. 輸入的[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4. 輸出[資料集](data-factory-create-datasets.md)的類型[DocumentDbCollection](#azure-documentdb-dataset-type-properties)。 
4. [管線](data-factory-create-pipelines.md)與使用[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)及[DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties)複製活動。


此範例會將資料從 Azure blob 至 Azure DocumentDB。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。

**Azure Blob 儲存體連結服務︰**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure DocumentDB 連結服務︰**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob 輸入資料集︰**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Azure DocumentDB 輸出資料集︰**

此範例會將資料複製到集合名為 「 人員 」。

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

下列管線會將資料從 Azure Blob DocumentDB 中的人員集合。 已指定為組件的複本活動輸入與輸出資料集。 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
如果範例 blob 輸入為 

    1,John,,Doe

然後輸出中 DocumentDB JSON 會為︰

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB 是 NoSQL 存放區 JSON 文件]，允許巢狀的結構的位置。 Azure 資料工廠可讓使用者，以表示階層**nestingSeparator**，也就是透過 「。 」 在此範例中。 分隔符號，以複製活動會產生 「 名稱 」 三個子項目物件第一次，中間名及姓氏的圖片，根據 「 Name.First 」、 「 Name.Middle 」 和 「 Name.Last 」 資料表定義。

## <a name="azure-documentdb-linked-service-properties"></a>Azure DocumentDB 連結服務的內容

下表提供 JSON 項目的特定 Azure DocumentDB 連結服務描述。 

| **屬性** | **描述** | **所需** |
| -------- | ----------- | --------- |
| 類型 | [類型] 屬性必須設定為︰ **DocumentDb** | [是] |
| 連接字串 | 指定連線至 Azure DocumentDB 資料庫所需的資訊。 | [是] |

## <a name="azure-documentdb-dataset-type-properties"></a>Azure DocumentDB 資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)的文件。 所有的資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。) 區段，例如結構、 可用性和資料集 JSON 原則類似。
 
TypeProperties 區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 TypeProperties 資料集的區段類型**DocumentDbCollection**有下列屬性。

| **屬性** | **描述** | **所需** |
| -------- | ----------- | -------- |
| collectionName | DocumentDB 文件集合的名稱。 | [是] |


範例︰

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>資料工廠的結構描述
例如 DocumentDB 沒有結構描述的資料儲存區的資料工廠服務會以下列方法的其中一推斷的結構描述︰  

1.  如果您使用 [**結構**] 屬性中的資料集定義] 指定資料的結構資料工廠服務就會接受此結構和結構描述。 在此情況下，如果資料列不包含資料行的值，它將會提供 null 值。
2.  如果您使用 [**結構**] 屬性中的資料集定義未指定的資料結構，資料工廠服務會使用第一列在資料推斷的結構描述。 在此情況下，如果第一列不包含完整的結構描述，某些資料欄中會缺少複製作業的結果。

因此，沒有結構描述的資料來源的最佳作法是指定的結構，使用 [**結構**] 屬性的資料。

## <a name="azure-documentdb-copy-activity-type-properties"></a>Azure DocumentDB 複製活動類型的屬性

節與屬性可用於定義活動的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。
 
**附註︰**複製活動會只有一個輸入，而產生單一輸出。

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型，然後複製活動的情況下，而有所不同的來源和接收類型。

若複製活動時來源類型**DocumentDbCollectionSource**的下列屬性會出現在**typeProperties** ] 區段中︰

| **屬性** | **描述** | **允許的值** | **所需** |
| ------------ | --------------- | ------------------ | ------------ |
| 查詢 | 指定要讀取資料的查詢。 | 查詢字串 DocumentDB 支援。 <br/><br/>範例︰`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | 無 <br/><br/>如果未指定 SQL 陳述式的執行︰`select <columns defined in structure> from mycollection` 
| nestingSeparator | 表示文件巢狀結構的特殊字元 | 任何字元。 <br/><br/>DocumentDB 是 NoSQL 存放區 JSON 文件]，允許巢狀的結構的位置。 Azure 資料工廠可讓使用者，以表示階層 nestingSeparator，也就是透過 「。 」 在上述範例。 分隔符號，以複製活動會產生 「 名稱 」 三個子項目物件第一次，中間名及姓氏的圖片，根據 「 Name.First 」、 「 Name.Middle 」 和 「 Name.Last 」 資料表定義。 | 無

**DocumentDbCollectionSink**支援下列屬性︰

| **屬性** | **描述** | **允許的值** | **所需** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | 需要來源資料行名稱，指出該巢狀的文件中的特殊字元。 <br/><br/>上方，例如︰`Name.First`輸出中表格會產生下列 JSON 結構 DocumentDB 文件中︰<br/><br/>「 名稱 」: {<br/>  「 第一個 「: 「 名字 」<br/>}, | 用於分隔巢狀層級的字元。<br/><br/>預設值是`.`（點）。 | 用於分隔巢狀層級的字元。 <br/><br/>預設值是`.`（點）。 | 無 | 
| writeBatchSize | 平行要求 DocumentDB 服務，來建立文件數。<br/><br/>使用此屬性複製從 DocumentDB 資料時，您可以微調效能。 當您增加 writeBatchSize，因為傳送 DocumentDB 平行要求，您可以預期更佳的效能。 不過您必須避免節流的會引發錯誤訊息: 「 要求工資率很大 」。<br/><br/>節流設定會決定數個等各種因素包括文件，在文件中的字詞的數字的大小、 編製索引的目標集合等原則。複製作業，您可以使用更好的集合 (例如 S3) 有最處理量可用 （2500 要求單位秒）。 | 整數 | 不 (預設︰ 10000) |
| writeBatchTimeout | 等待的時間供作業完成逾時。 | 時段<br/><br/> 範例: 「 00: 30:00 」 （30 分鐘）。 | 無 |
 
## <a name="appendix"></a>附錄
1. **問題︰**  
   會複製活動支援的更新現有記錄？

    **Answer （回應):**  
    [否]。

2. **問題︰**  
   如何是否已複製到 DocumentDB 處理的重試複製記錄？

    **Answer （回應):**  
   如果記錄具有 「 識別碼 」 欄位，複製操作嘗試插入一筆記錄的相同的識別碼，[複製] 作業會擲回錯誤。  
 
3. **問題︰** 
   資料工廠支援[範圍或雜湊為基礎的資料分割](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)嗎？ 

    **Answer （回應):** 
    [否]。 
4. **問題︰** 
   指定多個表格的 DocumentDB 集合？
    
    **Answer （回應):** 
    [否]。 此時，可以指定只有一個集合。
     
## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。
