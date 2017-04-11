<properties
    pageTitle="將媒體檔案與 Azure 媒體索引編製索引"
    description="Azure 媒體重新可讓您以供搜尋的媒體檔案的內容，並產生隱藏式輔助字幕和關鍵字的全文檢索字幕。 本主題說明如何使用媒體索引。"
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>將媒體檔案與 Azure 媒體索引編製索引


Azure 媒體重新可讓您以供搜尋的媒體檔案的內容，並產生隱藏式輔助字幕和關鍵字的全文檢索字幕。 您可以處理一個媒體檔案或多個媒體檔案以批次。  

>[AZURE.IMPORTANT] 當編製索引的內容，請確定使用很清楚語音 （不含背景音樂、 雜訊、 效果或麥克風 hiss） 的媒體檔案。 部分範例適當的內容是︰ 錄製會議、 課堂上或簡報。 下列內容可能不適用於編製索引作業︰ 影片，電視節目，任何與混合的音訊聲音效果不佳錄製與背景噪音 (hiss) 的內容。


編製索引的作業可能會產生下列輸出︰

- 關閉 [標題在下列格式︰**沙米文**、 **TTML**及**WebVTT**。

    字幕檔案包含標記稱為 Recognizability，如何可辨識來源影片語音是根據編製索引作業的分數。  您可以使用 Recognizability 的值合用性的畫面輸出檔案。 低分數表示因為音訊品質不佳編製索引的結果。
- 關鍵字檔案 (XML)。
- 搭配 SQL server 索引 blob 檔案 (AIB) 的音訊。

    如需詳細資訊，請參閱[Azure 媒體重新與 SQL Server 使用 AIB 檔案](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)。


本主題說明如何建立**索引資產**及**多個檔案編製索引**編製索引作業。

Azure 媒體重新最新的更新，請參閱[媒體服務部落格](#preset)。

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>使用索引工作的設定和資訊清單的檔案

使用工作設定，您可以為編製索引任務指定更多詳細資料。 例如，您可以指定您媒體檔案的中繼資料。 此中繼資料用來展開其詞彙語言引擎，便可改善語音辨識的正確性。  您也可指定您想要的輸出檔案。

您也可以使用資訊清單的檔案，一次處理多個媒體檔案。

如需詳細資訊，請參閱[Azure 媒體索引器預設的任務](https://msdn.microsoft.com/library/dn783454.aspx)。

## <a name="index-an-asset"></a>資產編製索引

下列方法上傳的媒體檔案以資產，並建立索引資產的工作。

請注意，是否有不指定任何的設定檔，則媒體檔案會能編製索引的所有預設設定。

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
<!-- __ -->
### <a id="output_files"></a>輸出檔案

根據預設，編製索引的工作會產生下列輸出檔案。 檔案會儲存在第一個輸出資產。

當有一個以上的輸入的媒體檔案時，請重新會產生工作輸出，名為 「 JobResult.txt 」 的資訊清單檔案。 每個輸入媒體檔案，產生 AIB、 沙米文、 TTML、 WebVTT 和關鍵字檔案，依序編號以及名為 「 使用 」 別名]。

檔案名稱 | 描述
----------|------------
__InputFileName.aib__ | 音訊編製索引的 blob 檔案。 <br /><br /> 編製索引作業 Blob (AIB) 的音訊檔案是在 Microsoft SQL server 中使用完整的文字搜尋可搜尋的二進位檔案。  AIB 檔案是簡單的標號檔案中，於功能更強大的因為它包含每個單字，允許更豐富的搜尋體驗的替代方案。 <br/> <br/>需要安裝的電腦執行 Microsoft SQL server 2008 或更新版本上重新 SQL 附加元件。 搜尋 AIB 使用 Microsoft SQL server 全文檢索提供更精確的搜尋結果比搜尋 WAMI 所產生的字幕檔案。 這是因為 AIB 包含 word 替代方案類似的音效而字幕檔案包含最高信賴 word 音訊的每個區段。 如果搜尋有聲文字是 upmost 重要，建議您是使用 Microsoft SQL Server AIB 中搭配使用。<br/><br/> 若要下載附加元件，按一下 [ <a href="http://aka.ms/indexersql">Azure 媒體重新 SQL 附加元件</a>]。 <br/><br/>您也可利用其他搜尋引擎例如 Apache Lucene/Solr 只要索引視訊字幕及關鍵字 XML 檔案，但會導致不正確的搜尋結果。
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |關閉沙米文、 TTML 及 WebVTT 格式的標題 （副本） 檔案。<br/><br/>他們可以用於障礙人士聽力殘障人士音訊和視訊檔案。<br/><br/>已關閉的標題檔案包含稱為<b>Recognizability</b>編製索引作業根據視訊來源中的方式可辨識語音的分數是標記。  您可以使用的<b>Recognizability</b>值合用性的畫面輸出檔案。 低分數表示因為音訊品質不佳編製索引的結果。
__InputFileName.kw.xml<br />InputFileName.info__ |關鍵字和資訊的檔案。 <br/><br/>XML 檔案包含從語音內容中，展開頻率與位移的資訊的關鍵字的關鍵字檔案。 <br/><br/>純文字檔，其中包含細微資訊將每個字詞資訊檔案。 第一行特殊，並包含 Recognizability 分數。 後續的每一行是 tab 字元分隔的清單下列資料︰ 啟動時間、 結束時間、 word/片語、 信賴度。 時間會取得秒數，confidence 提供為數字，從 0-1。 <br/><br/>行範例: 「 1.20 1.45 word 0.67 」 <br/><br/>這些檔案可以的數字的用途，例如，用來執行語音分析，或搜尋引擎，例如 Bing、 Google 或 Microsoft SharePoint 將媒體檔案更容易找到，或甚至是用來發表更多相關的廣告上公開。
__JobResult.txt__ |輸出資訊清單，有多個檔案，包含下列資訊編製索引作業時，只︰<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias （別名)</th><th>MediaLength</th><th>錯誤</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



如果不成功索引的所有輸入的媒體檔案，會失敗，出現錯誤碼 4000 編製索引作業。 如需詳細資訊，請參閱[錯誤碼](#error_codes)。

## <a name="index-multiple-files"></a>編製索引的多個檔案

下列方法上傳多個媒體檔案的資產，為，然後建立索引以批次的所有這些檔案工作。

建立並上傳到資產.lst 副檔名資訊清單的檔案。 資訊清單檔案包含所有資產檔案的清單。 如需詳細資訊，請參閱[Azure 媒體索引器預設的任務](https://msdn.microsoft.com/library/dn783454.aspx)。

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>部分成功的工作

如果不成功索引的所有輸入的媒體檔案，會失敗，出現錯誤碼 4000 編製索引作業。 如需詳細資訊，請參閱[錯誤碼](#error_codes)。


產生相同的輸出 （以成功的工作）。 您可以參考，找出哪些輸入的檔案會失敗，根據錯誤資料行值輸出資訊清單的檔案。 輸入失敗的檔案，並產生 AIB、 沙米文、 TTML、 WebVTT 和關鍵字的檔案不會產生。

### <a id="preset"></a>Azure 媒體索引子任務預設

從 Azure 媒體重新處理可以自訂提供選擇性預設同時工作的工作。  下列說明此設定的 xml 的格式。

名稱 | 需要 | 描述
----|----|---
__輸入__ | false | 您想要編製索引的資產檔案。</p><p>Azure 媒體重新支援下列媒體檔案格式︰ MP4、 WMV、 MP3、 M4A、 WMA、 AAC、 WAV。</p><p>（如下圖所示），您可以**輸入**的項目**名稱**] 或 [**清單**屬性中指定的檔案名稱 (s)。如果您沒有指定要索引的資產檔案，選取主要的檔案。 如果沒有主要資產檔設定]，輸入資產中的第一個檔案就會建立索引。</p><p>若要明確指定資產檔案名稱，請執行︰<br />`<input name="TestFile.wmv">`<br /><br />您也可以索引多個資產檔案一次 （最多 10 個檔案）。 若要執行這項操作︰<br /><br /><ol class="ordered"><li><p>建立文字檔案 （資訊清單檔案），並為它.lst 副檔名。 </p></li><li><p>在您輸入的資產加入此資訊清單檔案的資產檔案名稱的清單。 </p></li><li><p>新增 （上傳） thanifest 檔案的資產。  </p></li><li><p>在輸入清單屬性中指定的資訊清單檔案的名稱。<br />`<input list="input.lst">`</li></ol><br /><br />注意︰ 如果您的資訊清單的檔案新增 10 封以上的檔案，編製索引作業會失敗 2006年錯誤的程式碼。
__中繼資料__ | false | 用於詞彙配接指定的資產檔案的中繼資料。  準備重新辨識非標準詞彙關鍵字，例如專有名詞很有用。<br />`<metadata key="..." value="..."/>` <br /><br />您可以提供的預先定義的__索引鍵__的__值__。 目前支援下列機碼︰<br /><br />「 標題 」 與 「 描述 」-用於詞彙配接調整語言模型工作，並改善語音辨識的正確性。  值植網際網路搜尋來尋找更相關的文字文件，來加強內部字典編製索引工作期間使用內容。<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__功能__ <br /><br /> 新增 1.2 版。 目前，只支援的功能是語音辨識 (「 ASR 」)。| false | 語音辨識功能有下列設定機碼︰<table><tr><th><p>索引鍵</p></th>     <th><p>描述</p></th><th><p>範例值</p></th></tr><tr><td><p>語言</p></td><td><p>若要將多媒體檔案中自然語言。</p></td><td><p>英文、 西班牙文</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>以分號分隔清單所要的輸出標題的格式 （如果有的話）</p></td><td><p>ttml; 沙米文; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>布林值的旗標，指定 AIB 檔案需要 （適用於 SQL Server 與客戶重新 i 篩選使用）。  如需詳細資訊，請參閱<a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Azure 媒體重新與 SQL Server 使用 AIB 檔案</a>。</p></td><td><p>True;False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>布林值的旗標，指定為必要的關鍵字 XML 檔案。</p></td><td><p>True;為 false。 </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>布林值的旗標，指定要強制完整標題 （不論信賴度等級）。  </p><p>在此情況下單字或片語，必須小於 50%信賴度省略最終的標號輸出而由省略符號 （…]），預設值為 false。  省略符號有助標題品質控制及稽核。</p></td><td><p>True;為 false。 </p></td></tr></table>

### <a id="error_codes"></a>錯誤碼

如果發生錯誤，報告 Azure 媒體重新返回下列錯誤碼其中一項︰

程式碼 | 名稱 | 可能的原因
-----|------|------------------
2000 | 不正確的設定 | 不正確的設定
2001 | 無效的輸入的資產 | 遺漏輸入的資產或空白的資產。
2002 | 無效的資訊清單 | 資訊清單是空值，或顯示含有無效的項目。
2003 | 無法下載媒體檔案 | 資訊清單檔案中有無效的 URL。
2004 | 不支援的通訊協定 | 不支援的媒體 URL 的通訊協定。
2005 | 不支援的檔案類型 | 輸入的媒體檔案類型不受支援。
2006 | 輸入的檔案太多 | 輸入資訊清單中有 10 封以上的檔案。
3000 | 無法解碼媒體檔案 | 不支援的媒體轉碼器 <br/>或<br/> 損毀的媒體檔案 <br/>或<br/> 在 [輸入媒體沒有音訊資料流。
4000 | 部分編製索引批次成功 | 輸入的媒體檔案的部分會無法能編製索引。 如需詳細資訊，請參閱<a href="#output_files">輸出檔案</a>。
其他 | 內部錯誤 | 請連絡支援小組。 indexer@microsoft.com


## <a id="supported_languages"></a>支援的語言

目前，支援英文和西班牙文的語言。 如需詳細資訊，請參閱[v1.2 發行部落格文章](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>相關的連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體重新與 SQL Server 使用 AIB 檔案](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Azure 媒體重新 2 預覽編製索引的媒體檔案](media-services-process-content-with-indexer2.md)
