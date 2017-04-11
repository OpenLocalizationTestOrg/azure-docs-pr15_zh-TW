<properties
    pageTitle="收到警示通知 Azure 服務 |Microsoft Azure"
    description="通知規則條件符合時收到通知。"
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>收到通知的通知

您會收到通知監控指標，或 Azure 服務上的事件。

上一個公制值的通知規則，指定度量單位價值交叉指派，臨界值時通知規則會變成作用中，並可以傳送通知。 事件的提醒規則，在*每個*事件，或，只有在特定數目的事件發生時，規則時，可以傳送通知。

當您建立提醒的規則時，您可以選取選項以便傳送電子郵件通知服務系統管理員和共同管理員或其他您可以指定的系統管理員。 規則會變成作用中時，以及解決通知的條件時，會傳送通知電子郵件。

您可以使用的[REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)設定，並以程式設計方式取得警示規則的相關資訊。

## <a name="create-an-alert-rule"></a>建立提醒的規則

1. 在[入口網站](https://portal.azure.com/)中，按一下 [**瀏覽]**，然後您感興趣監控資源。

2. 按一下在**作業**lens 中的 [**提醒的規則**] 方塊。

3. 按一下 [**新增提醒**] 命令。

    ![新增提醒](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. 您可以命名通知的規則，並選擇會顯示通知電子郵件中的描述。

5. 當您選取**指標**會選擇條件和臨界值的公制。 這是一段時間 Azure 用來監控和繪圖提醒的活動。

    ![條件和臨界值](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. 您也可以選擇 [**事件**]，並特定事件發生時收到通知。

    ![事件](./media/insights-receive-alert-notifications/Insights_Events.png)

7. 最後，您可以選擇傳送電子郵件通知給系統管理員負責。

按一下 [**儲存**後，在幾分鐘內您必須掌握資訊每當您選擇的公制超過臨界值。

## <a name="managing-your-alert-rules"></a>管理提醒規則

當您建立提醒的規則之後時，您可以檢視您的提醒的預覽閥值相比較的公制前一天。

![事件](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


當然您就可以編輯**此通知的規則，並停用**或**啟用**，如果您想要暫時停止接收相關的通知。

## <a name="next-steps"></a>後續步驟

* [在您的通知設定 webhooks](insights-webhooks-alerts.md)各種頻道傳送通知
* [監視服務指標](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
* [啟用監控和診斷](insights-how-to-use-diagnostics.md)以您的服務收集詳細的頻率高的度量。
* [監視器可用性和回應的任何網頁](../application-insights/app-insights-monitor-web-app-availability.md)與應用程式深入資訊，您可以瞭解如果您的頁面，讓已關閉。
* [監視應用程式效能](../application-insights/app-insights-azure-web-apps.md)如果您想要了解完全如何程式碼執行雲端中。
* [檢視事件及稽核記錄檔](insights-debugging-with-events.md)若要瞭解所有項目發生在您的服務。
* 若要找出 Azure 遇到效能降低或服務中斷時[追蹤服務健康狀況](insights-service-health.md)。
