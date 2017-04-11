<properties 
    pageTitle="將資料從 Web 資料表移動 |Azure 資料工廠" 
    description="進一步瞭解如何將內部部署中的資料使用 Azure 資料工廠網頁中的表格。" 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>移動資料來源的 Web 資料表使用 Azure 資料工廠
本文概述如何使用複製活動 Azure 資料工廠，將資料複製到另一個資料存放區的網頁中的表格。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠目前支援只移動資料從 Web 資料表到其他資料儲存區中，但不是將資料從其他資料移到 Web 資料表目的地儲存。

> [AZURE.NOTE] 此網頁連接器目前支援只解壓縮的 HTML 頁面的表格內容。

## <a name="sample-copy-data-from-web-table-to-azure-blob"></a>範例︰ Web 資料表將資料複製到 Azure Blob

下列範例顯示︰

1.  連結的類型[Web](#web-linked-service-properties)服務。
2.  連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
3.  輸入的[資料集](data-factory-create-datasets.md)的類型[WebTable](#WebTable-dataset-properties)。
4.  輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
4.  [管線](data-factory-create-pipelines.md)與使用[WebSource](#websource-copy-activity-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

範例將資料從 Web 資料表到 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 

下列範例會顯示如何將資料複製到 Azure blob 的 Web 資料表。 不過，可以直接至任何[資料移動活動](data-factory-data-movement-activities.md)文件中所述，使用複製活動 Azure 資料工廠接收複製資料。 

**網頁連結服務**此範例使用匿名驗證的連結的 Web 服務。 請參閱驗證您可以使用的不同類型的[網頁連結服務](#web-linked-service-properties)一節。 

    {
        "name": "WebLinkedService",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "url" : "https://en.wikipedia.org/wiki/"
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

**WebTable 輸入資料集**設定**外部** **，則為 true**通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。

> [AZURE.NOTE] 請參閱步驟以 HTML 網頁快速表格索引的[取得 HTML 頁面中的表格索引](#get-index-of-a-table-in-an-html-page)一節。  

    
    {
        "name": "WebTableInput",
        "properties": {
            "type": "WebTable",
            "linkedServiceName": "WebLinkedService",
            "typeProperties": {
                "index": 1,
                "path": "AFI's_100_Years...100_Movies"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }



**Azure Blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/Movies"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }




**含複製活動的管線**

管線包含已設定為使用上述的輸入與輸出資料集複製活動和排程執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **WebSource** ，**接收**類型] 設定為**BlobSink**。 

支援 WebSource 屬性的清單，請參閱[WebSource 類型屬性](#websource-copy-activity-type-properties)。 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [
              {
                "name": "WebTableInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "WebSource"
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


## <a name="web-linked-service-properties"></a>Web 服務連結屬性

下表提供的特定網頁連結的服務 JSON 元素的描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰**網頁** | [是] | 
| Url | Web 來源 URL | [是] |
| 識別碼 | 基本或匿名。 | [是] |
| 使用者名稱 | 基本驗證的使用者名稱。 | 是 （適用於基本驗證）
| 密碼 | 基本驗證的密碼。 | 是 （適用於基本驗證）

### <a name="using-anonymous-authentication"></a>使用匿名驗證

    {
        "name": "web",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "url" : "https://en.wikipedia.org/wiki/"
            }
        }
    }


### <a name="using-basic-authentication"></a>使用基本驗證
    
    {
        "name": "web",
        "properties":
        {
            "type": "Web",
            "typeProperties":
            {
                "authenticationType": "basic",
                "url" : "http://myit.mycompany.com/",
                "userName": "Administrator",
                "password": "password"
            }
        }
    }


## <a name="webtable-dataset-properties"></a>WebTable 資料集屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 資料集的類型**WebTable** typeProperties 區段有下列屬性

屬性 | 描述 | 所需
:-------- | :----------- | :--------
類型    | 輸入資料集]。 必須設定為 [ **WebTable** | [是]
路徑 | 含有表格的資源相對 URL。 | [否]。 當未指定的路徑，則會使用只將連結的服務定義中指定的 URL。 
索引 | 資源中的資料表索引。 請參閱步驟以 HTML 網頁快速表格索引的[取得 HTML 頁面中的表格索引](#get-index-of-a-table-in-an-html-page)一節。 | [是]


**範例︰**

    {
        "name": "WebTableInput",
        "properties": {
            "type": "WebTable",
            "linkedServiceName": "WebLinkedService",
            "typeProperties": {
                "index": 1,
                "path": "AFI's_100_Years...100_Movies"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }

## <a name="websource---copy-activity-type-properties"></a>WebSource-複製活動類型的屬性

節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。 

活動 typeProperties 一節中提供的屬性另一方面會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

目前的類型**WebSource**複製活動的來源時，會支援沒有其他的屬性。 

## <a name="get-index-of-a-table-in-an-html-page"></a>取得 HTML 頁面中的表格索引

1. 啟動**Excel 2016**並切換到 [**資料**] 索引標籤。  
2. 按一下工具列] 上的 [**新增查詢**，指向**從其他來源**，按一下 [**從 Web**]。
    
    ![Power Query] 功能表](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. 在 [**從 Web** ] 對話方塊中，輸入您想要連結的服務 JSON 中使用的**URL** (例如︰ https://en.wikipedia.org/wiki/) 以及您指定的資料集的路徑 (例如︰ AFI %27s_100_years...100_Movies)，然後按一下**[確定]**。 

    ![從 Web] 對話方塊](./media/data-factory-web-table-connector/FromWeb-DialogBox.png) 

    此範例中使用的 URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  如果您看到**存取的網站內容**] 對話方塊中，選取正確**的 URL**，**驗證**]，然後按一下 [**連線**]。 

    ![存取網站內容] 對話方塊](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  按一下**表格**中的項目樹狀檢視，請參閱資料表中的內容，然後按一下 [底部的 [**編輯**] 按鈕。  

    ![導覽器] 對話方塊](./media/data-factory-web-table-connector/Navigator-DialogBox.png) 

5. 在 [**查詢編輯器**] 視窗中，按一下工具列上的 [**進階編輯器**] 按鈕。

    ![進階的編輯器] 按鈕](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. 在 [進階編輯器] 對話方塊中，「 來源 」 旁的數字是索引。

    ![進階編輯器-編製索引](./media/data-factory-web-table-connector/AdvancedEditor-Index.png) 


如果您使用 Excel 2013，請使用[Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379)取得的索引。 如需詳細資訊，請參閱[連線至網頁](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8)文件。 如果您使用的[桌面版的 Microsoft Power BI](https://powerbi.microsoft.com/desktop/)類似所的步驟。 

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。
