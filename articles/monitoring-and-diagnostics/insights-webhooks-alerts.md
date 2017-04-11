<properties
    pageTitle="Azure 公制通知設定 webhooks |Microsoft Azure"
    description="重設其他非 Azure 系統 Azure 通知。"
    authors="kamathashwin"
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
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>設定 webhook Azure 公制通知

Webhooks 可讓您將 Azure 提醒通知傳送到其他系統的後置處理或自訂動作。 您可以使用 webhook 在提醒上，將它路由傳送簡訊、 登入錯誤，通知小組透過聊天室/訊息服務，或執行其他動作的任何數字的服務。 本文將說明如何設定 webhook Azure 公制提醒及 webhook HTTP 張貼的內容的外觀。 設定和結構描述的資訊 Azure 活動記錄的通知 （提醒的事件中），[而是看到此頁面](./insights-auditlog-to-webhook-email.md)。

Azure 通知 HTTP 文章中 JSON 通知的內容設定格式，請至 webhook URI 建立提醒時，您所提供的下方，定義的結構描述。 此 URI 必須是有效的 HTTP 或 HTTPS 結束點。 啟動通知時，azure 文章邀請的每一個項目。

## <a name="configuring-webhooks-via-the-portal"></a>設定 webhooks 透過入口網站

您可以新增或更新 webhook URI 在[入口網站](https://portal.azure.com/)中的 [建立/更新通知] 畫面。

![新增提醒的規則](./media/insights-webhooks-alerts/Alertwebhook.png)

您也可以設定以張貼到 webhook URI 使用[Azure PowerShell Cmdlet](./insights-powershell-samples.md#create-alert-rules)、[跨平台 CLI](./insights-cli-samples.md#work-with-alerts)或[Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)通知。

## <a name="authenticating-the-webhook"></a>驗證 webhook

Webhook 可以驗證使用下列方法之一︰

1. **權杖為基礎的授權**webhook URI 會 token 的識別碼，例如儲存。 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **基本授權**webhook URI 會使用使用者名稱和密碼，例如儲存。 `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>內容結構描述

[文章] 作業會包含下列 JSON 內容與結構描述的所有公制為基礎的通知。

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| 功能變數 | 強制性 | 固定的值 | 備忘稿 |
| :-------------| :-------------   | :-------------   | :-------------   |
|狀態|Y|「 啟動 」，[解決]|提醒條件依據您設定的狀態。|
|內容| Y | | 通知的內容。|
|時間戳記| Y | | 觸發提醒的時間。|
|識別碼 | Y | | 每個通知規則都有唯一識別碼。|
|名稱               |Y                  |                   | 提醒的名稱。|
|描述        |Y                  |                           |提醒的描述。|
|conditionType      |Y                  |「 公制 「，」 事件 」          |支援兩種類型的通知。 根據公制條件的項目及其他根據活動記錄中的事件。 您可以使用此值，核取提醒根據公制] 或 [事件。|
|條件          |Y                  |                           | 若要檢查的特定欄位根據 conditionType。|
|metricName         |針對公制的通知  |                           |定義規則的監視公制的名稱。|
|metricUnit         |針對公制的通知  |「 位元組 」、 「 BytesPerSecond 」、 「 字數統計 」、 「 CountPerSecond 」、 「 百分比 」，」 （秒）]|     允許的度量單位。 [允許值列示如下](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx)。|
|metricValue        |針對公制的通知  |                           |警示公制的實際值。|
|閥值          |針對公制的通知  |                           |啟動提醒時臨界值。|
|windowSize         |針對公制的通知  |                           |一段時間，可用來監控通知根據臨界值的活動。 必須是 5 分鐘介於 1 天。 ISO 8601 持續時間格式。|
|timeAggregation    |針對公制的通知  |「 平均值 」、 「 姓氏 」、 「 最大值 」、 「 最小值 」、 「 無 」 的 「 總 」 | 如何收集的資料，應該要結合一段時間。 預設值是平均值。 [允許值列示如下](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx)。|
|運算子           |針對公制的通知  |                           |用來比較目前公制資料以設定臨界值的運算子。|
|subscriptionId     |Y                  |                           |Azure 訂閱識別碼。|
|resourceGroupName  |Y                  |                           |受影響的資源的 [資源] 群組的名稱。|
|resourceName       |Y                  |                           |受影響的資源的資源名稱。|
|resourceType       |Y                  |                           |受影響的資源的資源類型。|
|預設         |Y                  |                           |受影響的資源的資源識別碼。|
|resourceRegion     |Y                  |                           |地區或受影響的資源的位置。|
|portalLink         |Y                  |                           |入口網站的資源摘要頁面的直接連結。|
|屬性         |N                  |選擇性                   |設定的`<Key, Value>`組 (亦即`Dictionary<String, String>`)，包括事件的相關詳細資料。 [屬性] 欄位是選擇性的。 自訂 UI 或邏輯應用程式型工作流程中，使用者可以輸入金鑰/值，可透過內容傳遞。 將自訂屬性傳回 webhook 的替代方法是透過 webhook uri （作為查詢參數）|


>[AZURE.NOTE] 屬性欄位只可以使用[Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)加以設定。

## <a name="next-steps"></a>後續步驟

- Azure 通知和 webhooks 視訊[與 PagerDuty 整合 Azure 通知](http://go.microsoft.com/fwlink/?LinkId=627080)的詳細資訊
- [Azure 通知上執行 Azure 自動化指令碼 (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)
- [從 Azure 通知傳送簡訊 Twilio 透過使用邏輯應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [使用邏輯應用程式從 Azure 通知傳送可寬延時間的郵件](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [若要將訊息傳送給 Azure 佇列中，從 Azure 警示使用邏輯應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
