## <a name="invoking-stored-procedure-for-sql-sink"></a>SQL 接收叫用預存程序

將資料複製到 SQL Server] 或 [Azure SQL/SQL Server 資料庫，當使用者指定的預存程序可能已設定並使用其他參數叫用。 

內建複製機制不會服務目的時，可以運用預存程序。 這通常被未能額外處理 （合併欄，尋找其他的值，插入多個資料表...） 的需求，以完成前最終插入目的資料表中的來源資料。 

您可能會叫用所選擇的預存程序。 下列範例會示範如何使用預存程序執行簡單的插入至資料庫中的表格。 

**輸出資料集**

在此範例中，輸入已設為︰ SqlServerTable。 將它設定為 AzureSqlTable 若要使用 Azure SQL 資料庫。 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
定義 SqlSink 一節中複製活動 JSON，如下所示。 若要打電話預存程序插入資料時，所需 SqlWriterStoredProcedureName 和 SqlWriterTableType 兩個屬性。

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

在您的資料庫中，定義 SqlWriterStoredProcedureName 預存程序，使用相同的名稱。 將輸出表格處理從指定的來源及插入的輸入的資料。 請注意預存程序的參數名稱應表格 JSON 檔案中所定義的表格名稱相同。

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

在您的資料庫中，定義具有相同名稱的表格類型為 SqlWriterTableType。 請注意，應傳回輸入資料的結構描述與相同資料表類型的結構描述。

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

[預存程序] 功能會利用[屬性參數](https://msdn.microsoft.com/library/bb675163.aspx)。