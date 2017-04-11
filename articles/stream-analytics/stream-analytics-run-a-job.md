<properties 
    pageTitle="如何啟動串流工作中串流分析 |Microsoft Azure" 
    description="如何執行 Azure 資料流分析中的 [串流工作 |學習路徑區段。"
    keywords="串流工作"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>如何執行串流工作中 Azure 資料流狀況分析

當工作輸入查詢與輸出所有已指定您就可以開始串流分析作業。

若要啟動您的工作︰

1.  在 Azure 傳統的入口網站中從工作儀表板中，按一下 [在頁面底部的 [**開始**]。

    ![啟動工作] 按鈕](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    在 Azure 入口網站中，按一下 [在您的工作] 頁面頂端的 [**開始**]。

    ![Azure 入口網站開始工作] 按鈕](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  指定**啟動輸出**值來判斷時產生的輸出開始此工作。 先前尚未啟動的工作的預設設定，則**工作開始的時間**，這表示工作會立即開始處理資料。 您也可以在過去的經驗 （適用於使用歷史資料） 或 （若要延遲直到未來的時間處理） 未來指定**自訂**時間。 當工作具有先前啟動和停止，選項**上次停止的時間**就可以使用才能繼續工作的最後一個輸出時間並避免資料遺失。  

    ![開始串流工作時間](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure 入口網站開始串流工作時間](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  確認您的選取範圍。 工作狀態會變更為*開始*，並開始作業會引進了移到*執行*。 您可以監視進度的**通知中心**中的 [**開始**] 作業︰

    ![串流工作進度](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Azure 串流工作進度的入口網站](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
