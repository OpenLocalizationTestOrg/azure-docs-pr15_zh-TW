<properties
    pageTitle="將資料從 Salesforce 移使用資料工廠 |Microsoft Azure"
    description="瞭解如何將資料從 Salesforce，使用 Azure 資料工廠。"
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
    ms.date="10/25/2016"
    ms.author="jingwang"/>

# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>將資料從 Salesforce 移使用 Azure 資料工廠
本文概述如何使用複製活動 Azure 資料工廠，將資料複製 Salesforce，會列在接收資料表資料行中[支援的來源和接收](data-factory-data-movement-activities.md#supported-data-stores)任何資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

Azure 資料工廠目前支援只移動資料 Salesforce [支援的接收資料 stores]((data-factory-data-movement-activities.md#supported-data-stores)，但不支援將資料從其他資料會儲存到 Salesforce 的用途。

## <a name="prerequisites"></a>必要條件
- 您必須使用 Salesforce 下列版本之一︰ 開發人員 Edition、 專業版、 企業版或不受限制的版本。
- 您必須啟用 API 權限。 請參閱[如何啟用 Salesforce 權限設定的 API 存取？](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
- 若要 Salesforce 資料複製到內部部署資料存放區，您必須至少安裝在您的內部部署環境中的資料管理閘道器 2.0。

## <a name="salesforce-request-limits"></a>Salesforce 要求限制
Salesforce 有限制總 API 邀請和並行 API 邀請兩者均適用。 請參閱詳細資料[Salesforce 開發人員限制](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)本文的 「 API 邀請限制 」 一節。 注意是否同時的要求數目超過限制，節流發生，您會看到隨機失敗。要求總數超過限制，Salesforce 帳戶會遭到封鎖 24 小時。您也可能會收到 「 REQUEST_LIMIT_EXCEEDED 」 錯誤，這兩個案例。

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線將資料從 Salesforce 到任何支援的接收資料存放區，最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)快速的逐步解說，使用 [複製資料精靈建立管線。

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)、 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)中或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製到 Azure Blob 儲存體 Salesforce。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。   

## <a name="sample-copy-data-from-salesforce-to-an-azure-blob"></a>範例︰ 資料複製 Salesforce 到 Azure blob
這個範例將資料從 Salesforce 到 Azure blob 每小時。 以下範例中所使用的 JSON 屬性的各節所述後的範例。 您可以直接至任何列出的[資料移動活動](data-factory-data-movement-activities.md#supported-data-stores)文件中使用複製活動 Azure 資料工廠接收複製資料。

以下是資料工廠成品，必須先建立實作案例。 以下清單區段提供詳細步驟。

- 連結之服務的[Salesforce](#salesforce-linked-service-properties)的類型
- 連結之服務的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
- 輸入的[資料集](data-factory-create-datasets.md)的類型[RelationalTable](#salesforce-dataset-properties)
- 類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)的輸出[資料集](data-factory-create-datasets.md)
- 含使用[RelationalSource](#relationalsource-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動的[管線](data-factory-create-pipelines.md)

**Salesforce 連結服務**

此範例使用**Salesforce**連結服務。 請參閱[Salesforce 連結服務](#salesforce-linked-service-properties)此連結服務所支援的屬性。  如需如何重設取得安全性 token 指示，請參閱[取得安全性 token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) 。

    {
        "name": "SalesforceLinkedService",
        "properties":
        {
            "type": "Salesforce",
            "typeProperties":
            {
                "username": "<user name>",
                "password": "<password>",
                "securityToken": "<security token>"
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

**Salesforce 輸入資料集**

    {
        "name": "SalesforceInput",
        "properties": {
            "linkedServiceName": "SalesforceLinkedService",
            "type": "RelationalTable",
            "typeProperties": {
                "tableName": "AllDataType__c"  
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

設定**外部** **，則為 true**通知資料工廠服務，資料集外部資料 factory，並不會所產生的資料工廠中的活動。

> [AZURE.IMPORTANT] 「 __C 」 名稱的一部分 API 所需的任何自訂的物件。

![資料工廠 Salesforce 連線-API 名稱](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure blob 輸出資料集**

每小時寫入資料至新 blob (頻率︰ 小時、 間隔︰ 1)。

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/alltypes_c"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**使用複製活動管線**

管線包含複製活動，設定為使用上述的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **RelationalSource**，然後**接收**類型設定為 [ **BlobSink**。

支援的 RelationalSource 屬性的清單，請參閱[RelationalSource 類型屬性](#relationalsource-type-properties)。

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "SalesforceToAzureBlob",
                "description": "Copy from Salesforce to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "SalesforceInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"             
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

> [AZURE.IMPORTANT] 「 __C 」 名稱的一部分 API 所需的任何自訂的物件。

![資料工廠 Salesforce 連線-API 名稱](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="salesforce-linked-service-properties"></a>Salesforce 連結服務屬性

下表提供 JSON 元素的特定 Salesforce 連結服務的描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 類型 | [類型] 屬性必須設定為︰ **Salesforce**。 | [是] |
| 使用者名稱 |指定的使用者帳戶的使用者名稱。 | [是] |
| 密碼 | 指定的使用者帳戶的密碼。  | [是] |
| securityToken | 指定安全性 token 使用者帳戶。 如需如何重設取得安全性 token 指示，請參閱[取得安全性 token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) 。 若要深入瞭解安全性權杖一般而言，請參閱[安全性和 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)。  | [是] |

## <a name="salesforce-dataset-properties"></a>Salesforce 資料集屬性

如需區段及屬性所使用的定義資料集的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 （Azure SQL、 Azure blob、 Azure 資料表，等等）。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 TypeProperties 資料集的區段類型**RelationalTable**具有下列屬性︰

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 表格名稱 | 在 [Salesforce 資料表的名稱。 | 不需要 （如果已指定**查詢**的**RelationalSource** ） |

> [AZURE.IMPORTANT]  「 __C 」 名稱的一部分 API 所需的任何自訂的物件。

![資料工廠 Salesforce 連線-API 名稱](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="relationalsource-type-properties"></a>RelationalSource 類型屬性

如需區段及屬性所使用的定義活動的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)。 屬性名稱、 描述、 輸入與輸出的資料表，然後各種原則可供所有類型的活動。

所提供的活動，[typeProperties] 區段中的屬性相反地，會隨著每個活動類型。 複製活動，而有所不同的來源和接收類型。

複製活動時來源類型**RelationalSource** （包括 Salesforce） 的下列屬性會出現在 typeProperties] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 查詢 | 使用自訂查詢資料。 | SQL 92 查詢或[Salesforce 物件查詢語言 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm)查詢。 例如︰ `select * from MyTable__c`。 | 不需要 （如果指定的**資料集****的表格名稱**） |

> [AZURE.IMPORTANT] 「 __C 」 名稱的一部分 API 所需的任何自訂的物件。

![資料工廠 Salesforce 連線-API 名稱](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>查詢秘訣

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>擷取資料使用 where 子句的日期時間欄
何時指定 SOQL 或 SQL 查詢，請注意 DateTime 格式的差異。 例如︰

- **SOQL 範例**︰ $$Text.Format (「 選取 [識別碼]、 名稱、 BillingCity 從帳戶位置 LastModifiedDate > = {0:yyyy-MM-ddTHH:mm:ssZ} 和 LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}'，WindowStart、 WindowEnd)
- **SQL 範例**︰ $$Text.Format (「 選取 * 從帳戶位置 LastModifiedDate > = {{ts\'{0:yyyy-MM-dd ss}\'}} 和 LastModifiedDate < {{ts\'{1:yyyy-MM-dd ss}\'}}'，WindowStart、 WindowEnd)'。

### <a name="retrieving-data-from-salesforce-report"></a>從 [Salesforce 報告擷取資料
您可以從 Salesforce 報告擷取資料，以指定查詢作為`{call "<report name>"}`，例如`"query": "{call \"TestReport\"}"`。

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>擷取 Salesforce 資源回收筒刪除記錄
若要查詢柔邊的 Salesforce 資源回收筒] 刪除的記錄，您可以指定**」 IsDeleted = 1 」**您在查詢中。 例如， 

- 若要查詢已刪除的記錄，請指定 「*從 MyTable__c*選取*位置 IsDeleted = 1** 」
- 若要查詢所有包括現有及已刪除的記錄，請指定 「*從 MyTable__c*選取*位置 IsDeleted = 0 或 IsDeleted = 1** 」

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-salesforce"></a>Salesforce 類型對應
Salesforce 類型 | .網路為基礎的類型
--------------- | ---------------
自動編號 | 字串
核取方塊 | 布林值
貨幣 | 點兩下
日期 | 日期時間
日期/時間 | 日期時間
電子郵件 | 字串
識別碼 | 字串
查閱關聯 | 字串
多重選取把貨物 | 字串
數字 | 點兩下
完成百分比 | 點兩下
電話 | 字串
把貨物 | 字串
文字 | 字串
文字區域 | 字串
文字區域 （時間） | 字串
文字區域 (Rtf) | 字串
文字 （加密） | 字串
URL | 字串

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動效能與調整輔助線](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。
