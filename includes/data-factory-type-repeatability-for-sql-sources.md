## <a name="repeatability-during-copy"></a>複製期間重複性

當複製資料到 Azure SQL/SQL Server 的其他資料儲存時需要謹記重複性為了避免非預期的結果。 

當複製資料到 Azure SQL/SQL Server 資料庫時，預設的 [新增至接收資料表的資料集預設會複製活動。 例如，當資料來源的 CSV （逗號分隔值） 檔案包含兩筆記錄至 Azure SQL/SQL Server 資料庫中複製資料，這是表格看起來像︰
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

假設您在 [來源檔案中找到錯誤，並更新數量的向下管 2 至 4 來源檔案中。 如果該期間的重新執行資料扇形區，您會發現附加至 Azure SQL/SQL Server 資料庫的兩筆新記錄。 以下假設的資料表中的欄都沒有主索引鍵的限制。
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

若要避免此問題，您需要利用下列其中一項來指定了語意下方下 2 機制。

> [AZURE.NOTE] 扇形區可以重新執行自動 Azure 資料工廠依照指定的重試原則。

### <a name="mechanism-1"></a>機制 1

您可以利用**sqlWriterCleanupScript**屬性扇形區執行時，第一次執行清理] 動作。 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

清除指令碼就會執行第一個期間複本以便指定的扇形區，其會對應到該扇形區 SQL 資料表刪除資料。 活動就會插入資料 SQL 資料表。 

如果扇形區現在重新執行，然後您會發現數量會更新為所需。
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

假設一般墊圈記錄會從原始 csv 中移除。 然後再次執行扇形區會產生下列結果︰ 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

什麼新必須這麼做。 複製活動已清除指令碼，若要刪除該扇形區的對應資料。 然後讀取 csv （其中然後包含僅 1 的記錄） 輸入並插入表格。 

### <a name="mechanism-2"></a>機制 2
> [AZURE.IMPORTANT] 這次 Azure SQL Data Warehouse 不支援 sliceIdentifierColumnName。 

達成重複性的另一個機制，是由專用的資料行 (**sliceIdentifierColumnName**) 在目標資料表。 Azure 資料 Factory 想要使用此欄，請確定來源與目的地保持同步處理。 這種方式運作時變更或定義的目的地 SQL 資料表結構描述的彈性。 

此欄會用於 Azure 資料工廠重複性然後程序中 Azure 資料工廠會不進行任何結構描述變更至資料表。 使用此方法的方式︰

1.  定義類型二進位 (32) 資料行的目的地 SQL 資料表。 應在此欄的任何限制式。 此範例中為 「 ColumnForADFuseOnly' 現在就讓我們命名此欄。
2.  使用複製活動，如下所示︰

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure 資料工廠填入依照其需求，以確保來源與目的地保持同步處理此資料行。 此欄的值不應此內容外部使用者。 

類似機制 1，複製活動會自動先清理指定的扇形區，從目的地 SQL 資料表的資料，然後再執行複製活動以正常方式將資料從來源插入至該扇形區的目的地。 
