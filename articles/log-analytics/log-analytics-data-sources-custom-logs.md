<properties 
   pageTitle="自訂登入記錄檔分析 |Microsoft Azure"
   description="從 Windows 及 Linux 電腦上的文字檔案收集事件記錄檔分析。  本文將說明如何定義新的自訂記錄檔和他們 OMS 存放庫中建立的記錄的詳細資料。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-logs-in-log-analytics"></a>自訂的記錄檔中記錄狀況分析

自訂記錄資料來源中記錄分析，可讓您從 Windows 及 Linux 電腦上的文字檔案收集事件。 許多應用程式，而不是標準的記錄服務，例如 Windows 事件記錄檔] 或 [系統的文字檔案記錄資訊。  一旦收集，您可以剖析記錄檔中的每一筆記錄至個別功能變數使用記錄分析的 [[自訂欄位](log-analytics-custom-fields.md)] 功能。

![自訂的記錄檔集合](media/log-analytics-data-sources-custom-logs/overview.png)

記錄檔收集必須符合下列準則。

- 登入請有每一行的單一項目，或使用符合下列格式的每一個項目開頭的其中一個時間戳記。

    YYYY MM DD SS <br>
  M/YYYY HH: MM: SS AM/PM <br>
  星期一 DD，YYYY ss
    
- 記錄檔必須不允許循環更新為新的項目與覆寫檔案。 

## <a name="defining-a-custom-log"></a>定義自訂的記錄檔

您可以使用下列程序來定義自訂的記錄檔。  捲動到本文最後的逐步解說中的 [新增自訂的記錄檔的範例。

### <a name="step-1-open-the-custom-log-wizard"></a>步驟 1。 開啟 [自訂記錄檔精靈

自訂的記錄精靈 OMS 入口網站中執行，並可讓您定義新的自訂記錄以收集。

1.  在 [OMS 入口網站中，移至 [**設定]**。
2.  按一下**資料**，然後**自訂的記錄**。
3.  根據預設，所有設定變更自動推都入所有代理程式。  Linux 代理程式的設定檔會傳送至 Fluentd 資料收集。  如果您想要修改以手動方式在每個 Linux 代理程式此檔案，然後取消核取 [*套用至我的 Linux 電腦設定] 下方*的方塊。
4.  按一下 [**新增 +**來開啟 [自訂記錄精靈。

### <a name="step-2-upload-and-parse-a-sample-log"></a>步驟 2。 上傳並剖析範例記錄檔

您開始上傳自訂的記錄檔的範例。  將會剖析精靈，並顯示的項目，以驗證此檔案中。  記錄檔分析會使用您指定要識別每一筆記錄的分隔符號。

**新的一行**是預設的分隔符號，並用於有單一項目每一行的記錄檔。  如果線條的日期和時間，在其中一個可用的格式，您可以指定**的時間戳記**分隔符號支援橫跨多個線條的項目。 

如果使用的時間戳記分隔符號，然後儲存 OMS 在每一筆記錄的 TimeGenerated 屬性會填入的記錄檔中的項目指定日期/時間。  如果使用新的一行分隔符號，然後 TimeGenerated 會填入日期和時間記錄分析收集項目。 

>[AZURE.NOTE]記錄檔分析目前將使用時間戳記分隔符號為 UTC 記錄從收集日期/時間。  此很快就會變更為使用代理程式的時區。 
 
1.  按一下 [**瀏覽**]，瀏覽至範例檔案。  請注意，這可能按鈕可能會在某些瀏覽器中標示為**選擇檔案**。
2.  按一下 [**下一步**]。 
3.  自訂記錄精靈將上傳檔案，並列出的識別記錄。
4.  變更用來找出一筆新記錄，並選取分隔符號可識別最適合您的記錄檔中的記錄的分隔符號。
5.  按一下 [**下一步**]。

### <a name="step-3-add-log-collection-paths"></a>步驟 3。 新增記錄集合路徑

您必須定義一個或多個路徑代理程式，可以在此找到自訂的記錄檔。  您可以提供的特定路徑和記錄檔的名稱，或您可以指定路徑，使用萬用字元的名稱。  這個選項支援建立新檔案，每一天或一個檔案達到特定大小的應用程式。  您也可以提供多個路徑的單一記錄檔。

例如，應用程式可能會建立記錄檔每日與 log20100316.txt 名稱中包含日期。 記錄檔的圖樣可能*記錄\*.txt*其套用到任何追蹤應用程式的記錄檔的命名配置。

下表提供範例有效的樣式，若要指定不同的記錄檔。 

| 描述 | 路徑 |
|:--|:--|
| 中的所有檔案*C:\Logs* .txt 副檔名上 Windows 代理程式 | C:\Logs\\\*.txt |
| 在*C:\Logs*開始記錄 Windows 代理程式上的以.txt 副檔名為名稱的所有檔案 | C:\Logs\log\*.txt |
| 在*/var/log/audit* .txt 副檔名 Linux 代理程式上的所有檔案 | /var/log/audit/*.txt |
| */Var/log/audit*開始記錄 Linux 代理程式上的以.txt 副檔名為名稱中的所有檔案 | /var/log/audit/log\*.txt |
  

1.  選取 Windows 或 Linux 的路徑格式的指定您要新增。
2.  輸入路徑上並按一下**+**] 按鈕。
3.  重複此程序的任何其他的路徑。

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>步驟 4。 提供的名稱和描述的記錄檔

您指定的名稱會用於的記錄類型，如上述。  它會永遠結尾 _CL 區分作為自訂的記錄。

1.  輸入登入的名稱。  ** \_CL**後置字元會自動提供。
2.  新增選擇性的**描述**。
3.  按一下 [**下一步**儲存自訂的記錄檔定義。

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>步驟 5。 驗證收集自訂的記錄檔
將其最多可能需要初始資料為小時從新的自訂記錄會出現在記錄檔分析。  它會啟動收集項目在路徑中找到記錄檔中所指定點您定義自訂的記錄檔。  它不會保留自訂的記錄建立時，您上傳的項目，但它會收集找到記錄檔中的現有項目。

一旦記錄分析開始收集從自訂的記錄，其記錄將可供使用記錄搜尋。  使用**類型**以提供自訂的記錄，您在查詢中的名稱。

>[AZURE.NOTE] 搜尋遺失 RawData 屬性時，您可能需要關閉並重新開啟瀏覽器。

### <a name="step-6-parse-the-custom-log-entries"></a>步驟 6。 剖析的自訂的記錄項目

整個記錄項目會儲存在名為**RawData**的單一屬性。  您最有可能會想要個別的不同的項目，將儲存於記錄中的個別內容的每一個項目中的資訊。  您執行這項使用記錄分析的[自訂欄位](log-analytics-custom-fields.md)功能。

這裡並未提供詳細的步驟剖析的自訂的記錄項目。  請參閱的[自訂欄位](log-analytics-custom-fields.md)文件，這項資訊。

## <a name="disabling-a-custom-log"></a>停用自訂的記錄檔

當已建立，但您可以在來移除其集合路徑的所有停用，您無法移除自訂的記錄檔定義。

1.  在 [OMS 入口網站中，移至 [**設定]**。
2.  按一下**資料**，然後**自訂的記錄**。
3.  若要停用的自訂的記錄檔定義旁，按一下 [**詳細資料**。
4.  移除每個自訂的記錄檔定義的集合路徑。


## <a name="data-collection"></a>資料收集

記錄檔分析會收集新的項目從每個自訂的記錄檔約每 5 分鐘。  代理程式會收集從每個記錄檔中的 [錄製位置。  如果代理程式離線一段時間，然後記錄分析會收集項目從上次停止的地方，即使這些項目建立時代理程式已離線。

單一屬性稱為**RawData**寫入記錄項目的全部內容。  您可以將它剖析到多個屬性來分析及搜尋分別以建立自訂的記錄後，定義[自訂欄位](log-analytics-custom-fields.md)。


## <a name="custom-log-record-properties"></a>自訂的記錄檔記錄內容

自訂的記錄會有下列表格中所提供的記錄名稱與內容類型。

| 屬性 | 描述 |
|:--|:--|
| TimeGenerated | 日期及時間記錄收集記錄分析。  如果登入使用時間分隔符號這是從項目所收集時間。 |
| SourceSystem  | 從收集代理程式的記錄類型。 <br> OpsManager – Windows 代理程式]、 [直接連線或是 SCOM <br> Linux – 所有 Linux 代理程式  |
| RawData             | 完整收集項目的文字。 |
| ManagementGroupName | 是 SCOM 代理程式的 [管理] 群組的名稱。  其他代理程式 」，這是 AOI-\<工作區識別碼\> |


## <a name="log-searches-with-custom-log-records"></a>使用自訂的記錄的記錄檔搜尋

自訂的記錄的記錄會儲存在 OMS 存放庫，就像其他任何資料來源的記錄。  使用者將能夠類型，比對時，您可以在搜尋中使用 [類型] 屬性，以擷取從特定的記錄中收集的記錄，定義記錄，您提供的名稱。

下表提供不同的自訂的記錄檔中擷取記錄記錄搜尋範例。

| 查詢 | 描述 |
|:--|:--|
| 輸入 = MyApp_CL | 從自訂的所有事件都記錄命名的 MyApp_CL。 |
| 輸入 = MyApp_CL Severity_CF = 錯誤 | 從自訂的記錄檔名 MyApp_CL 為自訂欄位中的*錯誤*值的所有事件都命名*Severity_CF*。 |




## <a name="sample-walkthrough-of-adding-a-custom-log"></a>新增自訂的記錄檔的範例逐步解說

下一節逐步解說，建立自訂的記錄檔的範例。  收集範例記錄檔的開始日期和時間然後逗號每一行的單一項目具有分隔程式碼、 狀態及訊息的欄位。  數個範例項目如下所示。

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>上傳並剖析範例記錄檔

我們提供一個記錄檔，並可以看到將收集的事件。  在此情況下新的一行是足夠的分隔符號。  如果在記錄中單一項目可能橫跨多個線條，然後使用所需的時間戳記分隔符號。

![上傳並剖析範例記錄檔](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>新增記錄集合路徑

記錄檔會位於*C:\MyApp\Logs*。  每日使用圖樣*appYYYYMMDD.log*中包含日期的名稱會建立新檔案。  此記錄足夠模式應*C:\MyApp\Logs\\\*.log*。

![記錄集合路徑](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>提供的名稱和描述的記錄檔

我們會使用*MyApp_CL*並輸入的名稱中**描述**。

![記錄名稱](media/log-analytics-data-sources-custom-logs/log-name.png)


### <a name="validate-that-the-custom-logs-are-being-collected"></a>驗證收集自訂的記錄檔

我們使用的查詢*類型 = MyApp_CL*從收集記錄傳回的所有記錄。

![記錄無自訂欄位的查詢](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>剖析的自訂的記錄項目

我們使用自訂欄位來定義*EventTime**程式碼*、 [*狀態*]，然後*郵件*欄位，我們可以看到查詢所傳回的記錄中的差異。

![記錄與自訂欄位的查詢](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>後續步驟

- 使用[自訂欄位](log-analytics-custom-fields.md)來剖析自訂的記錄檔中的項目至個別功能變數。
- 深入了解[記錄搜尋](log-analytics-log-searches.md)，以分析的資料來源與解決方案從收集的資料。 