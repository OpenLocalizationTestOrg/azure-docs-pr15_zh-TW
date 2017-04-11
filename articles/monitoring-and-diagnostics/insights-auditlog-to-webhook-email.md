<properties
    pageTitle="Azure 活動記錄通知設定 webhook |Microsoft Azure"
    description="瞭解如何使用活動記錄通知呼叫 webhooks。 "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>設定 webhook Azure 活動記錄通知

Webhooks 可讓您將 Azure 提醒通知傳送到其他系統的後置處理或自訂動作。 您可以使用 webhook 在提醒上，將它路由傳送簡訊、 登入錯誤，通知小組透過聊天室/訊息服務，或執行其他動作的任何數字的服務。 本文將說明如何在 Azure 活動記錄通知設定 webhook 及 webhook HTTP 張貼的內容的外觀。 設定和結構描述 Azure 公制通知，[而是看到此頁面](insights-webhooks-alerts.md)上的資訊。 您也可以傳送電子郵件時啟動設定活動記錄通知。

>[AZURE.NOTE] 此功能目前預覽中，因此預期變數品質及效能。

您可以將使用[Azure PowerShell Cmdlet](insights-powershell-samples.md#create-alert-rules)、[跨平台 CLI](insights-cli-samples.md#work-with-alerts)或[Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)活動記錄通知設定。

## <a name="authenticating-the-webhook"></a>驗證 webhook
您 webhook 可以驗證使用下列方法之一︰

1. **權杖為基礎的授權**webhook URI 會 token 的識別碼，例如儲存。 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **基本授權**webhook URI 會使用使用者名稱和密碼，例如儲存。 `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>內容結構描述
文章運算包含下列 JSON 內容與結構描述的所有活動記錄為基礎的通知。 此結構描述很類似公制式提醒所使用的項目。

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|項目名稱       |描述|
|---                |---|
|狀態             |用於公制的通知。 永遠針對活動記錄通知設定為 「 啟動 」。|
|內容            |事件的內容。|
|resourceProviderName|受影響的資源的資源提供者。|
|conditionType      |永遠 「 活動 」。|
|名稱               |通知規則的名稱。|
|識別碼                 |資源識別碼的提醒。|
|描述        |建立提醒的期間設定警示描述。|
|subscriptionId     |Azure 訂閱識別碼。|
|時間戳記          |事件處理要求 Azure 服務所產生的時間。|
|預設         |受影響的資源的資源識別碼。|
|resourceGroupName  |受影響的資源的 [資源] 群組的名稱|
|屬性         |設定的`<Key, Value>`組 (亦即`Dictionary<String, String>`)，包括事件的相關詳細資料。|
|事件              |包含事件的相關的中繼資料的項目。|
|授權      |事件的 RBAC 內容。 這些通常包含 「 動作 」、 「 角色 」 和 「 範圍]。|
|類別           |類別的事件。 支援的值包括︰ 管理，提醒，安全性、 ServiceHealth、 建議。|
|來電者             |執行作業、 UPN 宣告或 SPN 宣告可用性所根據的使用者電子郵件地址。 為特定系統通話 null。|
|相互關聯識別碼      |通常是字串格式 GUID。 事件的相互關聯識別碼屬於相同較大的動作，並通常共用的相互關聯識別碼。|
|eventDescription   |靜態文字事件的描述。|
|eventDataId        |事件的唯一識別碼。|
|eventSource        |Azure 服務或產生的事件的基礎結構的名稱。|
|httpRequest        |通常包含 「 clientRequestId 」、 「 clientIpAddress 」 和 「 方法 」 （例如將 HTTP 方法）。|
|層級              |下列的值的其中一個: 「 重要 」，「 錯誤 」，「 警告 」 「 資訊 」 和 「 詳細資訊 」。|
|operationId        |通常 GUID 對應到單一作業事件之間共用。|
|operationName      |在作業名稱。|
|屬性         |事件的內容。|
|狀態             |字串。 作業的狀態。 常見的值包括: 「 開始 」、 「 進度 」、 「 成功 」、 「 失敗 」、 「 使用中 」、 「 解決 」。|
|子狀態          |通常包含對應的其餘部分呼叫 HTTP 狀態碼。 也可能會包含其他說明次狀態的字串。 常見的子狀態值包括: [確定] (HTTP 狀態碼︰ 200)、 建立 (HTTP 狀態碼︰ 201)、 已接受 (HTTP 狀態碼︰ 202)、 不內容 (HTTP 狀態碼︰ 204)，錯誤的要求 (HTTP 狀態碼︰ 400)，找不到 (HTTP 狀態碼︰ 404)，衝突 (HTTP 狀態碼︰ 409)，內部伺服器錯誤 (HTTP 狀態碼︰ 500)，無法使用的服務 (HTTP 狀態碼︰ 503)，閘道逾時 (HTTP 狀態碼︰ 504)|

## <a name="next-steps"></a>後續步驟
- [深入瞭解活動記錄](monitoring-overview-activity-logs.md)
- [Azure 通知上執行 Azure 自動化指令碼 (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)
- [使用邏輯應用程式傳送簡訊透過 Twilio 從 Azure 警示](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 此範例中公制通知，但無法修改使用活動記錄通知。
- [使用邏輯應用程式傳送可寬延時間從 Azure 警示訊息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 此範例中公制通知，但無法修改使用活動記錄通知。
- [若要傳送郵件給從 Azure 通知 Azure 佇列中使用邏輯應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 此範例中公制通知，但無法修改使用活動記錄通知。
