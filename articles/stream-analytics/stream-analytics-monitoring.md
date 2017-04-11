<properties 
    pageTitle="了解資料流分析工作監控 |Microsoft Azure" 
    description="了解監控串流分析工作" 
    keywords="查詢監視器"
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

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>了解資料流分析工作監控，以及如何監控查詢

## <a name="introduction-the-monitor-page"></a>簡介︰ 監視器頁面

Azure 管理入口網站和 Azure 入口網站呈現可用來監控和疑難排解查詢和工作效能的關鍵效能指標。 

Azure 管理入口網站中，按一下中執行的資料流分析作業，若要查看這些計量 [**監視器**] 索引標籤。 沒有顯示在 [監視器] 頁面的效能計量以最 1 分鐘的延遲。  

  ![監視工作儀表板](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

在 [Azure 入口網站中，瀏覽至您感興趣的文件都看到指標，並**監控**] 區段中檢視資料流分析工作。  

  ![Azure 入口網站的監控工作儀表板](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

第一次建立串流分析工作中，您必須設定診斷該區域。 若要這麼做，請按一下任何位置中的 「**監控**」 區段與**診斷**會出現。 您可以在這裡啟用診斷並指定監視資料的儲存空間帳戶。  

  ![Azure 入口網站設定查詢診斷程式](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>可用的資料流分析的指標


| 公制 | 定義 |
|--------|-------------|
| SU %使用率 | 串流的單位的指派工作的工作的 [縮放比例] 索引標籤。 此指標應達到 80%，或上述高機率事件處理可能會延遲或停止繼續進行。 |
| 輸入的事件 | 資料串流分析工作，在事件所收到的金額。 這可以用於驗證的事件會傳送到 [輸入來源。 |
| 輸出事件 | 輸出目標，在事件串流分析工作所傳送的資料量。 |
| 單事件 | 登出順序已中斷或指定調整的時間戳記，根據的事件順序原則接收的事件的數字。 這可以受到出的順序了視窗設定的設定。 |
| 資料轉換錯誤 | 依串流分析作業所產生的資料轉換錯誤的數目。 |
| 執行階段錯誤 | 執行資料流分析工作期間發生的錯誤的數目。 |
| 最遲輸入的事件 | 已調整落後送達，從 [刪除的來源或其時間戳記的事件的數目，根據慢送達了視窗設定的事件順序原則設定。 |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>自訂監控 Azure 管理入口網站中 ##

可以在圖表上最 6 指標顯示。

若要顯示相對的值 （僅適用於每個公制的最後一個值） 和絕對值值 （Y 軸顯示） 之間切換，請選取相對於] 或 [頂端的 [圖表的絕對值。

  ![相對絕對的查詢監視器](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

指標可以檢視監視器圖表中的 1 小時、 12 小時，24 小時的時間或 7 天的彙總。

若要變更的時間範圍指標圖表會顯示，會選取 1 小時、 24 小時的時間或 7 天頂端的圖表。

  ![查詢監視器時間單位](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

您可以設定會通知您的電子郵件以避免工作交叉定義的臨界值的規則。 

## <a name="customizing-monitoring-in-the-azure-portal"></a>自訂監控 Azure 入口網站 ##

您可以調整的圖表，顯示的指標類型，時間範圍中的編輯圖表設定]。 如需詳細資訊，請參閱[如何自訂監控](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

  ![Azure 入口網站的查詢監視器時間單位](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>工作狀態

Azure 傳統入口網站，您會看到工作清單中，可以檢視資料流分析工作的狀態。 您可以 Azure 傳統入口網站中的 [資料流分析] 圖示，即可看到工作清單。

| 狀態 | 定義 |
|--------|------------|
| 建立 | 工作一建立之後，但尚未啟動。 |
| 啟動 | 使用者按一下在 [開始工作，並啟動工作 |
| 執行 | 配置工作時，處理輸入，或是等候輸入 [處理程序。 如果工作會顯示的執行狀態，而不產生輸出，則可能資料處理時間視窗大或很複雜的查詢邏輯。 另一個原因可能是目前沒有任何資料傳送至工作。 |
| 停止 | 使用者在按一下 [停止工作，並停止工作。 |
| 停止 | 已停止工作。 |
| 降低 | 此狀態指出串流分析工作發生暫時性錯誤 (如 365&ex。 輸入/輸出錯誤、 錯誤處理、 轉換錯誤等）。 工作仍在執行，不過有很多所產生的錯誤。 這項工作需要客戶注意，而且客戶可以看到作業的記錄錯誤。 |
| 失敗 | 這表示作業已失敗的錯誤，因為已停止處理程序。 客戶必須偵錯錯誤才能查詢作業的記錄。 |
| 刪除 | 這表示刪除的工作。 |

## <a name="diagnosis"></a>診斷

Azure 管理入口網站中的工作儀表板提供有關要尋找 [診斷，亦即輸入輸出及/或作業的記錄。 您可以按一下連結到適當的位置，看診斷。

  ![查詢監視器錯誤](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

按一下 [上輸入或輸出資源提供診斷的詳細的資訊。 這會重新整理的最新的診斷資訊時執行的工作。

  ![查詢診斷程式](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
