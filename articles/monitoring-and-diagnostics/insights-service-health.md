<properties
    pageTitle="追蹤使用 Azure 監視活動記錄的 Azure 服務健康狀況 |Microsoft Azure"
    description="Azure 遇到效能降低或服務中斷時，請找出。 "
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>使用 Azure 監視活動記錄的追蹤 Azure 服務健康狀況

Azure publicizes 有服務中斷或效能降低每次。 您可以瀏覽 Azure 入口網站中，這些事件，您也可以使用[REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) ，以程式設計方式存取完整的事件。

## <a name="browse-the-service-health-logs-for-your-subscription"></a>瀏覽您的訂閱的服務健康情況記錄

1. [Azure 入口網站](https://portal.azure.com/)登入。

2. 在 [**常用]** ，您應該會看到磚，稱為 [**服務健康狀況**。 按一下它。

    ![首頁](./media/insights-service-health/Insights_Home.png)

3. 您會看到 Azure 中的所有區域的清單。 按一下以顯示活動記錄查詢來顯示服務事件的影響您的訂閱的任何過去 24 小時內的任何區域。

    ![活動記錄訂閱服務健康狀況](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. 您可以看到個別的事件的詳細資料，即可在資料表中的該事件。

5. 變更**時段**若要查看較長的時間範圍。

## <a name="next-steps"></a>後續步驟

* [監視器可用性和回應的任何網頁](../application-insights/app-insights-monitor-web-app-availability.md)與應用程式深入資訊，您可以瞭解如果您的頁面，讓已關閉。
