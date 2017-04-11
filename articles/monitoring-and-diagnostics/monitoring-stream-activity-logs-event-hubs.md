<properties
    pageTitle="串流到事件 Azure 活動記錄 |Microsoft Azure"
    description="瞭解如何將串流到事件 Azure 活動記錄。"
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
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>資料流事件集線器 Azure 活動記錄檔
[**Azure 活動的記錄**](./monitoring-overview-activity-logs.md)可以使用內建的 [匯出] 選項，在入口網站，或讓服務匯流排規則識別碼，透過 Azure PowerShell Cmdlet 或 Azure CLI 記錄設定檔中的任何應用程式的近期即時資料流傳送。

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>您可以執行的活動記錄和事件集線器
以下是一些您可能會用於活動記錄中的資料流功能的方法︰

- **協力廠商記錄及遙測系統串流**– 一段時間，事件集線器串流會變成到第三方 SIEMs 管道您的活動登入並記錄分析解決方案機制。
- **建立自訂的遙測與記錄平台**– 如果您已將自訂的遙測平台或只思考建置，彈性發行-訂閱性質事件集線器可讓您彈性內嵌活動記錄。 [請參閱使用中的全域的縮放比例遙測平台的事件集線器 Dan Rosanova 指南。](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>啟用串流活動記錄檔
您可以啟用串流的活動記錄，以程式設計方式或透過入口網站。 兩種方式，您挑選的服務匯流排命名空間和共用的存取原則命名空間，並在第一個新的活動記錄事件發生時，將會建立該命名空間中的事件中心。 如果您沒有服務匯流排命名空間，您必須建立帳戶。 如果您先前已傳送活動記錄事件，此服務匯流排命名空間，可重複使用先前已建立的 [事件] 中心。 共用的存取原則可定義串流機制具有權限。 現在，串流到事件集線器需要**管理**」、 「**讀取**」 和 「**傳送**的權限。 您可以建立或修改您的服務匯流排命名空間的 「 設定 」 索引標籤下方的傳統入口網站中的共用服務匯流排命名空間存取原則。 若要更新的活動記錄記錄設定檔，包括串流，進行變更的使用者必須 ListKey 權限的服務匯流排授權規則。

### <a name="via-azure-portal"></a>透過 Azure 入口網站 
1. 瀏覽至**活動記錄**刀使用左側的入口網站的功能表。

    ![瀏覽至活動記錄中入口網站](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 按一下 [刀頂端的 [**匯出**] 按鈕。

    ![在入口網站中的 [匯出] 按鈕](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在出現刀，您可以選擇用您想要資料流事件中您想要針對串流這些事件建立事件中心服務匯流排命名空間的區域。

    ![匯出活動記錄刀](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 按一下 [儲存這些設定的 [**儲存**]。 設定會立即套用至您的訂閱。


### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet
如果記錄檔已存在，您首先要移除的設定檔。

1. 使用`Get-AzureRmLogProfile`識別記錄的設定檔
2. 如果是這樣，使用`Remove-AzureRmLogProfile`將其移除。
3. 使用`Set-AzureRmLogProfile`建立設定檔︰

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

服務匯流排規則識別碼是使用此格式字串: {服務匯流排資源識別碼} /authorizationrules/ {機碼名稱}，例如 

### <a name="via-azure-cli"></a>透過 Azure CLI
如果記錄檔已存在，您首先要移除的設定檔。

1. 使用`azure insights logprofile list`識別記錄的設定檔
2. 如果是這樣，使用`azure insights logprofile delete`將其移除。
3. 使用`azure insights logprofile add`建立設定檔︰

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

服務匯流排規則識別碼是使用此格式字串︰ `{service bus resource ID}/authorizationrules/{key name}`。
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>如何使用記錄事件集線器資料？
[以下是可用的活動記錄的結構描述](./monitoring-overview-activity-logs.md)。 每項事件位於陣列 JSON blob 稱為 「 記錄 」。

## <a name="next-steps"></a>後續步驟
- [封存儲存帳戶活動記錄](./monitoring-archive-activity-log.md)
- [閱讀 Azure 活動記錄的概觀](./monitoring-overview-activity-logs.md)
- [設定 [根據活動記錄事件的提醒](./insights-auditlog-to-webhook-email.md)
