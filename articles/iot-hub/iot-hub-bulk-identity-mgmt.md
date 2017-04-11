<properties
 pageTitle="匯入匯出 IoT 中心裝置身分識別 |Microsoft Azure"
 description="概念和.NET 程式碼片段的大量 IoT 中心裝置身分識別管理"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>大量 IoT 中心裝置身分識別的管理

每個 IoT 中心有裝置身分識別登錄，您可以使用的服務，例如包含雲端-裝置訊息中建立裝置每個資源。 裝置身分識別登錄機碼也可讓您控制存取權的裝置具結束點。 本文將說明如何匯入及匯出裝置，大量的身分識別與裝置身分識別登錄。

匯入及匯出作業發生*作業*，可讓您執行大量服務作業 IoT 中樞的內容中。

**RegistryManager**課程包括**ExportDevicesAsync**和使用**工作**架構的**ImportDevicesAsync**方法。 這些方法可讓您匯出、 匯入及同步處理 IoT 中心裝置登錄的全部內容。

## <a name="what-are-jobs"></a>有哪些工作？

裝置身分識別登錄作業使用**工作**系統時作業︰

*  具有潛在很長的執行時間比標準執行階段作業，或
*  傳回大量的資料給使用者。

在這種情況下，而不是單一 API 呼叫等待或作業的結果上封鎖作業非同步會建立該 IoT 集線器**工作**。 然後作業立即傳回**JobProperties**物件。

下列 C# 程式碼片段顯示如何建立匯出工作︰

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

然後您可以使用**RegistryManager**類別來查詢所傳回的**JobProperties**中繼資料的**工作**狀態。

下列 C# 程式碼片段顯示如何以獲得每五秒若要查看工作是否已完成執行︰

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>匯出裝置

使用 [ **ExportDevicesAsync**方法來使用[共用的 Access 簽章](https://msdn.microsoft.com/library/ee395415.aspx) [Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)blob 容器匯出 IoT 中心裝置登錄的全部內容。

這個方法可讓您建立可靠的備份您的裝置的資訊，您可用於控制 blob 容器中。

**ExportDevicesAsync**方法需要兩個參數︰

*  *字串*，包含 blob 容器的 URI。 此 URI 必須包含 SA 權杖授與容器的寫入權限。 工作會封鎖 blob 建立序列的匯出裝置資料的儲存容器中。 SA 權杖必須包含下列權限︰
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *布林值*，指出是否您想要排除您匯出的資料驗證金鑰。 如果**為 false**，驗證鍵會包含在匯出輸出;否則，鍵會匯出為**null**。

下列 C# 程式碼片段示範如何啟動包含裝置驗證金鑰匯出資料的匯出作業，然後再完成投票︰

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

工作會將儲存其輸出提供的 blob 容器中為區塊 blob 與名稱**devices.txt**。 輸出資料所組成 JSON 序列化裝置資料，與每一行的一個裝置。

下列範例顯示的輸出資料︰

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

如果您需要在程式碼中的此資料的存取權，您可以輕鬆地將還原序列化使用**ExportImportDevice**課程此資料。 下列 C# 程式碼片段示範如何讀取裝置先前已匯入至區塊 blob 的資訊︰

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  您也可以使用**GetDevicesAsync**的方法**RegistryManager**類別以擷取您裝置的清單。 不過，這種方法有 1000年的硬首字放大傳回裝置物件數目。 **GetDevicesAsync**方法預期的使用案例適用於開發案例，以協助偵錯時，並不建議使用生產負載。

## <a name="import-devices"></a>匯入裝置

**RegistryManager**類別中**ImportDevicesAsync**方法可讓您執行大量匯入及同步處理作業 IoT 中心裝置登錄中。 **ExportDevicesAsync**的方法，例如**ImportDevicesAsync**方法是使用**工作**架構。

請謹慎使用**ImportDevicesAsync**方法，因為除了佈建裝置身分識別登錄中的新裝置，它也更新與刪除現有的裝置。

> [AZURE.WARNING]  匯入作業無法復原。 備份您現有的資料使用**ExportDevicesAsync**方法到另一個 blob 容器之前您裝置的身分識別登錄中進行大量的變更。

**ImportDevicesAsync**方法需要兩個參數︰

*  *字串*，包含用來作為*輸入*工作的[Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)blob 容器 URI。 此 URI 必須包含 SA 權杖授與容器的 「 讀取 」 權限。 此容器必須包含與包含要匯入至您裝置的身分識別登錄序列的裝置資料名稱**devices.txt** blob。 匯入的資料必須包含相同的 JSON 格式時，它會建立**devices.txt** blob **ExportImportDevice**工作所使用的裝置資訊。 SA 權杖必須包含下列權限︰

    ```
    SharedAccessBlobPermissions.Read
    ```

*  *字串*，包含用來作為*輸出*工作[Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)blob 容器的 URI。 工作會建立區塊 blob 儲存已完成的匯入**作業**的任何錯誤資訊容器中。 SA 權杖必須包含下列權限︰
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  兩個參數可以指向相同 blob 容器。 另一個參數只是讓更多的控制您的資料輸出容器需要額外的權限。

下列 C# 程式碼片段示範如何啟動 [匯入工作︰

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>匯入行為

您可以使用**ImportDevicesAsync**方法，在您裝置的身分識別登錄中執行下列大量作業︰

-   新裝置的大量註冊
-   大量刪除的現有的裝置
-   大量狀態變更 （啟用或停用裝置）
-   大量工作分派的新裝置驗證索引鍵
-   大量自動重新產生的裝置驗證索引鍵

您可以執行在單一**ImportDevicesAsync**呼叫的上一個作業的任何組合。 例如，您可以註冊新裝置，並刪除或同時更新現有的裝置。 使用此選項，以及**ExportDevicesAsync**方法，完全到另一個移轉您的所有裝置從一個 IoT 中心。

使用匯入序列化資料的每個裝置中的選擇性**importMode**屬性來控制匯入程序每個裝置。 **ImportMode**屬性具有下列選項︰

| importMode |  描述 |
| -------- | ----------- |
| **createOrUpdate** | 如果裝置不存在指定的**識別碼**，也會新註冊。 <br/>如果使用的裝置已存在，所提供的輸入資料，花費**ETag**值會覆寫現有的資訊。 |
| **建立** | 如果裝置不存在指定的**識別碼**，也會新註冊。 <br/>如果使用的裝置已存在，錯誤是寫入記錄檔。 |
| **更新** | 如果裝置已經存在指定的**識別碼**，提供輸入資料花費**ETag**值會覆寫現有的資訊。 <br/>如果裝置不存在，錯誤是寫入記錄檔。 |
| **updateIfMatchETag** | 如果裝置已經存在指定的**識別碼**，現有的資訊會覆寫提供輸入資料只有在**ETag**相符項目。 <br/>如果裝置不存在，錯誤是寫入記錄檔。 <br/>如果沒有**ETag**不相符，錯誤是寫入記錄檔。 |
| **createOrUpdateIfMatchETag** | 如果裝置不存在指定的**識別碼**，也會新註冊。 <br/>如果使用的裝置已存在，現有的資訊會覆寫提供輸入資料只有在**ETag**相符項目。 <br/>如果沒有**ETag**不相符，錯誤是寫入記錄檔。 |
| **刪除** | 如果裝置已經存在指定的**識別碼**，則會刪除花費**ETag**值。 <br/>如果裝置不存在，錯誤是寫入記錄檔。 |
| **deleteIfMatchETag** | 如果裝置已經存在指定的**識別碼**，就會刪除只有在**ETag**相符項目。 如果裝置不存在，錯誤是寫入記錄檔。 <br/>如果沒有 ETag 不相符，錯誤是寫入記錄檔。 |

> [AZURE.NOTE] 如果序列化資料沒有明確定義的裝置**importMode**標幟，預設為**createOrUpdate**匯入作業期間。

## <a name="import-devices-example--bulk-device-provisioning"></a>匯入裝置的範例，大量佈建裝置 

下列 C# 程式碼範例說明如何產生多個裝置身分識別的︰

- 包含驗證金鑰。
- 封鎖 blob 的寫入該裝置的資訊。
- 裝置匯入裝置的身分識別登錄機碼。

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>匯入裝置範例 – 大量刪除

下列程式碼範例會示範如何刪除您所新增的前一個的程式碼範例的裝置︰

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>取得容器 SA URI


下列程式碼範例會示範如何產生[SA URI](../storage/storage-dotnet-shared-access-signature-part-2.md)與讀取與寫入，blob 容器刪除權限︰

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>後續步驟

本文中，您學到如何在 IoT 中心執行大量作業裝置身分識別登錄機碼。 請遵循這些連結，深入瞭解管理 Azure IoT 中心︰

- [使用指標][lnk-metrics]
- [監控的作業][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md