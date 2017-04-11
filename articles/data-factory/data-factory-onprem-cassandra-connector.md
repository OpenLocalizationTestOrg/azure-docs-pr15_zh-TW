<properties 
    pageTitle="將資料從 Cassandra 使用資料工廠移 |Microsoft Azure" 
    description="進一步瞭解如何從內部部署 Cassandra 資料庫使用 Azure 資料工廠移動資料。" 
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
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>從內部部署 Cassandra 資料庫使用 Azure 資料工廠移動資料
本文概述如何使用複製活動 Azure 資料工廠將來自內部部署 Cassandra 資料庫的資料複製到任何[支援的來源和接收](data-factory-data-movement-activities.md#supported-data-stores)] 區段中的接收資料行底下所列的資料存放區。 本文是根據呈現資料移動的一般概觀複製活動與支援的資料存放區組合的[資料移動活動](data-factory-data-movement-activities.md)文件。

資料工廠目前支援只移動資料從 Cassandra 資料庫[支援接收資料存放區](data-factory-data-movement-activities.md#supported-data-stores)，但不是移動資料從其他資料儲存區 Cassandra 資料庫。

## <a name="prerequisites"></a>必要條件
Azure 資料工廠服務，才能連線到您的內部部署 Cassandra 資料庫，您必須安裝下列項目︰ 

- 資料管理閘道器 2.0 或同一台機器裝載資料庫的或不同的電腦，避免佔用與資料庫的資源。 資料管理閘道是安全和受管理的方式將內部部署資料來源連線至雲端服務的軟體。 請參閱[移動內部部署與雲端之間的資料](data-factory-move-data-between-onprem-and-cloud.md)文件的詳細資料管理閘道器。
  
    當您安裝的閘道器時，它會自動安裝用來連線到 Cassandra 資料庫 Microsoft Cassandra ODBC 驅動程式。 

> [AZURE.NOTE] 如需疑難排解連線/閘道器的秘訣請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)相關問題。 

## <a name="copy-data-wizard"></a>複製資料精靈
建立管線從 Cassandra 資料庫的資料複製到任何支援的接收資料存放區，最簡單的方法是使用 [複製資料精靈。 請參閱[教學課程︰ 建立使用複製精靈的管線](data-factory-copy-data-wizard-tutorial.md)的快速建立管線，使用 [複製資料] 精靈的逐步解說。 

下列範例會提供可供您建立的管線使用[Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](data-factory-copy-activity-tutorial-using-powershell.md)範例 JSON 定義。 即會顯示如何將資料複製到 Azure Blob 儲存體 Cassandra 資料庫。 不過，可以複製資料至任何接收敘述[以下](data-factory-data-movement-activities.md#supported-data-stores)Azure 資料工廠中使用複製活動。   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>範例︰ 資料複製 Cassandra Blob
範例將資料從 Cassandra 資料庫至 Azure blob 每小時。 這些範例中所用的 JSON 屬性是由下列範例的各節所述。 可以直接至任何[資料移動活動](data-factory-data-movement-activities.md#supported-data-stores)文件中所述，使用複製活動 Azure 資料工廠接收複製資料。 

- 連結的類型[OnPremisesCassandra](#onpremisescassandra-linked-service-properties)的服務。
- 連結的類型[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)的服務。
- 輸入的[資料集](data-factory-create-datasets.md)的類型[CassandraTable](#cassandratable-properties)。
- 輸出[資料集](data-factory-create-datasets.md)的類型[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。
- [管線](data-factory-create-pipelines.md)與使用[CassandraSource](#cassandrasource-type-properties)及[BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)複製活動。

**連結的 Cassandra 服務**

此範例使用**Cassandra**連結服務。 請參閱此連結服務所支援的屬性的[Cassandra 連結服務](#onpremisescassandra-linked-service-properties)一節。  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
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

**Cassandra 輸入資料集**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
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
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**含複製活動的管線**

管線包含複製活動，設定為使用的輸入與輸出資料集，並排定執行每小時。 在管線 JSON 定義，**來源**類型設定為 [ **CassandraSource** ，**接收**類型] 設定為**BlobSink**。 

支援 RelationalSource 屬性的清單，請參閱[RelationalSource 類型屬性](#cassandrasource-type-properties)。 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
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
## <a name="onpremisescassandra-linked-service-properties"></a>連結的 OnPremisesCassandra 服務屬性

下表提供 JSON 項目的特定 Cassandra 連結服務描述。

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- | 
| 類型 | [類型] 屬性必須設定為︰ **OnPremisesCassandra** | [是] |
| host （主機) | 一或多個 IP 位址或的 Cassandra 伺服器主機名稱。<br/><br/>指定逗點分隔的 IP 位址或同時連線至所有伺服器主機名稱清單。 | [是] | 
| 連接埠 | Cassandra 伺服器會使用用戶端連線接聽 TCP 連接埠。 | 否，預設值︰ 9042 |
| 識別碼 | 基本，或匿名 | [是] |
| 使用者名稱 |指定使用者名稱的使用者帳戶。 | 是的如果基本設定識別碼。 |
| 密碼 | 指定使用者帳戶的密碼。  | 是的如果基本設定識別碼。 |
| gatewayName | 閘道器用來連線到內部部署 Cassandra 資料庫的名稱。 | [是] |
| encryptedCredential | 加密的閘道器的認證。 | 無 | 

## <a name="cassandratable-properties"></a>CassandraTable 屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則] 區段都是類似的所有資料集類型 (Azure SQL Azure blob、 Azure 資料表等等。)。

**TypeProperties**區段不同資料集的每個類型，並提供的資料存放區中的資料位置的相關資訊。 資料集的類型**CassandraTable** typeProperties 區段有下列屬性

| 屬性 | 描述 | 所需 |
| -------- | ----------- | -------- |
| 再 | 再或 Cassandra 資料庫中的結構描述的名稱。 | 是] （**查詢** **CassandraSource**未定義）。 |
| 表格名稱 | Cassandra 資料庫中資料表的名稱。 | 是] （**查詢** **CassandraSource**未定義）。 |


## <a name="cassandrasource-type-properties"></a>CassandraSource 類型屬性
節與適用於定義活動的內容的完整清單，請參閱[建立管線](data-factory-create-pipelines.md)文章。 例如名稱、 描述、 輸入與輸出的資料表，以及原則的內容，可針對所有類型的活動。 

另一方面活動 typeProperties 一節中提供的屬性會隨著每一個活動類型。 複製活動，而有所不同的來源和接收類型。

類型**CassandraSource**的來源時，會提供下列屬性 typeProperties] 區段中︰

| 屬性 | 描述 | 允許的值 | 所需 |
| -------- | ----------- | -------------- | -------- |
| 查詢 | 使用自訂查詢資料。 | SQL 92 查詢或 CQL 查詢。 請參閱[CQL 參考](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)。 <br/><br/>在使用 SQL 查詢時，指定**再 name.table 名稱**來代表您想要查詢的資料表。 | 不是需要 （如果已定義的表格名稱和資料集上的再）。  |
| consistencyLevel | 一致性層級指定多少複本傳回用戶端應用程式的資料之前，必須回應讀取要求。 Cassandra 會檢查指定的資料以符合讀取的要求的複本數目。 | ONE，TWO，THREE，仲裁，所有，LOCAL_QUORUM，EACH_QUORUM、 LOCAL_ONE。 如需詳細資訊，請參閱[設定資料的一致性](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)。 | [否]。 預設值為 1。 |  


### <a name="type-mapping-for-cassandra"></a>Cassandra 類型對應
Cassandra 類型 | .Net 基礎類型
--------------- | ---------------
ASCII | 字串 
BIGINT | Int64
BLOB | Byte]
布林值 | 布林值
小數位數 | 小數位數
點兩下 | 點兩下
浮動時間 | 單一
INET | 字串
INT | Int32
文字 | 字串
時間戳記 | 日期時間
TIMEUUID | Guid
UUID | Guid
VARCHAR | 字串
VARINT | 小數位數

> [AZURE.NOTE]  
> 集合類型 （地圖、 設定、 清單、 等），請參閱[使用 Cassandra 集合類型使用的虛擬資料表](#work-with-collections-using-virtual-table)一節。 
> 
> 不支援的使用者定義類型。
> 
> 二進位資料行和欄字串長度的長度不能超過 4000。 

## <a name="work-with-collections-using-virtual-table"></a>搭配使用的虛擬資料表的集合
Azure 資料工廠會使用內建的 ODBC 驅動程式連線至並複製 Cassandra 資料庫中的資料。 針對包含地圖、 設定和清單的集合類型，請驅動程式重新正規化資料至對應的虛擬資料表。 明確地說，如果資料表包含集合中的任何資料行，驅動程式就會產生下列虛擬表格︰

-   **基底資料表**，其中包含除了集合欄實際的資料表與相同的資料。 基底資料表使用它代表實際資料表使用相同的名稱。
-   **虛擬資料表**的每個集合欄中，展開的巢狀的資料。 虛擬資料表，表示集合被具名使用實際的資料表與 「_vt_」 的分隔符號的資料行名稱的名稱。

虛擬表格參照實際的資料表，啟用存取正規化的資料驅動程式中的資料。 如需詳細資訊，請參閱範例 > 一節。 您可以存取 Cassandra 集合的內容查詢和加入虛擬的資料表。

您可以使用[複製精靈](data-factory-data-movement-activities.md#data-factory-copy-wizard)以直覺方式檢視 Cassandra 資料庫包括虛擬的表格中的 [表格的清單及預覽內的資料。 您也可以建構複製精靈中的查詢，然後驗證來查看結果。

### <a name="example"></a>範例
例如，下列 「 ExampleTable 」 是 Cassandra 資料庫資料表，其中包含命名為 「 pk_int 」、 文字資料行名稱值、 清單欄、 地圖欄和設定資料行 （命名為 「 StringSet 」） 整數主索引鍵資料行。

pk_int | 值 | 清單 | 地圖 |   StringSet
------ | ----- | ---- | --- | --------
1 | 「 範例值 1 」 | ["1", "2", "3"]  | {」 S1 」: 「 「，」 S2 」:"b"} | {"A", "B", "C"}
3 | 「 範例值 3 」 | [「 100 」、 「 101 」、 「 102 「，」 105 」] | {」 S1 」: 「 t"} | {"A", "E"}

驅動程式會產生多個虛擬代表此單一資料表的資料表。 虛擬資料表中的外部索引鍵資料行參考中實際的資料表，主索引鍵資料行，並指出虛擬表格列對應到哪一個真實的表格列。 

第一個虛擬資料表是基底表格命名為 「 ExampleTable 」 會顯示在下表中。 基底資料表包含相同的資料，除了的集合，它是被省略了這個資料表中，展開其他虛擬資料表中的原始資料庫資料表。

pk_int | 值
------ | -----
1 | 「 範例值 1 」
3 | 「 範例值 3 」

下表顯示 renormalize 清單、 地圖及 StringSet 欄中的資料的虛擬資料表。 名稱結尾的 「 _index 」 或 「 _key 」 欄會指出內的原始的清單或地圖的資料的位置。 具有名稱以 「 _value 」 結尾的資料行包含展開的資料集合。

#### <a name="table-exampletablevtlist"></a>「 ExampleTable_vt_List 」 的資料表︰
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>「 ExampleTable_vt_Map 」 的資料表︰
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>「 ExampleTable_vt_StringSet 」 的資料表︰
pk_int | StringSet_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>效能及調整  
請參閱[複製活動的效能與微調指南](data-factory-copy-activity-performance.md)，瞭解如何要素 Azure 資料工廠，並將它最佳化的各種方式中的資料移動 （複製活動） 的效能的影響。
