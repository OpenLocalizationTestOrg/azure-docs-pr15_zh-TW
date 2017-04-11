<properties 
    pageTitle="使用媒體 Encoder 標準使用.NET 編碼資產 |Microsoft Azure" 
    description="本主題說明如何使用.NET 編碼使用 Media Encoder Strandard 資產。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>使用媒體 Encoder 標準使用.NET 編碼資產

編碼工作是最常見的處理作業中媒體服務。 您建立的媒體檔案轉換到另一種編碼方式編碼工作。 當您編碼時，您可以使用內建 Media Encoder 媒體服務。 您也可以使用所提供的媒體服務夥伴; encoder第三方編碼器都是透過 Azure Marketplace。 

本主題說明如何使用.NET 編碼資產與媒體 Encoder 標準 (MES)。 媒體 Encoder 標準設定使用其中一個 encoder 預設格式所述[以下](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)。

建議您隨時都將 MP4 設定，然後再將設定轉換為所要的格式，使用[動態包裝](media-services-dynamic-packaging-overview.md)調整位元率編碼 mezzanine 檔案。 若要利用動態包裝，必須先至少有一個視串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[如何比例媒體服務](media-services-portal-manage-streaming-endpoints.md)。

如果您的輸出資產加密的儲存空間，您必須設定資產傳遞原則。 如需詳細資訊，請參閱[設定資產傳遞原則](media-services-dotnet-configure-asset-delivery-policy.md)。

>[AZURE.NOTE]MES 產生輸出檔案的名稱，輸入的檔案名稱的第一次 32 個字元。 名稱為基礎的預設檔案中所指定。 例如，"檔案名稱]: 「 {Basename} _ {Index} {副檔名} 」。 輸入的檔案名稱的第一次 32 字元會取代 {Basename}。

###<a name="mes-formats"></a>MES 格式

[格式及轉碼器](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>MES 預設格式

媒體 Encoder 標準設定使用其中一個 encoder 預設格式所述[以下](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)。

###<a name="input-and-output-metadata"></a>輸入與輸出的中繼資料

當您編碼輸入的資產 （或資產） 使用 MES 時，您會在成功取得輸出資產的完成編碼工作。 輸出資產包含視訊、 音訊、 縮圖、 資訊清單等根據您使用的編碼預設格式。

輸出資產也包含輸入資產的中繼資料使用的檔案。 中繼資料 XML 檔案的名稱有下列格式︰ < asset_id > _metadata.xml (例如，41114ad3-eb5e-4 c 57 8 d 頁，共 92-5354e2b7d4a4_metadata.xml)，其中 < asset_id > 是 AssetId 輸入資產。 描述此輸入的中繼資料 XML 結構描述[以下](http://msdn.microsoft.com/library/azure/dn783120.aspx)。

輸出資產也包含輸出資產的中繼資料使用的檔案。 中繼資料 XML 檔案的名稱有下列格式︰ < source_file_name > _manifest.xml (例如，BigBuckBunny_manifest.xml)。 此 XML 所述的輸出中繼資料的結構描述[以下](http://msdn.microsoft.com/library/azure/dn783217.aspx)。

如果您想要檢查的任何兩個中繼資料檔案，您可以建立 SA 定位器，並將檔案下載到您的本機電腦。 您可以尋找如何建立 SA 定位器及下載檔案的媒體服務.NET SDK 擴充功能範例。

##<a name="download-sample"></a>下載範例

取得，並執行從[這裡](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)的範例。

##<a name="example"></a>範例

下列範例會使用媒體服務.NET SDK 來執行下列工作︰

- 建立編碼的工作。
- 取得 Media Encoder 標準 encoder 的參考。
- 指定要使用 「 H264 多個位元率 720p 「 預設。 您可以看到所有預設格式[以下](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)。 您也可以檢查的必須符合下列預設格式的結構描述[以下](https://msdn.microsoft.com/library/mt269962.aspx)主題。
- 新增單一編碼工作的工作。 
- 指定要編碼輸入的資產。
- 建立輸出資產包含編碼的資產。
- 新增事件處理常式來檢查工作進度。
- 送出工作。
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.None);
        
            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
        
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
            return job.OutputMediaAssets[0];
        }
        
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
        
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
        
                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        
        
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
            return processor;
        }


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>另請參閱 

[如何產生.NET 使用 Media Encoder 標準的縮圖](media-services-dotnet-generate-thumbnail-with-mes.md)
[媒體服務編碼概觀](media-services-encode-asset.md)
