<properties 
    pageTitle="資料工廠函數及系統變數 |Microsoft Azure" 
    description="提供 Azure 資料工廠函數和系統變數的清單" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Azure 資料工廠-函式和系統變數
本文提供函數及變數 Azure 資料工廠所支援的相關資訊。
  
## <a name="data-factory-system-variables"></a>資料工廠系統變數

變數名稱 | 描述 | 物件範圍 | JSON 範圍及使用案例
------------- | ----------- | ------------ | ------------------------
WindowStart | 執行視窗目前活動的時間間隔的開頭 | 活動 | <ol><li>指定資料選取範圍的查詢。 請參閱參考[資料移動活動](data-factory-data-movement-activities.md)文件中的連接器文章。</li><li>參數傳遞給登錄區指令碼 （如上所示的範例）。</li>
WindowEnd | 執行視窗目前活動的時間間隔的結尾 | 活動 | 上述相同
SliceStart | 資料扇形區所產生的時間間隔的開頭 | 活動<br/>資料集 | <ol><li>指定使用[Azure Blob](data-factory-azure-blob-connector.md)與[檔案系統資料集](data-factory-onprem-file-system-connector.md)的動態資料夾路徑和檔名。</li><li>指定輸入相依性活動輸入集合資料工廠功能。</li></ol>
SliceEnd | 目前所產生的資料扇形區的時間間隔的結尾 | 活動<br/>資料集 | 與上面相同。 

> [AZURE.NOTE] 目前資料工廠需要完全活動中指定的排程符合輸出資料集的顯示狀態中指定的排程。 這表示 WindowStart、 WindowEnd SliceStart 和 SliceEnd 一律對應到相同的時間週期] 與 [單一輸出扇形區。
 
## <a name="data-factory-functions"></a>資料工廠函數 

您可以在下列用途中所述的系統變數上方的資料工廠沿著與使用函數︰

1.  指定資料選取查詢 （請參閱參考[資料移動活動](data-factory-data-movement-activities.md)文件的連接器文章。

    啟動資料工廠函數的語法是︰**$$**資料選取查詢和其他屬性中的活動和資料集。  
2. 指定資料工廠函數輸入相依性活動輸入集合 （請參閱上述範例）。

    $$，不需要指定輸入相依性的運算式。   

在下列範例中， **sqlReaderQuery**屬性 JSON 檔案中的指派給**Text.Format**函數所傳回的值。 這個範例也會使用一個名為**WindowStart**，它代表執行視窗的活動的開始時間的系統變數。
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>函數

下表列出 Azure 資料工廠的所有功能︰

類別 | 函數 | 參數 | 描述
-------- | -------- | ---------- | ----------- 
時間 | AddHours(X,Y) | X: DateTime <br/><br/>Y: int | 將指定的時間 X Y 小時。 <br/><br/>範例︰ 2013 年 9/5/12:00:00 PM + 2 小時 = 9/5/2013年 2:00:00 PM
時間 | AddMinutes(X,Y) | X: DateTime <br/><br/>Y: int | 增加 X Y 分鐘。<br/><br/>範例︰ 2013 年 9/15/12: 00:00 PM + 15 分鐘 = 9/15/2013年 12: 15:00 PM
時間 | StartOfHour(X) | X: Datetime | 取得由 X 的小時部分小時的開始時間。 <br/><br/>範例︰ StartOfHour 9/15/2013年 05: 10:23 PM 是 9/15/2013年 05: 00:00 PM
日期 | AddDays(X,Y) | X: DateTime<br/><br/>Y: int | 新增 X Y 天。<br/><br/>範例︰ 2013 年 9/15/12:00:00 PM + 2 天 = 9/17 2013年/12:00:00 PM
日期 | AddMonths(X,Y) | X: DateTime<br/><br/>Y: int | 新增 X Y 月份。<br/><br/>範例︰ 2013 年 9/15/12:00:00 PM + 1 個月 = 10/15/2013年 12:00:00 PM 
日期 | AddQuarters(X,Y) | X: DateTime <br/><br/>Y: int | 新增 Y * x 的 3 個月。<br/><br/>範例︰ 2013 年 9/15/12:00:00 PM + 1 季 = 12/15/2013年 12:00:00 PM
日期 | AddWeeks(X,Y) | X: DateTime<br/><br/>Y: int | 新增 Y * x 7 天<br/><br/>範例︰ 2013 年 9/15/12:00:00 PM + 1 週 = 9/22 2013年/12:00:00 PM
日期 | AddYears(X,Y) | X: DateTime<br/><br/>Y: int | 加上 X Y 年。<br/><br/>範例︰ 2013 年 9/15/12:00:00 PM + 1 年 = 9/15/2014年 12:00:00 PM
日期 | Day(X) | X: DateTime | 取得 X 日期元件。<br/><br/>範例︰ 天的 9/15/2013年 12:00:00 PM 為 9。 
日期 | DayOfWeek(X) | X: DateTime | 取得 X 週元件的日期。<br/><br/>範例︰ DayOfWeek 的 9/15/2013年 12:00:00 PM 為星期日。
日期 | DayOfYear(X) | X: DateTime | 取得以 X 的年份部分表示的年份的日期。<br/><br/>範例︰<br/>12/1/2015年︰ 天 335 2015年<br/>12/31/2015年: 2015年 365 天<br/>12/31/2016年︰ 天 366 2016 （閏年）
日期 | DaysInMonth(X) | X: DateTime | 取得以參數 X 的月份部分表示月份中的日期。<br/><br/>範例︰ DaysInMonth 的 9/15/2013年為 30 因為 9 月 30 天。
日期 | EndOfDay(X) | X: DateTime | 取得代表結束的日期 （日期元件） X 的日期時間。<br/><br/>範例︰ EndOfDay 9/15/2013年 05:10:23 PM 是 9/15/2013年 11:59:59 PM。
日期 | EndOfMonth(X) | X: DateTime | 取得以月元件的參數 X 表示月份的結尾。 <br/><br/>範例︰ EndOfMonth 05:10:23 PM 是 9/15/2013年 2013/9/30 11:59:59 PM （代表結束 9 個月的日期時間）
日期 | StartOfDay(X) | X: DateTime | 取得參數 X 日期元件以表示一天的開始。<br/><br/>範例︰ StartOfDay 9/15/2013年 05:10:23 PM 是 9/15/2013年 12:00:00 AM。
日期時間 | From(X) | X︰ 字串 | 剖析字串 X 的日期時間。
日期時間 | Ticks(X) | X: DateTime | 取得刻度參數 X 的屬性。一個刻度等於 100 奈秒。 此屬性的值代表午夜 12:00:00 年 1 月 1 日 0001 經過的刻度數目。 
文字 | Format(X) | X︰ 字串變數 | 將文字。

#### <a name="textformat-example"></a>Text.Format 範例

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

您可以使用不同的格式設定選項的說明，請參閱[自訂日期和時間格式字串](https://msdn.microsoft.com/library/8kb3ddd4.aspx)主題 (例如︰ yy 與 yyyy)。 

> [AZURE.NOTE] 使用另一個函數中的函數，當您不需要使用**$$**內部函數的前置字元。 例如︰ $$Text.Format ('PartitionKey eq \\'my_pkey_filter_value\\' 和 RowKey ge \\' {0:yyyy-MM-dd ss}\\'，Time.AddHours (SliceStart-6))。 在此範例中，請注意， **$$** **Time.AddHours**函數不使用前置詞。 
  

