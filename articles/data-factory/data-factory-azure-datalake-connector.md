<properties
    pageTitle="將資料移到或從 Azure 資料湖存放 |Azure 資料工廠"
    description="瞭解如何將資料從 Azure 資料湖存放區使用 Azure 資料工廠"
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
    ms.date="09/27/2016"
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>從 Azure 資料湖存放區使用 Azure 資料工廠移動資料
本文概述如何使用複製活動 Azure 資料工廠移動資料從/至 Azure 資料湖存放區，從其他資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

> [AZURE.NOTE]
> 建立管線與將資料從 Azure 資料湖市集移至複製活動之前，先建立 Azure 資料湖存放帳戶。 若要瞭解 Azure 資料湖存放區，請參閱[開始使用 Azure 資料湖存放區](../data-lake-store/data-lake-store-get-started-portal.md)。
>  
> 檢閱[建立您的第一個管線教學課程](data-factory-build-your-first-pipeline.md)以瞭解詳細步驟，建立資料工廠、 連結的服務、 資料集，以及管線。 使用 JSON 片段資料工廠編輯器或 Visual Studio PowerShell 的 Azure 來建立資料工廠實體。

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Azure 資料湖存放複製最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製 Azure 資料湖存放和 Azure Blob 儲存體。 不過，資料可以複製**直接**從任何來源至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。  


## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>範例︰ 從 Azure Blob 複製資料至 Azure 資料湖存放區
下列範例顯示︰

1.  連結的類型[AzureStorage](#azure-storage-linked-service-properties)的服務。
2.  連結的類型[AzureDataLakeStore](#azure-data-lake-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[AzureBlob](#azure-blob-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureDataLakeStore](#azure-data-lake-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[BlobSource](#azure-blob-copy-activity-type-properties)和[AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties)複製活動。

範例將時間序列資料從 Azure Blob 儲存體 Azure 資料湖存放區的每個小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。


**Azure 連結的儲存空間服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure 資料湖連結服務︰**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### <a name="to-create-azure-data-lake-linked-service-using-data-factory-editor"></a>若要建立 Azure 資料湖連結服務使用資料工廠編輯器
下列程序提供步驟建立使用資料工廠編輯器 Azure 資料湖存放連結服務。

1. 按一下 [命令列中的 [**新的資料存放區**，然後選取**Azure 資料湖存放區**。
2. 在 [JSON 編輯器] 中的 [ **dataLakeStoreUri** ] 屬性中，輸入資料湖 URI。
3. 按一下 [**授權**] 按鈕上的命令列]。 您應該會看到快顯視窗。

    ![授權] 按鈕](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. 使用您的認證登入，以及 [**授權**] 屬性中 JSON 應該指派值現在。
5. （選用）JSON 中指定的選擇性參數，例如**accountName**、 **subscriptionID**及**resourceGroupName**值 （或者） 從 JSON 刪除這些屬性。
6. 按一下 [部署連結的服務的命令列上的 [**部署**]。

> [AZURE.IMPORTANT] 您使用 [**授權**] 按鈕來產生授權程式碼到期後某個。 **重新授權**使用**授權**] 按鈕的時機**權杖到期**，而且重新部署的機會連結的服務。 如需詳細資訊，請參閱[Azure 資料湖市集連結服務](#azure-data-lake-store-linked-service-properties)] 區段。 



**Azure Blob 輸入資料集︰**

資料從挑選新 blob 每小時 (頻率︰ 小時、 間隔︰ 1)。 Blob 的資料夾路徑和檔案名稱會根據正在處理的扇形區的開始時間，以動態方式評估。 年、 月和日的開始時間部分，會使用的資料夾路徑和檔案名稱會使用的開始時間的小時部分。 「 外部 」:"true"設定通知資料工廠服務，表格外部資料 factory，並不會所產生的資料工廠中的活動。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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


**Azure 資料湖輸出資料集︰**

此範例會將資料複製到 Azure 資料湖存放區。 新的資料是資料湖要儲存每個小時。

    {
        "name": "AzureDataLakeStoreOutput",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **BlobSource** ，**接收**類型] 設定為**AzureDataLakeStoreSink**。

    {  
        "name":"SamplePipeline",
        "properties":
        {  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":
            [  
                {
                    "name": "AzureBlobtoDataLake",
                    "description": "Copy Activity",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
                        },
                        "sink": {
                            "type": "AzureDataLakeStoreSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
            ]
        }
    }

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>範例︰ 資料複製 Azure 資料湖存放到 Azure Blob
下列範例顯示︰

1.  連結的類型[AzureDataLakeStore](#azure-data-lake-linked-service-properties)的服務。
2.  連結的類型[AzureStorage](#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[AzureDataLakeStore](#azure-data-lake-dataset-type-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](#azure-blob-dataset-type-properties)。
5.  含使用[AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties)和[BlobSink](#azure-blob-copy-activity-type-properties)複製活動的[管線](data-factory-create-pipelines.md)

範例將時間序列資料從 Azure 資料湖儲存到 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。

**Azure 資料湖存放連結服務︰**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [AZURE.NOTE] 請參閱取得授權 URL 前一個範例中的步驟進行。  

**Azure 連結的儲存空間服務︰**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure 資料湖輸入資料集︰**

設定**「 外部 」: true**表格外部資料 factory，並不會所產生的資料工廠活動通知資料工廠服務。

    {
        "name": "AzureDataLakeStoreInput",
        "properties":
        {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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

**Azure Blob 輸出資料集︰**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 根據正在處理的扇形區的開始時間，以動態方式評估 blob 的資料夾路徑。 年、 月、 日和開始時間的小時部分，則會使用的資料夾路徑。

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**管線與複製活動︰**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **AzureDataLakeStoreSource** ，**接收**類型] 設定為**BlobSink**。


    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureDakeLaketoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureDataLakeStoreInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureDataLakeStoreSource",
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]
        }
    }


## <a name="azure-data-lake-store-linked-service-properties"></a>Azure 資料湖市集連結服務的內容

您可以使用 Azure 儲存體連結服務 Azure 資料工廠連結 Azure 儲存體帳戶。 下表提供 JSON 項目的特定 Azure 儲存體連結服務描述。

| 屬性 | 描述 | 所需 |
| :-------- | :----------- | :-------- |
| 類型 | [類型] 屬性必須設定為︰ **AzureDataLakeStore** | [是] |
| dataLakeStoreUri | 指定 Azure 資料湖存放帳戶的相關資訊。 它會以下列格式︰ https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | [是] |
| 授權 | 按一下 [**授權**] 按鈕，在**資料工廠編輯器**] 中的，輸入您的認證，會自動產生的授權 URL 指派給此屬性。  | [是] |
| 工作階段識別碼 | 從 oauth 授權工作階段的 OAuth 工作階段識別碼。 每個工作階段 id 是唯一的而且只能使用一次。 當您使用資料工廠編輯器時，會自動產生這項設定。 | [是] |  
| accountName | 資料湖帳戶名稱 | 無 |
| subscriptionId | Azure 訂閱識別碼。 | 不 （如果未指定的資料 factory 訂閱使用）。 |
| resourceGroupName |  Azure 資源群組名稱 | 不 （如果未指定的資料 factory 資源群組使用）。 |

## <a name="token-expiration"></a>權杖到期日 
您使用 [**授權**] 按鈕產生授權程式碼到期後某個。 請參閱下表的不同類型的使用者帳戶的到期時間。 您可能會看到下列錯誤訊息時驗證**權杖到期**: 「 認證操作錯誤︰ invalid_grant-AADSTS70002︰ 驗證認證的錯誤。 AADSTS70008︰ 提供存取權授與已到期或已撤銷。 追蹤識別碼︰ d18629e8-af88-43c5-88e3-d8419eb1fca1 相互關聯識別碼︰ fac30a0c-6be6-4e02-8d69-a776d2ffefd7 時間戳記︰ 2015年-12-15 21-09-31Z 」。


| 使用者類型 | 在後到期 |
| :-------- | :----------- | 
| 不受 Azure Active Directory 的使用者帳戶(@hotmail.com,@live.com,等等。)。 | 12 小時 |
| 受管理的 Azure Active Directory (AAD) 的使用者帳戶 | 執行 14 天後的最後一個扇形區。 <br/><br/>90 天，如果根據 OAuth 型連結服務扇形區執行至少一次每 14 天。 |

如果您在此 token 的到期時間之前變更您的密碼，請權杖立即到期，而且您會看到此區段中所提到的錯誤。 

若要避免/解決此錯誤，重新授權使用**授權**] 按鈕的時機**權杖到期**，而且重新部署的機會連結的服務。 您也可以產生以程式設計方式使用下一節中的 [程式碼的**工作階段識別碼**和**授權**屬性的值︰

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>若要以程式設計方式產生工作階段識別碼和授權的值 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

請參閱[AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、 [AzureDataLakeAnalyticsLinkedService 課程](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)]，以及[AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題的程式碼中使用的資料工廠類別的詳細資料。 新增**2.9.10826.1824**版**Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** WindowsFormsWebAuthenticationDialog 所使用的類別中的程式碼的參考。 
 

## <a name="azure-data-lake-dataset-type-properties"></a>Azure 資料湖資料集類型屬性

JSON 節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供資訊的位置，相關設定格式等等，資料存放區中的資料。 類型**AzureDataLakeStore**資料集的資料集 typeProperties 區段具有下列屬性︰

| 屬性 | 描述 | 所需 |
| :-------- | :----------- | :-------- |
| folderPath | 存放容器和 Azure 資料湖中的資料夾的路徑。 | [是] |
| 檔案名稱 | Azure 資料湖存放區中的檔案名稱。 檔案名稱是選擇性的區分大小寫。 <br/><br/>如果您指定的檔案名稱，（包括複製） 活動的運作方式的特定檔案。<br/><br/>若未指定的檔案名稱，複本會包含所有檔案中輸入資料集的 folderPath。<br/><br/>輸出資料集未指定的檔案名稱時所產生的檔案名稱就會在下列格式︰ 資料。<Guid>.txt (例如:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | 無 |
| partitionedBy | partitionedBy 是選擇性的屬性。 您可以用它來指定的動態 folderPath 和時間的數列資料檔名。 每小時的資料，例如參數化 folderPath。 請參閱[使用 partitionedBy 屬性](#using-partitionedby-property)的詳細資料] 與 [範例。 | 無 |
| 格式 | 支援下列格式類型︰ **TextFormat** **AvroFormat**、 **JsonFormat**、 **OrcFormat**、 **ParquetFormat**。 [格式] 下的 [**類型**] 屬性設其中一個值。 請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat)，以及[指定 ParquetFormat](#specifying-parquetformat)區段，如需詳細資訊。 如果您想要複製檔案另存為-是檔案式存放區之間 （二進位複本），您可以略過兩邊輸入與輸出資料集的定義中的 [格式] 區段。| 無
| 壓縮 | 指定類型和層級壓縮的資料。 支援的類型︰ **GZip**、**結實**，及**BZip2**和支援層級是︰ **Optimal**和**最快**。 目前，壓縮設定不支援**AvroFormat**或**OrcFormat**中的資料。 如需詳細資訊，請參閱[壓縮支援](#compression-support)一節。  | 無 |

### <a name="using-partitionedby-property"></a>使用 partitionedBy 屬性
您可以使用**partitionedBy**區段與資料工廠巨集，系統變數指定的動態 folderPath 和檔名時間數列資料︰ SliceStart 和 SliceEnd，表示針對指定的資料扇形區的開始和結束時間。

請參閱[建立資料集](data-factory-create-datasets.md)和[排程與執行](data-factory-scheduling-and-execution.md)的文章，若要了解更多時間數列資料集、 排程和扇形區上的詳細資料。

#### <a name="sample-1"></a>範例 1

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在此範例中，{扇形區} 會取代格式 (YYYYMMDDHH) 中的資料工廠系統變數 SliceStart 指定的值。 SliceStart 指的是扇形區的開始時間。 FolderPath 是不同的每個扇形區項目。 例如︰ wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>範例 2

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

在此範例中，年、 月、 日和 SliceStart 時間擷取到另一個變數所使用的 folderPath 和檔名] 屬性。

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
 

### <a name="compression-support"></a>壓縮支援  
處理大型資料集，可能會導致 I/O 和網路瓶頸。 因此，壓縮的存放區中的資料可以不只加速在網路上的資料傳輸並節省磁碟空間，但也會顯示在處理大型資料的重要的效能提升。 目前，例如 Azure Blob 或內部部署檔案系統檔案為基礎的資料儲存區支援壓縮。  

若要指定壓縮的資料集，請使用 [**壓縮**] 屬性中資料集 JSON，如下列範例所示︰   

    {  
        "name": "AzureDatalakeStoreDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
[**壓縮**] 區段會有兩個屬性︰  
  
- **類型︰**壓縮轉碼器，可能是**GZIP**、**結實**或**BZIP2**。  
- **層級︰**壓縮比例，可以是**Optimal**或**最快**。 
    - **快︰**即使未最佳化壓縮所產生的檔案時，應盡快，完成壓縮作業。 
    - **Optimal**︰ 壓縮作業應最佳壓縮，即使該作業耗費很長的時間才能完成。 
    
    如需詳細資訊，請參閱[壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)主題。 

假設範例資料集會用來作為複製活動的成果。 複製活動的輸出資料壓縮 GZIP 轉碼器使用最佳比例，，然後將成檔案 pagecounts.csv.gz Azure 資料湖存放區中的 [壓縮的資料。   

當您輸入的資料集 JSON 中指定壓縮屬性時，管線會讀取壓縮的資料來源的資料。 在資料集中輸出 JSON 指定屬性，複製活動可以撰寫壓縮的資料至的目的地。 以下是一些範例案例︰ 

- 從 Azure 資料湖存放區中，閱讀 GZIP 壓縮資料解壓縮，並將結果資料寫入 Azure SQL 資料庫。 您可以在此情況下定義壓縮 JSON 屬性輸入的 Azure 資料湖存放資料集。 
- 讀取純文字檔案從內部部署檔案系統中的資料、 壓縮 GZip 格式和 Azure 資料湖存放區撰寫壓縮的資料。 您可以在此情況下定義壓縮 JSON 屬性輸出 Azure 資料湖資料集。  
- 讀取 GZIP 壓縮資料從 Azure 資料湖存放區，它解壓縮、 壓縮使用 BZIP2，和寫入結果資料 Azure 資料湖存放區。 壓縮類型設定為 GZIP 和 BZIP2 為輸入，然後分別輸出資料集。   


## <a name="azure-data-lake-copy-activity-type-properties"></a>Azure 資料湖複製活動類型的屬性  
節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型

**AzureDataLakeStoreSource**支援下列屬性**typeProperties**區段︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 遞迴 | 指出是否讀取資料遞迴子資料夾，或僅從指定的資料夾。 | False true （預設值） | 無 |



**AzureDataLakeStoreSink**支援下列屬性**typeProperties**區段︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | 指定複製行為。 | **PreserveHierarchy:**保留檔案中的階層的目標資料夾。 相對路徑的來源資料夾的來源檔案是相同的目標檔案到目標資料夾的相對路徑。<br/><br/>**FlattenHierarchy:**的第一層的目標資料夾中建立的來源資料夾的所有檔案。 目標檔案會建立自動產生的名稱。<br/><br/>**MergeFiles:**合併至一個檔案的來源資料夾的所有檔案。 如果指定檔案/Blob 名稱，合併的檔案名稱會指定的名稱。否則，就會自動產生的檔案名稱。 | 無 |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。
