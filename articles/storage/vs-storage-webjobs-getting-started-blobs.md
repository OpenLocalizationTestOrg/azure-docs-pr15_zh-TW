<properties
    pageTitle="快速入門 blob 儲存體和 Visual Studio 連線服務 （WebJob 專案） |Microsoft Azure"
    description="如何開始使用 Visual Studio Azure 儲存體連線後 WebJob 在專案中使用 Blob 儲存體已連線服務。"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>開始使用 Azure Blob 儲存體和 Visual Studio 連線服務 （WebJob 專案）

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

本文提供 C# 程式碼範例，顯示如何建立或更新 Azure blob 時觸發程序。 程式碼範例使用[WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md)版本 1.x。 當您新增的儲存空間帳戶，您可以 WebJob 專案使用 Visual Studio**新增已連線服務**] 對話方塊時，已安裝適當的 Azure 儲存體 NuGet 套件，適當的.NET 參考會新增到專案中，且 App.config 檔案會更新儲存帳戶的連線字串。



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>如何建立或更新 blob 時觸發函數

本節說明如何使用**BlobTrigger**屬性。

 **附註︰**WebJobs SDK 掃描記錄檔，以新增或變更 blob 監看。 此程序是原本就變得很慢。函數可能不輪直到幾分鐘的時間，或再建立 blob 後。  如果您的應用程式需要立即處理 blob，建議的方法就是建立佇列中的郵件，當您建立 blob，並使用[QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger)屬性，而不是處理 blob 的函數的**BlobTrigger**屬性。

### <a name="single-placeholder-for-blob-name-with-extension"></a>使用副檔名 blob 名稱單一版面配置區  

下列程式碼範例複製所顯示的*輸出*容器*輸入*容器中的文字二進位大型物件︰

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

屬性建構函式指定容器名稱] 和 [版面配置區 blob 名稱的字串參數。 在此範例中，*輸入*容器中建立名為*Blob1.txt* blob 函數會建立名為*Blob1.txt* *輸出*容器中的 blob。

下列程式碼範例所示，您可以指定名稱模式的 blob 名稱版面配置區︰

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

將此程式碼複製只 blob 的開頭 「 原始-」 的名稱。 例如，*輸入*容器中的*原始 Blob1.txt*會複製到*複製 Blob1.txt* *輸出*容器中。

如果您要指定名稱中的括號 blob 名稱名稱模式，請按兩下大括號。 例如，如果您想要尋找 blob*圖像*容器具有如下的名稱中︰

        {20140101}-soundfile.mp3

使用您的模式︰

        images/{{20140101}}-{name}

在此範例中，*名稱*] 版面配置區的值是*soundfile.mp3*。

### <a name="separate-blob-name-and-extension-placeholders"></a>個別 blob 的名稱和副檔名版面配置區

下列程式碼範例變更檔案的副檔名，因為它會將會出現在*輸出*容器*輸入*容器的二進位大型物件。 程式碼記錄*輸入*blob 的分機號碼，並設定*.txt**輸出*blob 的副檔名。

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>您可以將繫結到二進位大型物件的類型

在下列類型，您可以使用**BlobTrigger**屬性︰

* **字串**
* **TextReader**
* **串流**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* 還原序列化[ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)的其他類型

如果您想要直接使用 Azure 儲存體帳戶，您也可以新增**CloudStorageAccount**參數的方法簽章。

## <a name="getting-text-blob-content-by-binding-to-string"></a>取得文字字串的繫結的 blob 內容

如果預期文字 blob， **BlobTrigger**可以套用至**字串**參數。 下列程式碼範例會文字 blob 繫結**字串**參數名稱為**logMessage**。 此函數會使用該參數 WebJobs SDK 的儀表板撰寫 blob 的內容。

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>快速使用 ICloudBlobStreamBinder 序列化 blob 內容

下列程式碼範例使用實作**ICloudBlobStreamBinder** ，讓**BlobTrigger**屬性繫結至**WebImage**類型的 blob 的類別。

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

衍生自**ICloudBlobStreamBinder** **WebImageBinder**類別中提供的**WebImage**繫結程式碼。

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>如何處理有害的二進位大型物件

**BlobTrigger**函數失敗時，SDK 通話，再次以避免失敗所造成的暫時性的錯誤。 如果 blob 的內容會導致失敗，函數就會失敗，每次嘗試 blob [處理程序。 根據預設，SDK 呼叫函數最多 5 乘以指定 blob。 如果第五個嘗試失敗，請 SDK 會新增至名稱為*webjobs-blobtrigger-位置*佇列中的郵件。

最大次數是設定的。 相同的[MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue)設定用於有害 blob 處理和有害佇列中的郵件處理。

有害的二進位大型物件的佇列中訊息是 JSON 物件，包含下列屬性︰

* FunctionId （在*{WebJob 名稱}*的格式。函數。*{函數名稱}*，例如︰ WebJob1.Functions.CopyBlob)
* BlobType （「 BlockBlob 」 或 「 PageBlob 」）
* ContainerName
* BlobName
* ETag (blob 版本識別項，例如: 「 0x8D1DC6E70A277EF 」)

在下列程式碼範例， **CopyBlob**函數會有的程式碼，使其每次稱為失敗。 SDK 呼叫的次數上限後，有害 blob 佇列中，建立一則訊息，並**LogPoisonBlob**函數處理的郵件。

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK 自動還原序列化 JSON 訊息。 以下是**PoisonBlobMessage**類別︰

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Blob 投票演算法

WebJobs SDK 掃描**BlobTrigger**屬性應用程式啟動時所指定的所有容器。 大型的儲存空間帳戶此掃描需要一些時間，因此它可能時找不到新的二進位大型物件和**BlobTrigger**函數來執行之前。

若要在應用程式啟動後偵測新增或變更 blob，SDK 定期讀取 blob 儲存記錄檔。 Blob 記錄緩衝和只取得實際寫入每 10 分鐘的時間或是這樣，因此可能會嚴重延遲之後建立或更新之前對應 blob **BlobTrigger**函數執行。

有使用**Blob**屬性您建立的二進位大型物件的例外狀況。 當 WebJobs SDK 建立新的 blob 時，它會新 blob 立即任何相符的**BlobTrigger**函數。 因此如果您有 blob 輸入和輸出的鍊，SDK 可以處理這些有效率。 但如果您想執行處理所建立，或以其他方式更新二進位大型物件的函數您 blob 的低延遲，我們建議使用**QueueTrigger** ，而不是**BlobTrigger**。

### <a name="blob-receipts"></a>Blob 信回條

WebJobs SDK 可確保沒有**BlobTrigger**函數的同一個新的或更新 blob 取得多次呼叫。 它會維持*blob 信回條*，才能判斷是否已處理指定的 blob 版本。

Blob 信回條會儲存在名為 AzureWebJobsStorage 連線字串所指定的 Azure 儲存體帳戶中的 [*主辦 webjobs azure 城市*容器。 Blob 回條具有下列資訊︰

* 函數呼叫的 blob （"*{WebJob 名稱}*。函數。*{函數名稱}*」，例如: 「 WebJob1.Functions.CopyBlob 」)
* 容器名稱
* Blob 類型 （「 BlockBlob 」 或 「 PageBlob 」）
* Blob 名稱
* ETag (blob 版本識別項，例如: 「 0x8D1DC6E70A277EF 」)

如果您想要強制重新處理 blob 的您可以手動刪除該 blob blob 回條，從*主辦城市 webjobs azure*容器。

## <a name="related-topics-covered-by-the-queues-article"></a>相關的主題覆蓋佇列文章

如需如何處理 blob 處理佇列中的郵件中，即可觸發的資訊或 WebJobs SDK 案例不特定 blob 處理，瞭解[如何使用與 WebJobs SDK Azure 佇列中的儲存空間](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。

相關的主題的文件中包含包括下列各項︰

* 非同步函數
* 多個執行個體
* 正常的關閉
* 使用函數的內文中的 WebJobs SDK 屬性
* 在 [程式碼設定 SDK 連接字串。
* 設定值 WebJobs SDK 建構函式參數程式碼
* 設定**MaxDequeueCount**有害 blob 處理。
* 手動觸發函數
* 寫入記錄檔

## <a name="next-steps"></a>後續步驟

本文提供程式碼範例，顯示如何處理使用 Azure blob 的常見的案例。 如需有關如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 文件的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
