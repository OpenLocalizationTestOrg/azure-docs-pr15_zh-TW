### <a name="type-conversion-sample"></a>類型轉換的範例
下列範例會從 Blob 複製資料到 Azure SQL 類型轉換。

假設 Blob 資料集的 CSV 格式，並包含 3 個資料行。 這些是使用縮寫法文星期名稱的自訂 datetime 格式的日期時間資料行。

您將定義 Blob 來源資料集以及類型定義的資料行，如下所示。

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
            "external": true,
            "availability":
            {
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

指定 SQL 類型.NET 類型對應資料表，上方您想要定義下列結構描述的 SQL Azure 資料表。

| 資料行名稱 | SQL 類型 |
| ----------- | -------- |
| 使用者識別碼 | bigint |
| 名稱 | 文字 |
| lastlogindate | 日期時間 |

下一步您定義 Azure SQL 資料集，如下所示。 注意︰ 您不需要指定類型資訊的 [結構] 區段，因為基礎的資料存放區中已指定 [類型] 資訊。

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

在此情況下資料工廠會自動執行將資料從 Blob 移至 [Azure SQL 時使用 fr-fr 文化特性設定格式的轉換，包括使用自訂的日期時間 Datetime 欄位類型。


