## <a name="repeatability-during-copy"></a>複製期間重複性

當從和關聯式存放區，請複製資料，您需要謹記重複性為了避免非預期的結果。 

扇形區可以重新執行自動 Azure 資料工廠依照指定的重試] 原則。 我們建議您設定重試原則，以防止暫時性失敗。 因此重複性是來處理資料移動期間，重要。 

**做為來源︰**

> [AZURE.NOTE] 下列範例是 SQL Azure，但適用於支援矩形的資料集的任何資料存放區。 您可能需要調整**類型**來源與**查詢**屬性 (例如︰ 查詢，而不是 sqlReaderQuery) 的資料儲存。   

通常閱讀時從關聯式存放區，您想要讀取只資料對應至該扇形區。 若要執行這項操作方式，是使用可用的 WindowStart 和 WindowEnd 變數 Azure 資料工廠。 閱讀有關的變數和 Azure 資料工廠[排程和執行](../articles/data-factory/data-factory-scheduling-and-execution.md)文件中的函數。 範例︰ 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

此查詢會讀取從 「 我的表格 」 落在扇形區持續時間範圍內的資料。 重新執行此扇區起始也都要確定此行為。 

在其他情況下，您可能會想要讀取整個表格 （假設為一次只移動） 可能會定義 sqlReaderQuery，如下所示︰

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
