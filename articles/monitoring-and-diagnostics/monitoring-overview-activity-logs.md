<properties
    pageTitle="Azure 活動記錄概觀 |Microsoft Azure"
    description="瞭解 Azure 活動記錄的功能以及如何使用它若要瞭解您 Azure 訂閱中發生的事件。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Azure 活動記錄的概觀
**Azure 活動記錄**是提供深入您的訂閱中的資源執行的作業的記錄。 活動記錄之前稱為 「 稽核記錄 」 或 「 作業的記錄 」，因為它會報告您的訂閱的控制項平面事件。 使用活動記錄，您可以決定 ' 什麼，人員，以及何時 」 的任何撰寫您的訂閱中的資源所採取的作業 （放入，文章，刪除）。 您也可以瞭解的狀態作業與其他相關的屬性。 活動記錄不包含已讀取 （取得） 作業。

活動記錄[診斷記錄](./monitoring-overview-of-diagnostic-logs.md)] 下，從不同的資源所發出的所有記錄。 這些記錄提供作業的資源，而不是在該資源的作業相關資料。

您可以從您的活動登入，使用 [Azure 入口網站 CLI PowerShell cmdlet 來擷取事件和 Azure 監視器 REST API。

檢視此[視訊介紹活動記錄](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz)。  

## <a name="what-you-can-do-with-the-activity-log"></a>您可以執行的活動記錄檔
以下是一些您可以執行的活動記錄工作︰

- 查詢和**Azure 入口網站**中檢視。
- 透過 REST API、 PowerShell Cmdlet 或 CLI 查詢。
- [建立電子郵件或 webhook 觸發關閉活動記錄事件的提醒。](./insights-auditlog-to-webhook-email.md)
- [儲存至保存或手動檢查的**儲存空間的帳戶**](./monitoring-archive-activity-log.md)。 您可以指定使用**記錄設定檔**的保留時間 （以天）。
- 在使用[**中的內容套件**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)中分析。
- 協力廠商服務或自訂分析解決方案，例如中 ingestion，[串流到**事件集線器**](./monitoring-stream-activity-logs-event-hubs.md)。

## <a name="export-the-activity-log-with-log-profiles"></a>匯出活動記錄與記錄設定檔
**記錄檔的設定檔**來控制如何匯出您的活動記錄。 使用記錄檔，您可以設定︰

- （儲存帳戶或事件集線器） 位置傳送活動記錄
- 要傳送的事件類別 （寫入、 刪除、 動作）
- 應匯出的區域 （位置）
- 儲存帳戶 – 零天的保留多久保留活動記錄，就表示記錄會保持不限次數。 否則，值可以是任何數字 1 和 2147483647 之間的天數。 如果保留原則設定，但在儲存帳戶中儲存記錄檔] 會停用 （例如，如果僅選取事件集線器或 OMS 選項），保留原則有任何效果。

您可以透過活動記錄刀，在入口網站中的 [匯出] 選項設定這些設定。 也可以設定以程式設計方式[使用 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、 PowerShell cmdlet 或 CLI。 訂閱只能有一個記錄設定檔。

### <a name="configure-log-profiles-using-the-azure-portal"></a>設定記錄使用 Azure 入口網站的設定檔
您可以將串流到事件集線器活動記錄，或將它們儲存在儲存帳戶 Azure 入口網站中使用 [匯出] 選項。

1. 瀏覽至**活動記錄**刀使用左側的入口網站的功能表。

    ![瀏覽至活動記錄中入口網站](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 按一下 [刀頂端的 [**匯出**] 按鈕。

    ![在入口網站中的 [匯出] 按鈕](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在出現的刀，您可以選擇︰  
    - 您要匯出的事件的區域
    - 儲存帳戶至您想要儲存的事件
    - 您想要保留下列事件儲存區中的天數。 設定為 0 天會永久保留記錄。
    - 在其中您想要針對串流這些事件建立事件中心服務匯流排命名空間。

    ![匯出活動記錄刀](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 按一下 [儲存這些設定的 [**儲存**]。 設定會立即套用至您的訂閱。

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>設定使用 Azure PowerShell Cmdlet 的記錄設定檔
#### <a name="get-existing-log-profile"></a>取得現有記錄的設定檔
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>新增記錄檔
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| 屬性         | 所需 | 描述   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱             | [是]      | 記錄設定檔的名稱。                                 |
| StorageAccountId | 無       | 儲存帳戶活動記錄要儲存的資源識別碼。                         |
| serviceBusRuleId | 無       | 您想要有中建立的事件集線器服務匯流排命名空間服務匯流排規則識別碼。 使用此格式字串︰ `{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置        | [是]      | 逗點分隔的區域，您要收集活動記錄事件的清單。              |
| RetentionInDays  | [是]      | 保留哪些事件，介於 1 到 2147483647 之間的日數。 零值會永遠儲存記錄 （永久）。 |
| 類別       | 無       | 逗點分隔應該收集的事件類別清單。 可能的值會寫入、 刪除和動作。                                 |

#### <a name="remove-a-log-profile"></a>移除記錄檔
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>設定使用 Azure 跨平台 CLI 記錄設定檔
#### <a name="get-existing-log-profile"></a>取得現有記錄的設定檔
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name`屬性應該記錄設定檔的名稱。

#### <a name="add-a-log-profile"></a>新增記錄檔
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| 屬性         | 所需 | 描述   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱             | [是]      | 記錄設定檔的名稱。                                 |
| storageId        | 無       | 儲存帳戶活動記錄要儲存的資源識別碼。                         |
| serviceBusRuleId | 無       | 您想要有中建立的事件集線器服務匯流排命名空間服務匯流排規則識別碼。 使用此格式字串︰ `{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置        | [是]      | 逗點分隔的區域，您要收集活動記錄事件的清單。              |
| retentionInDays  | [是]      | 保留哪些事件，介於 1 到 2147483647 之間的日數。 零值會永遠儲存記錄 （永久）。     |
| 類別       | 無       | 逗點分隔應該收集的事件類別清單。 可能的值會寫入、 刪除和動作。                                 |

#### <a name="remove-a-log-profile"></a>移除記錄檔
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>事件結構描述
活動記錄中的每個事件具有 JSON blob 類似此範例中︰

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| 項目名稱         | 描述             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 授權        | Blob RBAC 內容中的事件。 通常包含 「 動作 」、 「 角色 」 和 「 範圍] 屬性。|
| 來電者               | 執行的作業、 UPN 宣告或根據 [可用的 SPN 宣告之使用者的電子郵件地址。|
| 頻道             | 下列的值的其中一個: 「 系統 」、 「 作業 」|
| 相互關聯識別碼        | 通常是字串格式 GUID。 共用的相互關聯識別碼的事件屬於相同的烏柏動作。   |
| 描述          | 事件的靜態文字描述。                              |
| eventDataId          | 事件的唯一識別碼。    |
| eventSource          | Azure 服務或產生此事件的基礎結構的名稱。    |
| httpRequest          | Blob 描述 Http 要求。 通常包含 「 clientRequestId 」、 「 clientIpAddress 」 和 「 方法 」 （HTTP 方法。 For example，將)。                            |
| 層級                | 事件的層級。 下列的值的其中一個: 「 重要 」，「 錯誤 」，「 警告 」 「 資訊 」 和 「 詳細資訊 」  |
| resourceGroupName    | 受影響的資源的 [資源] 群組的名稱。               |
| resourceProviderName | 受影響資源的資源提供者的名稱             |
| resourceUri          | 受影響的資源的資源識別碼。                               |
| operationId          | GUID，會對應到單一作業事件之間共用。         |
| operationName        | 在作業名稱。  |
| 屬性           | 設定的`<Key, Value>`描述事件的詳細資料的成對 （也就是字典）。                                |
| 狀態               | 描述作業的狀態字串。 一些常見的值︰ 進度，成功、 失敗，使用中時，啟動已解決。                                |
| 子狀態            | 相對應的其餘部分的 HTTP 狀態碼通常通話，但也可以包含其他說明，請在子狀態，例如這些常見的值的字串: [確定] (HTTP 狀態碼︰ 200)、 建立 (HTTP 狀態碼︰ 201)、 已接受 (HTTP 狀態碼︰ 202)、 不內容 (HTTP 狀態碼︰ 204)，錯誤的要求 (HTTP 狀態碼︰ 400)，找不到 (HTTP 狀態碼︰ 404)，衝突 （HTTP 狀態碼: 409)，內部伺服器錯誤 (HTTP 狀態碼︰ 500)、 服務無法使用 (HTTP 狀態碼︰ 503)，閘道逾時 (HTTP 狀態碼︰ 504)。 |
| eventTimestamp       | Azure 服務處理對應事件要求產生事件時的時間戳記。     |
| submissionTimestamp  | 事件變得可供查詢時的時間戳記。             |
| subscriptionId       | Azure 訂閱識別碼。  |
| nextLink             | 前往下一組結果時它們會分成多個回覆接續權杖。 當有超過 200 封記錄時，通常被需要。     |

## <a name="next-steps"></a>後續步驟
- [深入瞭解活動記錄 （舊稱稽核記錄檔）](../resource-group-audit.md)
- [資料流事件集線器 Azure 活動記錄檔](./monitoring-stream-activity-logs-event-hubs.md)
