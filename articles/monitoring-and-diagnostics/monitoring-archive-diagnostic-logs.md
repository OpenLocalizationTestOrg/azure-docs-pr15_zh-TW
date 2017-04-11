<properties
    pageTitle="封存 Azure 診斷記錄 |Microsoft Azure"
    description="瞭解如何將封存的儲存空間帳戶長期保留您 Azure 診斷記錄。"
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
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>封存 Azure 診斷記錄
在本文中，我們會示範如何使用 Azure 入口網站，PowerShell Cmdlet、 CLI 或 REST API 封存[Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)儲存帳戶。 如果您想要保留的稽核、 靜態分析或備份的選用的保留原則您診斷記錄時，此選項。

## <a name="prerequisites"></a>必要條件
在開始之前，必須先[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)，您可以封存您診斷記錄。 我們強烈建議您不使用現有的儲存空間帳戶有其他、 非監控資料儲存在它，讓您可以更有效地控制監視資料的存取權。 不過，如果您的活動記錄和診斷指標儲存帳戶時，您也要封存，它可能意義使用該儲存帳戶以及您診斷記錄中要保留所有監控資料中心的位置。 您使用的儲存空間帳戶必須是一般用途儲存帳戶，而不是 blob 儲存體帳戶。

## <a name="diagnostic-settings"></a>診斷設定
若要封存使用下列方法的任何您診斷記錄，您將**診斷設定**為特定的資源。 儲存或傳送的是和輸出診斷設定資源定義的類別的記錄，儲存帳戶及/或事件的中心。 也會儲存在儲存帳戶中每個記錄類別的事件定義的保留原則 （若要保留的日數）。 如果保留原則設定為零，該記錄的類別的事件會儲存無限期 （亦即不限次數說的話）。 保留原則，否則可以是任何數字 1 和 2147483647 之間的天數。 [您可以閱讀更多關於診斷設定以下](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。

## <a name="archive-diagnostic-logs-using-the-portal"></a>使用入口網站的封存診斷記錄

1. 在入口網站中，按一下 [將資源在您想要啟用診斷記錄的封存資源刀。
2. 在 [資源的 [設定] 功能表的 [**監控**] 區段中，選取 [**診斷**]。

    ![監控] 區段中的 [資源] 功能表](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. **匯出到儲存的帳戶**，請核取方塊，然後選取儲存的帳戶。 或者，您可以設定來使用保留這些記錄的天數**保留天數**滑桿。 零天的保留無限儲存記錄。

    ![診斷記錄刀](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. 按一下 [**儲存**]。

診斷記錄被封存該儲存帳戶，一旦產生新的事件資料。

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>透過 PowerShell Cmdlet 的封存診斷記錄

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| 屬性         | 所需 | 描述                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| 預設       | [是]      | 您要將 [診斷設定資源的資源識別碼。                            |
| StorageAccountId | 無       | 資源識別碼要儲存診斷記錄的儲存空間帳戶。                          |
| 類別       | 無       | 逗點分隔啟用記錄類別清單。                                                     |
| 啟用          | [是]      | 指出是否啟用或停用這項資源診斷布林值。                  |
| RetentionEnabled | 無       | 指出這項資源是否啟用保留原則布林值。                            |
| RetentionInDays  | 無       | 事件應該保留介於 1 到 2147483647 之間的天數。 零值會永遠儲存記錄。 |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>封存透過跨平台 CLI 活動記錄

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| 屬性         | 所需 | 描述                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| 預設       | [是]      | 您要將 [診斷設定資源的資源識別碼。                            |
| storageId        | 無       | 資源識別碼要儲存診斷記錄的儲存空間帳戶。                          |
| 類別       | 無       | 逗點分隔啟用記錄類別清單。                                                     |
| 啟用          | [是]      | 指出是否啟用或停用這項資源診斷布林值。                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>透過 REST API 的封存診斷記錄
[請參閱本文件](https://msdn.microsoft.com/library/azure/dn931931.aspx)您可以設定使用 Azure 監視器 REST API 診斷設定的詳細資訊。

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>在儲存帳戶的診斷記錄的結構描述
一旦您已設定保存，存放容器會建立在儲存帳戶，只要在其中一個已啟用記錄類別中所發生的事件。 容器內 blob 遵循相同的格式透過診斷記錄及活動記錄。 這些二進位大型物件的結構是︰

> 獲得深入見解-記錄-{記錄的類別名稱} / = 預設/訂閱 / {訂閱識別碼} /RESOURCEGROUPS/ {資源群組名稱} /PROVIDERS/ {提供者名稱} {資源輸入} / {資源名稱} / y = {四位數的數字年份} / m = {兩位數的數字月份} /d = {兩位數的數字天數} / h = {兩位數 24 小時制 hour}/m=00/PT1H.json

或更簡單，

> 獲得深入見解-記錄-{記錄的類別名稱} / 預設 = / {資源識別碼} / y = {四位數的數字年份} / m = {兩位數的數字月份} /d = {兩位數的數字天數} / h = {兩位數 24 小時制 hour}/m=00/PT1H.json

例如，可能是 blob 名稱︰

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

每個 PT1H.json blob 包含 JSON blob 的 blob URL 中所指定的小時內發生的事件 (例如，h = 12)。 在簡報小時、 事件會加上 PT1H.json 檔案發生。 Minute 值 (m = 00) 一律 00，因為診斷記錄事件分成個別的 blob 每小時。

在 PT1H.json 檔案中，每項事件會儲存在 「 記錄 」 的陣列，追蹤此格式︰

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| 項目名稱  | 描述                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| 時間          | Azure 服務處理對應事件要求產生事件時的時間戳記。 |
| 預設    | 受影響的資源的資源識別碼。                                                                       |
| operationName | 在作業名稱。                                                                                      |
| 類別      | 事件記錄檔類別。                                                                                  |
| 屬性    | 設定的`<Key, Value>`描述事件的詳細資料的成對 （亦即字典）。                            |

> [AZURE.NOTE] 根據資源有所不同的內容及使用方式的屬性。

## <a name="next-steps"></a>後續步驟
- [下載二進位大型物件的分析](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [串流到事件的診斷記錄](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [閱讀更多有關診斷記錄](monitoring-overview-of-diagnostic-logs.md)
