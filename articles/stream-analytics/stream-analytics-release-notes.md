<properties 
    pageTitle="資料流分析版本資訊 |Microsoft Azure" 
    description="資料流分析版本資訊" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="stream-analytics-release-notes"></a>資料流分析版本資訊

## <a name="notes-for-04152016-release-of-stream-analytics"></a>備忘稿串流分析 04/15/2016年版本 ##

在這個版本包含下列的更新。

標題 | 描述
---|---
一般的顯示狀態，Power BI 的輸出  | [Power BI 輸出](stream-analytics-power-bi-dashboard.md)現在推出。 已移除 Power bi 的 90 天授權到期日。 授權要更新的案例的詳細資訊，請參閱建立 Power BI 儀表板的 [[更新授權](stream-analytics-power-bi-dashboard.md#Renew-authorization)] 區段。

## <a name="notes-for-03032016-release-of-stream-analytics"></a>備忘稿串流分析 03/03 2016年版本 ##

在這個版本包含下列的更新。

標題 | 描述
---|---
新的資料流分析查詢語言項目  | SAQL 現在有[GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN 頁面")、 [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN 頁面")和[REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH msdn 文章頁面")。

## <a name="notes-for-12102015-release-of-stream-analytics"></a>備忘稿串流分析 12/10/2015年版本 ##

在這個版本包含下列的更新。

標題 | 描述
---|---
REST API 版本更新 | REST API 版本已更新 2015年-10-01 到。 詳細資料可以在[串流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)及[電腦學習整合的資料流分析](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)MSDN 上找到。
Azure 機器學習整合 | 使用此版本提供支援 Azure 電腦學習使用者定義的函數。 請參閱[教學課程](stream-analytics-machine-learning-integration-tutorial.md)的詳細資訊，以及[一般的部落格公告](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx)。

## <a name="notes-for-11122015-release-of-stream-analytics"></a>備忘稿串流分析 11/12/2015年版本 ##

在這個版本包含下列的更新。

標題 | 描述
---|---
選取新行為 | 選取資料流分析中的已擴充允許 * 為屬性存取子的巢狀的記錄。 如需詳細資訊，請參閱[http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "複雜的資料類型")。

## <a name="notes-for-10222015-release-of-stream-analytics"></a>備忘稿串流分析 10/22/2015年版本 ##

在這個版本包含下列的更新。

標題 | 描述
---|---
其他查詢語言功能 | 資料流分析已展開查詢語言，包括下列功能︰ [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx)、 [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx)、 [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx)、[樓面](https://msdn.microsoft.com/library/azure/mt605240.aspx)、 [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx)、[號](https://msdn.microsoft.com/library/azure/mt605290.aspx)、[方形](https://msdn.microsoft.com/library/azure/mt605288.aspx)，以及[SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx)。
移除彙總的限制  | 此版本在查詢中移除 15 彙總的限制。 現在是不受限制的每個查詢的彙總數目。
新增的群組的 System.Timestamp 功能 | [群組依據](https://msdn.microsoft.com/library/azure/dn835023.aspx)函數現在可讓 window_type 或[System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx)。
新增的位移解決和跳 windows | 根據預設，[解決](https://msdn.microsoft.com/library/azure/dn835055.aspx)與[Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows 會對齊針對時間為零 (1/1/0001 12:00:00 AM UTC)。 新的 （選擇性） 參數 'offsetsize 」 可讓指定自訂 offset （或對齊方式）。


## <a name="notes-for-09292015-release-of-stream-analytics"></a>備忘稿串流分析 09/29/2015年版本 ##

在這個版本包含下列的更新。

標題 | 描述
---|---
Azure IoT 套件公用預覽 | 資料流分析會包含在公用 Azure IoT 套件的預覽。
Azure 入口網站整合 | Azure 管理入口網站中的持續存在，除了串流分析現在已在[Azure 入口網站](https://azure.microsoft.com/overview/preview-portal/)整合。 請注意，[預覽] 入口網站中的功能是目前的資料流分析功能的子集合中提供 Azure 管理入口網站中，不支援的瀏覽器中查詢測試，Power BI 輸出設定，並瀏覽功能至或您有權存取的訂閱中建立新的輸入與輸出資源。
支援 DocumentDB 輸出 | [DocumentDB](https://azure.microsoft.com/services/documentdb/)現在可以輸出資料流分析工作。
IoT 中心輸入支援 | 資料流分析工作現在可以內嵌 IoT 集線器的資料。
時間戳記的異質性事件 | 當單一資料流包含多個事件類型有時間戳記不同欄位中時，您現在可以使用[時間戳記，](http://msdn.microsoft.com/library/mt573293.aspx)使用運算式來指定每個案例的時間戳記不同欄位。

## <a name="notes-for-09102015-release-of-stream-analytics"></a>備忘稿串流分析 09/10/2015年版本 ##

在這個版本包含下列的更新。

標題|描述
---|---
支援中的群組|若要啟用共用的資料與其他 Power BI 的使用者，串流分析工作現在可以在您的 Power BI 帳戶[中](stream-analytics-define-outputs.md#power-bi)群組撰寫。

## <a name="notes-for-08202015-release-of-stream-analytics"></a>備忘稿串流分析 08/20/2015年版本 ##

在這個版本包含下列的更新。

標題|描述
---|---
新增的最後一個函數 |[最後一個](http://msdn.microsoft.com/library/mt421186.aspx)函數現提供資料流分析工作，讓您以擷取特定時間範圍內的事件資料流中最新的事件。
新的陣列函數|陣列函數[GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx)、 [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx)和[GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx)現在可供使用。
新的記錄功能|記錄功能[GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx)和[GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx)現在可供使用。

## <a name="notes-for-07302015-release-of-stream-analytics"></a>備忘稿串流分析 07/30/2015年版本 ##

在這個版本包含下列的更新。

標題|描述
---|---
從 Azure 識別碼分離 power BI 組織識別碼|此功能讓[Power BI 輸出](stream-analytics-power-bi-dashboard.md)ASA （Live Id 或組織識別碼） 任何 Azure 帳戶類型] 下的工作。 此外，您可以使用組織識別碼 Azure 帳戶，並使用另一個授權 Power BI 輸出。
支援服務匯流排佇列輸出|[服務匯流排佇列](stream-analytics-define-outputs.md#service-bus-queues)輸出現在可在 [串流分析工作。
支援服務匯流排主題輸出|[服務匯流排主題](stream-analytics-define-outputs.md#service-bus-topics)輸出現在可在 [串流分析工作。

## <a name="notes-for-07092015-release-of-stream-analytics"></a>備忘稿串流分析 07/09/2015年版本 ##

在這個版本包含下列的更新。


標題|描述
---|---
自訂 Blob 輸出分割|Blob 儲存體輸出寫入 blob 的輸出的結構及輸出資料路徑資料夾結構的格式，現在允許指定的頻率的選項。 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>備忘稿串流分析 05/03/2015年版本 ##

在這個版本包含下列的更新。


標題|描述
---|---
增加出的順序了視窗最大值|現在 59:59 (MM:SS) 出的順序了視窗的大小上限。
JSON 輸出格式︰ 分隔線或陣列|現在有選項輸出 Blob 儲存體或事件] 中心內，將兩陣列的 JSON 物件，或使用新的一行分隔 JSON 物件輸出時。 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>備忘稿串流分析 04/16/2015年版本 ##


標題|描述
---|---
延遲的 Azure 儲存體帳戶設定|在區域中建立資料流分析工作第一次時，會提示您建立新的儲存空間帳戶或指定監視該區域中的資料流分析工作的現有帳戶。 設定監控延遲，因為在 30 分鐘內建立相同的區域中的另一個資料流分析工作都會提示指定的第二個的儲存空間帳戶，而非顯示監控儲存帳戶下拉式清單中的最近設定項目。 若要避免建立不需要的儲存空間帳戶，請等候 30 分鐘後之前佈建該區域中的其他工作的第一次建立區域中的工作。
工作升級|此時，串流分析不支援即時編輯的定義或設定執行中的工作。 若要變更的輸入、 輸出、 查詢、 縮放比例或執行中的工作設定，您必須先停止工作。
從 [輸入來源推斷資料類型|如果不是 CREATE TABLE 陳述式，輸入的類型衍生自輸入格式，例如 csv 的所有欄位都是字串。 欄位必須明確地轉換為正確的類型，使用轉換函數，以避免類型不相符的錯誤。
遺失的欄位被 outputted 為 null 值|參照沒有出現在 [輸入來源的欄位，將會導致輸出事件中的 null 值。
陳述式必須在 SELECT 陳述式|在查詢中 SELECT 陳述式必須遵循定義陳述式使用子查詢。
記憶體不足的問題|重新啟動-單事件及/或複雜的查詢維護大量的狀態可能會導致工作中的工作結果的記憶體用完大型容錯串流分析工作。 開始及停止作業會顯示在工作的作業的記錄。 若要避免此問題，請跨多個資料分割縮放出查詢。 在未來的版本，這項限制將會透過降級效能受影響的工作，而不是將它們重新啟動收件者。
沒有裝載時間戳記的大型 blob 輸入可能會導致-記憶體問題|如果未指定的時間戳記欄位的時間戳記，透過使用從 Blob 儲存體大型檔案可能會導致損毀的資料流分析工作。 若要避免此問題，請大小每個 blob 底下 10 MB。
SQL 資料庫事件大量限制|時使用的輸出目標 SQL 資料庫，更高的輸出大量資料內容可能會導致串流分析作業逾時。 若要解決此問題，請使用彙總或篩選運算子來減少輸出音量或改為做為輸出目標選擇 Azure Blob 儲存體或事件集線器。
中的資料集只能包含一個資料表|中不支援多個資料表中指定的資料集。

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
