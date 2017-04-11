<properties 
    pageTitle="如何建立資料流分析的資料分析處理工作 |Microsoft Azure" 
    description="建立資料流分析的資料分析處理工作 |學習路徑區段。"
    keywords="資料分析處理"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>如何建立資料流分析的資料分析處理工作

Azure 資料流分析中最上層的資源是串流分析工作。  它包含一或多個輸入的資料來源與表達資料轉換，查詢結果會寫入的一或多個輸出目標。 在一起這些讓使用者執行資料分析處理串流資料案例。

若要開始使用串流分析，開始建立新的資料流分析工作。  請注意，這個動作不計費的含意直到工作開始。

1.  [Azure 傳統入口網站](http://manage.windowsazure.com)或[Azure 入口網站](https://portal.azure.com/)登入 [線上。
2.  在入口網站︰**按一下 [新增**]，然後按一下 [根據您的入口網站的 [**資料服務**] 或 [**資料分析**並再按一下 [ **Azure 資料流分析**或**串流分析**然後**快速建立**。

    ![資料分析處理工作精靈](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![建立處理工作的資料分析](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  指定資料流分析作業所需的設定。
    - 在 [**工作名稱**] 方塊中，輸入名稱來識別串流分析作業。 驗證**工作名稱**時綠色的核取記號會出現在 [工作名稱] 方塊中。 **工作名稱**可能包含英數字元和 '-' 字元，而且必須是 3 和 63 字元之間。
    - 使用**地區**Azure 入口網站或**位置**中 Azure 入口網站中，指定您要執行工作的地理位置。
    - 如果使用 Azure 入口網站，請選取或建立用來作為**區域監控儲存帳戶**儲存帳戶。 此儲存帳戶用來儲存監視資料執行此區域中的所有資料流分析工作。
    - 如果使用 Azure 入口網站中，指定新的或現有的**資源群組**保留您的應用程式的相關的資源。

4.  一旦設定新的資料流分析作業選項，請按一下 [**建立資料流分析工作**]。 可能需要幾分鐘，為您建立的資料流分析工作。 若要查看狀態，您可以監視通知中心中的進度。

    ![資料分析處理工作 notfications 中心](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Azure 入口網站的資料分析處理工作建立工作](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  新的工作會顯示其狀態是 [**建立]**。 請注意已停用 [**開始**] 按鈕。 您必須設定工作輸入、 查詢和輸出，才能開始工作。

    ![資料分析處理工作工作狀態](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Azure 入口網站的資料分析處理工作的工作狀態](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
