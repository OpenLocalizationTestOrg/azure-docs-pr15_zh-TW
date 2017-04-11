<properties 
    pageTitle="使用作業進行偵錯及服務記錄檔中串流分析 |Microsoft Azure" 
    description="使用方法使用串流分析作業的記錄" 
    keywords="服務記錄檔"
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

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>偵錯串流分析工作使用的服務與作業的記錄檔

所有的 Azure 服務可提供作業的記錄郵件使用者管理作業相關記錄的詳細資料。 在 Azure 資料流分析，這項資訊可用於偵錯檢視工作狀態、 工作進度，例如，失敗訊息追蹤的工作進度一段時間，，從開始處理輸出。

## <a name="find-operation-logs-in-the-azure-management-portal"></a>尋找記錄 Azure 管理入口網站中的作業

兩種方法可以存取作業的記錄︰  

- 儀表板的資料流分析作業  
- 在傳統 Azure 入口網站管理服務  

## <a name="dashboard-of-the-stream-analytics-job"></a>儀表板的資料流分析作業

資料流分析工作的相對應的記錄檔的連結會顯示在工作的儀表板] 索引標籤。 如果您按一下該連結時，它會設定篩選沒有顯示最新記錄的特定工作的方式。

  ![選取 [管理服務記錄檔](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>管理服務

手動瀏覽至作業記錄的資料流分析及其他服務中 Azure 傳統入口網站︰

1.  按一下 [[傳統 Azure 入口網站](https://manage.windowsazure.com)中的**管理服務**。
2.  選取**資料流分析****類型**及工作的名稱**服務**名稱。  

  ![選取資料流狀況分析](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>尋找 Azure 入口網站中的 [稽核記錄 ##

若要尋找您的資料流分析工作 Azure 入口網站中作業的記錄，按一下 [**瀏覽**並選取**稽核記錄**。

  ![Azure 入口網站選取資料流狀況分析](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

這會開啟刀，在您的訂閱中顯示之所有資源過去 7 天的事件。  您可以篩選，按一下 [**篩選**] 命令來查看指定類型或時間範圍的事件。

  ![Azure 入口網站選取資料流狀況分析](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>取得記錄的詳細資料

您可以篩選的時間範圍，若要檢視您的工作的記錄檔的狀態。

在 Azure 管理入口網站中，按一下 [檢視選取事件的相關的更多詳細資料] 視窗底部的 [**詳細資料**] 按鈕。 

  ![選取 [詳細資料](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

在 Azure 入口網站中，按一下以查看內詳細的事件記錄項目。

  ![Azure 入口網站，選取 [詳細資料](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

之後，您可以開啟**詳細資料**刀事件上即可。

  ![Azure 入口網站，選取 [詳細資料](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>偵錯失敗的工作

在 Azure 管理入口網站中，按一下 [搜尋] 圖示，'失敗 」 的類型。 這會提供與失敗的所有記錄的結果。 

  ![偵錯失敗的工作](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

在 Azure 入口網站，您可以篩選層級來檢視**要徑**事件的訊息。

  ![Azure 入口網站的偵錯](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

您可以選取的失敗次數，其中，並按一下 [**詳細資料**錯誤的相關資訊。  有些錯誤訊息也會提供減輕問題的相關資訊。 

  ![作業詳細資料](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

如果需要連絡[支援人員](https://azure.microsoft.com/support/options/)，或提供小組透過[MSDN 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)的資訊，請注意作業詳細資料，特別是**相互關聯識別碼**。 

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
