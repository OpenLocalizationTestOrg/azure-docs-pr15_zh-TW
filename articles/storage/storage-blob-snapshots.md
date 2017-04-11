<properties
    pageTitle="建立 blob 的唯讀快照 |Microsoft Azure"
    description="瞭解如何建立要在指定的時間時間備份 blob 資料的 blob 的快照。 瞭解如何快照向收費，以及如何使用這些最小化容量費用。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="create-a-blob-snapshot"></a>建立 blob 快照集

## <a name="overview"></a>概觀

快照是唯讀版本的時間點會採取 blob。 快照是很適合用於備份二進位大型物件。 建立快照之後，您可以閱讀、 複製或刪除，但無法加以修改。

Blob 的快照是相同，其基底 blob，但 blob URI 具有附加至 blob URI 表示製作快照的時間的**DateTime**值。 例如，如果頁面 blob URI 是`http://storagesample.core.blob.windows.net/mydrives/myvhd`，URI 是類似的快照集`http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`。 

> [AZURE.NOTE] 所有快照集共用基底 blob 的 URI。 僅限與之間的差異基底 blob 快照是附加的**DateTime**值。

Blob 可以有任何數字的快照集。 快照持續直到明確刪除的郵件。 快照無法 outlive 其基底 blob。 您可以列舉基底 blob 來追蹤您目前的快照集相關聯的快照集。

當您建立 blob 的快照時，就會將 blob 的系統屬性複製到相同的值的快照。 基底 blob 的中繼資料會一併複製快照，除非您在建立時，指定不同的中繼資料的快照。

基底 blob 相關聯的任何租用不會影響快照集。 您無法取得租用的快照。

## <a name="create-a-snapshot"></a>建立快照集

下列範例會示範如何建立.net 的快照。 建立時，此範例會指定不同的中繼資料的快照。

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>複製快照集

包含二進位大型物件和快照的複製作業，請遵循下列規則︰

- 您可以複製其基底 blob 的快照。 藉由升級的位置基底 blob 的快照，您可以還原舊版的 blob。 快照仍會保留，但基底 blob 會覆寫快照的可寫入複本。

- 您可以將快照複製到目的地 blob 使用不同的名稱。 產生的目的地 blob 是可寫入的 blob 並不快照。

- 當複製來源 blob 時，並不會將來源 blob 的任何快照複製到目的地。 當目的地 blob 會覆寫複本時，任何與原始的目的地 blob 相關聯的快照會保持不變。

- 當您建立的區塊 blob 的快照時，blob 的認可的封鎖清單也會一併複製快照。 不會複製任何未認可的區塊。

## <a name="specify-an-access-condition"></a>指定存取條件

您可以指定存取條件，讓快照集在條件符合時，才會建立。 若要指定存取條件，請使用**AccessCondition**屬性。 如果不符合指定的條件，不會建立快照，及 Blob 服務會傳回狀態碼 HTTPStatusCode.PreconditionFailed。

## <a name="delete-snapshots"></a>刪除快照集

您無法刪除 blob 的快照，除非快照也會一併刪除。 您可以個別刪除快照，或指定當刪除來源 blob 時，會刪除所有快照集。 如果您嘗試刪除仍擁有快照 blob，會發生錯誤。

下列範例會示範如何刪除 blob 和.net，其快照位置`blockBlob`是類型**CloudBlockBlob**的變數︰

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Azure 進階版儲存空間的快照集

使用快照進階版儲存追蹤這些規則︰

- 每個進階版儲存帳戶頁面 blob 的快照的數目上限為 100。 如果超出的限制，則快照 Blob 作業會傳回錯誤碼 409 (**SnapshotCountExceeded**)。

- 您可以採取頁面 blob 的快照進階版儲存帳戶一次每 10 分鐘的時間。 如果超出的工資率，快照 Blob 作業會傳回錯誤碼 409 (**SnaphotOperationRateExceeded**)。

- 您無法呼叫取得 Blob 閱讀優質儲存帳戶頁面 blob 的快照。 進階版儲存帳戶快照上呼叫取得 Blob 傳回錯誤碼 400 (**InvalidOperation**)。 不過，您可以針對進階版儲存帳戶快照呼叫取得 Blob 屬性和取得 Blob 中繼資料。

- 若要閱讀快照，您可以使用 [複製 Blob 作業，將快照複製到另一個頁面 blob，在帳戶。 複製作業的目的地 blob 必須沒有任何現有的快照。 如果目的地 blob 具有快照，複製 Blob 作業會傳回錯誤的程式碼 409 (**SnapshotsPresent**)。

## <a name="return-the-absolute-uri-to-a-snapshot"></a>絕對 URI 返回快照

這個 C# 程式碼範例建立快照，並將 out 主要位置的絕對 URI。

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>瞭解如何快照累算費用

建立快照，也就是 blob 的唯讀複本，可能會導致其他資料儲存區費用至您的帳戶。 設計您的應用程式，時必須瞭解這些費用可能累算的方式，好讓您可以最小化不必要的成本。

### <a name="important-billing-considerations"></a>帳單寄送的重要考量

下列清單包含建立快照時要考慮的主要重點。

- Blob 中或在快照，您儲存帳戶必須支付唯一區塊的頁面、 費用。 您的帳戶並不會造成額外的費用，直到您更新為根據的 blob 相關聯的 blob 的快照集。 更新基底 blob 之後，它會出現從其快照。 發生這種情況下，您會唯一區塊或中每個 blob 或快照的頁面。

- 當您取代內區塊 blob 區塊時，區塊後續負責以唯一區塊。 即使封鎖有相同的區塊識別碼和相同的資料有快照中時，也是如此。 封鎖方案已獲確認後，其出現在任何快照，及其對應的您需要付費其資料。 同樣適用於頁面便會以相同的資料更新頁面 blob 中。

- 取代區塊 blob 呼叫**UploadFile**、 **UploadText**、 **UploadStream**或**UploadByteArray**方法會取代 blob 中的所有區塊。 如果您有該 blob 相關聯的快照，現在可選基底 blob 和快照中所有的區塊，，您將會針對您所有的兩個 blob 的區塊。 這是 true，即使基底 blob 及快照集內的資料保持相同。

- Azure Blob 服務沒有以便判斷是否有兩個區塊包含相同的資料。 上傳且已確認每個區塊會被視為為唯一的即使有相同的資料與相同的區塊識別碼。 因為費用累算的唯一的區塊，很重要更新有其他的唯一組塊及其他費用快照結果 blob 的。

> [AZURE.NOTE] 最佳作法指示您管理快照集小心避免額外的費用。 我們建議您管理快照，以下列方式︰

> - 刪除和重新建立聯 blob 每當您更新 blob，即使您要更新的相同的資料，除非您應用程式會要求您維護快照的快照。 刪除並重新建立 blob 的快照，您可以確保 blob 和快照集執行不有所不同。

> - 如果您維護 blob 的快照，避免呼叫**UploadFile**、 **UploadText**、 **UploadStream**或更新 blob **UploadByteArray** 。 這些方法會取代所有 blob 的區塊，好讓您基底 blob 和快照集可選 signficantly。 不過，使用**PutBlock**和**PutBlockList**方法更新區塊少可能數目。


### <a name="snapshot-billing-scenarios"></a>計費案例的快照


下列案例將示範費用區塊 blob 和其快照累算的方式。

在案例 1 中，基底 blob 尚未更新之後快照，讓費用所產生僅適用於唯一區塊 1、 2 和 3。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

在案例 2 中，已更新基底 blob，但快照集有不。 更新區塊 3，，即使其中包含相同的資料和相同的識別碼，並不一樣封鎖 3 的快照。 如此一來，該帳戶會負責四個組塊。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

在案例 3 中，已更新基底 blob，但快照集有不。 封鎖 3 已經取代區塊 4 中基底 blob，但快照仍會反映區塊 3。 如此一來，該帳戶會負責四個組塊。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

在案例 4，基底 blob 已完全更新，且包含無其原始區塊。 如此一來，所有的八個唯一區塊被費用帳戶。 這種情況可能是如果您使用的更新方法，例如**UploadFile**、 **UploadText**、 **UploadFromStream**或**UploadByteArray**，因為這些方法取代所有 blob 的內容。

![Azure 儲存體資源](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>後續步驟

如需使用 Blob 儲存體的其他範例，請參閱[Azure 程式碼範例](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)。 您可以下載的範例應用程式並執行，或瀏覽 GitHub 的程式碼。 
