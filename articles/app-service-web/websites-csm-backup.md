<properties
    pageTitle="使用其餘備份及還原應用程式服務應用程式"
    description="瞭解如何使用 RESTful API 呼叫備份及還原 Azure 應用程式服務中的應用程式"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>使用其餘備份及還原應用程式服務應用程式

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [REST API](websites-csm-backup.md)

[應用程式服務應用程式](https://azure.microsoft.com/services/app-service/web/)可以備份為二進位大型物件 Azure 儲存體中。 備份也可以包含應用程式的資料庫。 如果應用程式時不小心刪除，或如果應用程式需要還原成先前的版本，可以還原從任何先前的備份。 視情況下，隨時都可以完成備份，或在適當的時間間隔排定備份。

本文說明如何使用備份與還原 RESTful API 邀請與應用程式。 如果您想要建立及管理應用程式備份，以圖形方式透過 Azure 入口網站，請參閱[備份 Azure 應用程式服務中的 web 應用程式](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>快速入門
若要傳送其餘的要求，您需要知道您的應用程式**名稱**]、 [**資源] 群組**中，與**訂閱識別碼**。 按一下您的應用程式中的[Azure 入口網站](https://portal.azure.com)**應用程式服務**刀可以找到這項資訊。 本文中的範例，我們會設定網站**backuprestoreapiexamples.azurewebsites.net**。 它會儲存在預設-Web-WestUS 資源群組，並在訂閱的識別碼 00001111-2222年-3333-4444-555566667777 上執行。

![範例網站資訊][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>備份及還原 REST API
我們現在會說明如何使用備份與還原應用程式的 REST API 的幾個範例。 每個範例包括 URL] 與 [HTTP 邀請內文。 範例 URL 會包含在大括弧，例如 {訂閱識別碼} 換行的版面配置區。 與您的應用程式的相對應的資訊取代預留位置。 參照，以下是範例 Url 中會顯示每個版面配置區的說明。

* 訂閱 id-的 Azure 訂閱包含應用程式識別碼
* 資源群組的名稱 – 包含應用程式的資源群組的名稱
* 名稱 – 應用程式的名稱
* 備份 id-的應用程式備份

API，包括可在 HTTP 邀請中，包含數種選擇性參數的完整的文件，請參閱[Azure 資源檔案總管](https://resources.azure.com/)。

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>備份視應用程式
若要立即備份應用程式，傳送的**文章**要求**https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**。

以下是什麼 URL 看起來像使用我們的範例網站。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

提供您的要求，指定要使用儲存備份的儲存空間帳戶的內文中 JSON 物件。 JSON 物件都必須有一個名為**storageAccountUrl**，它會保留[SA URL](../storage/storage-dotnet-shared-access-signature-part-1.md)授與寫入存取權保留備份 blob Azure 儲存體容器屬性。 如果您想要備份您的資料庫，您也必須提供包含名稱、 類型及要備份的資料庫的連線字串的清單。

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

備份的應用程式會在收到要求後，立即開始。 備份的程序可能需要很長的時間才能完成。 HTTP 回應包含 ID，您可以用在另一個邀請中，請參閱備份的狀態。 以下是本文的我們備份要求 HTTP 回應範例。

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] 可以 HTTP 回應的 [記錄] 屬性中找到的錯誤訊息。

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>排程自動備份
除了備份應用程式，視需要，您也可以排程發生自動備份。

### <a name="set-up-a-new-automatic-backup-schedule"></a>設定新的自動備份排程
若要設定的備份排程，傳送的**放置**要求**https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**。

以下是我們的範例網站的 URL 外觀。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

邀請本文必須 JSON 指定的物件，備份設定。 以下是與所有必要的參數範例。

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

此範例中設定自動備份每七天的應用程式。 參數**frequencyInterval**和**frequencyUnit**分成一組決定備份發生的頻率。 **FrequencyUnit**有效值**小時**] 及 [**天**。 比方說，如果要每 12 小時備份應用程式，您可以將 frequencyInterval 12 和 frequencyUnit 小時。

從儲存的帳戶，會自動移除舊的備份。 您可以控制多久備份可以藉由設定**retentionPeriodInDays**參數。 如果您要永遠必須至少有一個儲存，不論如何舊，設定**keepAtLeastOneBackup**為 true 的備份。

### <a name="get-the-automatic-backup-schedule"></a>取得自動備份的排程
若要取得應用程式的備份設定，請傳送**文章**要求 url **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**。

我們的範例網站的 URL 是**https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**。

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>取得的備份
如何大型是應用程式，而定備份可能需要一段時間才能完成。 備份可能也會失敗逾時，或部分失敗。 若要查看所有應用程式的備份的狀態，請傳送**GET**要求 url **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**。

若要查看特定備份的狀態，請至 URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**傳送 GET 要求。

以下是我們的範例網站的 URL 外觀。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

回應內文包含類似此範例 JSON 物件。

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

備份的狀態是列舉的類型。 以下是每個可能的狀態。

* 0-InProgress︰ 備份已啟動，但尚未完成。
* 1-失敗︰ 備份作業已失敗。
* 2-成功︰ 備份已順利完成。
* 3-逾時︰ 備份未完成的時間，並已取消。
* 4-建立︰ 備份要求佇列，但尚未啟動。
* 5 – 略過︰ 備份並未繼續因為觸發太多備份的排程。
* 6-PartiallySucceeded︰ 備份順利完成，但尚未備份某些檔案，因為它們無法讀取。 這通常是因為因為獨佔鎖定放在檔案]。
* 7-DeleteInProgress︰ 備份已要求刪除，但尚未刪除。
* 8 – DeleteFailed︰ 無法刪除備份。 已過期 SA URL 是用來建立備份，可能會發生這種情況。
* 9 – 刪除︰ 備份已順利刪除。

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>從備份檔案還原應用程式
如果您的應用程式已遭刪除，或如果您想要回復為舊版應用程式，您可以從備份檔案還原應用程式。 要叫用還原，傳送**文章**要求 url **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**。

以下是我們的範例網站的 URL 外觀。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore**

在邀請內文中傳送 JSON 物件，包含還原作業的屬性。 以下是範例，其中包含所有必要的屬性︰

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>還原至新的應用程式
有時您可能要還原的備份，而不是覆寫現有的應用程式時，建立新的應用程式。 若要這麼做，變更要求 URL 指向您想要建立新的應用程式和變更 JSON 中的 [**覆寫**] 屬性設**為 false**。

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>刪除應用程式備份
如果您想要刪除備份，要求傳送到**刪除**URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**。

以下是我們的範例網站的 URL 外觀。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>管理備份的 SA URL
Azure 應用程式服務將會嘗試使用 SA URL 建立備份時所提供的 Azure 儲存體中刪除您的備份。 如果此 SA URL 已不再有效，您無法刪除備份透過 REST API。 不過，您可以更新的 url 傳送**文章**要求的相關備份 SA URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**。

以下是我們的範例網站的 URL 外觀。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list**

邀請本文中，傳送含有新的 SA URL 的 JSON 物件。 以下是範例。

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] 基於安全性理由，特定備份傳送 GET 要求時，不會傳回 SA URL 備份相關聯。 如果您想要檢視備份與相關聯的 SA URL，請至上述相同的 URL 傳送文章要求。 在邀請內文中包含空白的 JSON 物件。 從伺服器回應包含所有備份的資訊，包括其 SA URL。

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
