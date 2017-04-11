<properties
    pageTitle="您可以使用自動調整大小的動作，傳送電子郵件及 webhook 提醒通知。 |Microsoft Azure"
    description="請參閱如何使用自動調整大小動作呼叫網頁 Url 或 Azure 監視器中傳送電子郵件通知。 "
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
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>使用自動調整大小動作來傳送電子郵件及 webhook 提醒通知中 Azure 監視器

本文說明您如何設定引動程序，讓您可以打電話給特定的 web Url 或傳送電子郵件根據 Azure 中的 [自動調整大小動作。  

## <a name="webhooks"></a>Webhooks
Webhooks 可讓您將 Azure 提醒通知傳送到其他系統的後置處理或自訂通知。 例如，路由至可以處理連入 web 要求傳送簡訊，記錄錯誤，通知服務團隊使用聊天室訊息的服務等的提醒。Webhook URI 必須是有效的 HTTP 或 HTTPS 結束點。

## <a name="email"></a>電子郵件
電子郵件傳送至任何有效的電子郵件地址。 系統管理員和正在規則的訂閱的共同管理員也會收到通知。


## <a name="cloud-services-and-web-apps"></a>雲端服務與 Web 應用程式
您可以選擇加入集從 Azure 入口網站的雲端服務與伺服器陣列 （Web 應用程式）。

- 選擇 [**依比例縮放**公制]。

![縮放](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>虛擬機器縮放設定
建立與資源管理員 （虛擬機器比例組） 較新的虛擬機器，您可以設定這個使用 REST API、 資源管理員範本、 PowerShell 和 CLI。 入口網站的介面尚無法使用。
使用時的 REST API 或資源管理員的範本，包含下列選項的通知項目。

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|功能變數                              |強制嗎？ |描述|
|---                                |---        |---|
|作業                          |[是]        |值必須是 「 小數位數]|
|sendToSubscriptionAdministrator    |[是]        |值必須是"true"或"false"|
|sendToSubscriptionCoAdministrators |[是]        |值必須是"true"或"false"|
|customEmails                       |[是]        |值可以是空值的 [或字串陣列的電子郵件|
|webhooks                           |[是]        |值可以是空值或有效 Uri|
|serviceUri                         |[是]        |有效的 https Uri|
|屬性                         |[是]        |值必須是空白的 {} 或最多可以包含關鍵值組|


## <a name="authentication-in-webhooks"></a>在 webhooks 驗證
有兩個驗證 URI 表單︰

1. 權杖基底驗證，您將 webhook URI 儲存為查詢參數的權杖識別碼。 例如，https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. 基本驗證的使用者識別碼和密碼，您的使用位置。 例如，https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>自動調整大小通知 webhook 內容結構描述
產生自動調整大小通知時，所包含下列的中繼資料的 webhook 內容︰

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|功能變數  |強制嗎？|    描述|
|---|---|---|
|狀態 |[是]    |指出自動調整大小動作所產生的狀態|
|作業| [是] |執行個體增加，它位於 [縮放比例出 」，並執行個體減少，它位於 「 小數位數的 「|
|內容|   [是] |自動調整大小動作內容|
|時間戳記| [是] |[自動調整大小] 動作觸發時的時間戳記|
|識別碼 |[是]|   資源管理員 ID，自動調整大小] 設定|
|名稱   |[是]|   自動調整大小設定的名稱|
|詳細資料|   [是] |自動調整大小服務採取動作，並執行個體次數] 中的變更的說明|
|subscriptionId|    [是] |訂閱 ID 縮放的目標資源|
|resourceGroupName| [是]|    資源群組名稱縮放的目標資源|
|resourceName   |[是]|   縮放為目標資源的名稱|
|resourceType   |[是]|   三種支援值: 「 microsoft.classiccompute/domainnames/slots/roles 」-雲端服務角色、 「 microsoft.compute/virtualmachinescalesets 」 的虛擬機器比例組和 「 Microsoft.Web/serverfarms 」-Web 應用程式|
|預設 |[是]|資源管理員資源識別碼之目標的縮放|
|portalLink |[是]    |[摘要] 頁面的目標資源 azure 入口網站的連結|
|oldCapacity|   [是] |目前 （舊版） 執行個體的計數時自動調整大小所花費的小數位數的巨集指令|
|newCapacity|   [是] |新增自動調整大小調整資源的執行個體計數|
|屬性|    無| 選用。 設定 < 機碼，值 > 組 （例如字典 < 字串、 字串 >）。 [屬性] 欄位是選擇性的。 自訂使用者介面或邏輯基礎的應用程式工作流程，您可以輸入索引鍵和可以使用內容傳遞的值。 將自訂屬性傳回傳出 webhook 呼叫替代方法是使用 webhook URI 本身 (查詢參數）|
