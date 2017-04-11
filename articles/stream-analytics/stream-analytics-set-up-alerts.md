<properties
    pageTitle="設定提醒的資料流分析中的查詢 |Microsoft Azure"
    description="了解資料流分析警示"
    keywords="設定提醒"
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


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>設定 Azure 資料流分析工作的提醒

## <a name="introduction-monitor-page"></a>簡介︰ 監視器頁面

您可以設定提醒，來觸發公制達到您指定的條件時的通知。

例如，「 輸出事件前 15 分鐘時 < 100，將電子郵件通知傳送至電子郵件識別碼︰ xyz@company.com”。

規則可以在入口網站，透過標準設定，或可設定[以程式設計方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)作業的記錄資料。

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>設定提醒，透過 Azure 傳統入口網站

有兩種方法可以設定提醒 Azure 傳統入口網站中︰  

1.  資料流分析工作的 [**監視器**] 索引標籤  
2.  在管理服務中的 [作業登入  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>設定提醒，在入口網站中工作的 [監視器] 索引標籤

1.  在 [監視器] 索引標籤中選取 [公制並按一下儀表板，底部的 [**新增規則**] 按鈕，設定您的規則。  

    ![儀表板](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  定義的名稱和描述的提醒  

    ![建立規則](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  輸入臨界值]、 [通知評估視窗及 [提醒的動作  

    ![定義條件](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>設定提醒，透過作業記錄

1.  移至[Azure 傳統入口網站](https://manage.windowsazure.com)中的管理服務中的 [**通知**] 索引標籤。  
2.  按一下 [**新增規則**  

    ![準則](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  定義的名稱和描述的提醒。 選取 「 資料流分析 ' 服務類型，以及工作名稱以服務名稱。  

    ![定義通知](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>設定提醒 Azure 入口網站中 ##

在 Azure 入口網站中，瀏覽至您感興趣警示串流分析作業並按一下 [**監控**」 區段。  在開啟**公制**刀，按一下 [**新增提醒**] 命令。

  ![Azure 入口網站設定](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

您可以命名通知的規則，並選擇會顯示通知電子郵件中的描述。

當您選取指標會選擇條件和臨界值的公制。

  ![Azure 入口網站的選取公制](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

如需詳細 Azure 入口網站中設定警示的詳細資訊，請參閱[接收提醒通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。  

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
