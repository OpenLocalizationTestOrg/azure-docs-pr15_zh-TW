<properties
    pageTitle="封存 Azure 活動記錄 |Microsoft Azure"
    description="瞭解如何將封存長期儲存帳戶的保留您 Azure 活動記錄。"
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>封存 Azure 活動記錄
在本文中，我們會示範如何使用 Azure 入口網站，PowerShell Cmdlet 或跨平台 CLI 封存您儲存帳戶的[**Azure 活動記錄**](monitoring-overview-activity-logs.md)。 如果您想要保留超過 90 天 （具有完全控制權的保留原則的詳細資訊），稽核、 靜態分析，或備份您的活動的記錄檔，這個選項。 如果您只需要保留 90 天的事件或較少您不需要設定保存到儲存的帳戶，因為活動記錄保留在 Azure 平台的 90 天沒有啟用保存。

## <a name="prerequisites"></a>必要條件
在開始之前，必須先[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)，您可以對其封存您的活動記錄。 我們強烈建議您不使用現有的儲存空間帳戶有其他、 非監控資料儲存在它，讓您可以更有效地控制監視資料的存取權。 不過，如果您也要封存診斷記錄及儲存帳戶的度量，它可能意義使用該儲存帳戶為您的活動記錄檔以讓所有的監控資料保持在一個中央位置。 您使用的儲存空間帳戶必須是一般用途儲存帳戶，而不是 blob 儲存體帳戶。

## <a name="log-profile"></a>記錄檔的設定檔
若要備份使用下列方法之一活動記錄，您可以設定訂閱的**記錄檔的設定檔**。 記錄設定檔定義之類型的事件會儲存或傳送的和輸出 — 儲存帳戶及/或事件的中心。 也會定義事件儲存在儲存帳戶的保留原則 （若要保留的日數）。 如果保留原則設定為零，就會永遠儲存事件。 否則，這可以設定為介於 1 到 2147483647 的任何值。 [您可以閱讀更多關於記錄檔設定檔以下](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)。

## <a name="archive-the-activity-log-using-the-portal"></a>封存活動記錄使用入口網站
1. 在入口網站中，按一下左側導覽中的**活動記錄**連結。 如果您沒有看到連結的活動記錄，請先按一下 [**更多服務**] 連結。

    ![瀏覽至活動記錄刀](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. 在刀頂端，按一下 [**匯出**]。

    ![按一下 [匯出] 按鈕](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. 在出現刀的**匯出到儲存的帳戶**] 核取方塊並選取的儲存空間的帳戶。

    ![設定儲存帳戶](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. 使用滑桿或文字方塊中，定義數天的活動記錄事件應該放在您儲存的帳戶。 如果您想要有資料保留在儲存帳戶無限期，將這個數字設定為零。
5. 按一下 [**儲存**]。

## <a name="archive-the-activity-log-via-powershell"></a>封存透過 PowerShell 活動記錄
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| 屬性         | 所需 | 描述                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | 無       | 儲存帳戶活動記錄要儲存的資源識別碼。                                                                                                                                                                                                                        |
| 位置        | [是]      | 逗點分隔的區域，您要收集活動記錄事件的清單。 您可以檢視所有區域[瀏覽此頁面](https://azure.microsoft.com/en-us/regions)或使用[Azure 管理 REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx)的清單。 |
| RetentionInDays  | [是]      | 保留哪些事件，介於 1 到 2147483647 之間的日數。 零值會永遠儲存記錄 （永久）。                                                                                                                                                                                             |
| 類別       | [是]      | 逗點分隔應該收集的事件類別清單。 可能的值會寫入、 刪除和動作。                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>封存透過 CLI 活動記錄
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| 屬性        | 所需 | 描述                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱            | [是]      | 記錄設定檔的名稱。                                                                                                                                                                                                                                                                         |
| storageId       | 無       | 儲存帳戶活動記錄要儲存的資源識別碼。                                                                                                                                                                                                                        |
| 位置       | [是]      | 逗點分隔的區域，您要收集活動記錄事件的清單。 您可以檢視所有區域[瀏覽此頁面](https://azure.microsoft.com/en-us/regions)或使用[Azure 管理 REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx)的清單。 |
| retentionInDays | [是]      | 保留哪些事件，介於 1 到 2147483647 之間的日數。 零值會永遠儲存記錄 （永久）。                                                                                                                                                                                             |
| 類別      | [是]      | 逗點分隔應該收集的事件類別清單。 可能的值會寫入、 刪除和動作。                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>活動記錄的儲存空間結構描述
一旦您已設定保存，存放容器會建立在儲存帳戶活動記錄事件發生時。 容器內 blob 追蹤活動記錄以及診斷記錄的相同的格式。 這些二進位大型物件的結構是︰

> 獲得深入見解操作-記錄/名稱 = = 預設/預設/訂閱 / {訂閱識別碼} / y = {四位數的數字年份} / m = {兩位數的數字月份} /d = {兩位數的數字天數} / h = {兩位數 24 小時制 hour}/m=00/PT1H.json

例如，可能是 blob 名稱︰

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json

每個 PT1H.json blob 包含 JSON blob 的 blob URL 中所指定的小時內發生的事件 (例如 h = 12)。 在簡報小時、 事件會加上 PT1H.json 檔案發生。 Minute 值 (m = 00) 一律 00，因為活動記錄事件分成個別的 blob 每小時。

在 PT1H.json 檔案中，每項事件會儲存在 「 記錄 」 的陣列，追蹤此格式︰

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| 項目名稱    | 描述                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| 時間            | Azure 服務處理對應事件要求產生事件時的時間戳記。    |
| 預設      | 受影響的資源的資源識別碼。                                                                          |
| operationName   | 在作業名稱。                                                                                         |
| 類別        | 類別的動作，例如。 寫入、 讀取、 巨集指令。                                                               |
| resultType      | 結果類型的例如。 成功、 失敗，開始                                                            |
| resultSignature | 資源類型而定。                                                                                  |
| durationMs      | 以毫秒為單位作業期間                                                                      |
| callerIpAddress | 執行的作業、 UPN 宣告或 SPN 宣告可用性所根據的使用者的 IP 位址。         |
| 相互關聯識別碼   | 通常是字串格式 GUID。 共用的相互關聯識別碼的事件屬於相同的烏柏動作。         |
| 身分識別        | JSON blob 說明的授權和宣告。                                                             |
| 授權   | Blob RBAC 內容中的事件。 通常包含 「 動作 」、 「 角色 」 和 「 範圍] 屬性。            |
| 層級           | 事件的層級。 下列的值的其中一個: 「 重要 」，「 錯誤 」，「 警告 」 「 資訊 」 和 「 詳細資訊 」 |
| 位置        | 區域中的位置發生 （或全域管理員）。                                                             |
| 屬性      | 設定的`<Key, Value>`描述事件的詳細資料的成對 （亦即字典）。                             |

> [AZURE.NOTE] 根據資源有所不同的內容及使用方式的屬性。

## <a name="next-steps"></a>後續步驟
- [下載二進位大型物件的分析](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [資料流事件集線器活動記錄檔](monitoring-stream-activity-logs-event-hubs.md)
- [閱讀更多關於活動記錄](monitoring-overview-activity-logs.md)
