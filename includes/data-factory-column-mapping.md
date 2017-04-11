## <a name="column-mapping-with-translator-rules"></a>使用翻譯工具規則的資料行對應
資料行對應可用來指定如何在 [結構] 來源資料表地圖的資料行中接收資料表的 [結構] 指定指定資料行。 複製活動**typeProperties**一節中有**columnMapping**屬性。

資料行對應支援以下情況︰

- 在來源資料表 [結構] 中的所有資料行對應到接收器資料表 [結構] 中的所有資料行。
- 接收資料表 [結構] 中的所有資料行對應的來源資料表 [結構] 中的資料行的子集合。

以下是錯誤狀況而導致例外狀況︰

- 較少的欄或多個資料行 [結構] 接收資料表之比對應中指定。
- 複製對應。
- SQL 查詢結果中沒有對應中指定的資料行名稱。

## <a name="column-mapping-samples"></a>資料行對應範例
> [AZURE.NOTE] 下列範例是 Azure SQL 和 Azure Blob，但適用於支援矩形的資料集的任何資料存放區。 您必須以調整資料集和下方的範例中的連結的服務定義指向相關的資料來源中的資料。 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>範例 1︰ 從 Azure SQL 對應至 Azure blob 的欄
在此範例中，輸入的表格的結構，它提供 SQL Azure SQL 資料庫中的資料表。

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
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

在此範例中，輸出表格的結構，並指向 blob 中 Azure blob 儲存體。

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

活動 JSON 如下所示。 使用**翻譯工具**屬性對應至接收 (**columnMappings**) 中的資料行的來源資料行。

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**資料行對應流程︰**

![資料行對應流量](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>範例 2-對應的 SQL 查詢從 Azure SQL Azure blob 的欄
在此範例中，SQL 查詢用來從 SQL Azure 中擷取資料，而非只需指定 [結構] 區段中的 [資料表名稱和資料行名稱。 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

在此情況下，查詢結果第一次對應到指定的 [結構] 的來源資料行。 接下來，從來源 [結構] 欄對應到接收 [結構] 中的資料行與 columnMappings 中指定的規則。  假設此查詢會傳回 5 的資料行，兩個其他欄，然後來源 [結構] 中指定的。

**資料行對應流程**

![資料行對應流程 2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)







