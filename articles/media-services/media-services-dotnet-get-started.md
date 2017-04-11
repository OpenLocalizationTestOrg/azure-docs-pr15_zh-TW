<properties
    pageTitle="快速入門發表內容，視需要使用.NET |Azure"
    description="本教學課程中會引導您執行上視需要內容傳遞應用程式與使用.NET Azure 媒體服務的步驟。"
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>開始使用視需要使用.NET SDK 發表內容

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](/pricing/free-trial/?WT.mc_id=A261C142F)。 
 
##<a name="overview"></a>概觀 

本教學課程中會引導您執行 Video-on-Demand (VoD) 內容傳遞的應用程式，使用.NET Azure 媒體服務 （反飛彈系統） SDK 的步驟。


教學課程介紹基本的媒體服務工作流程的最常見的程式設計物件及媒體服務開發所需的工作。 在完成教學課程中，您可以將串流或漸進下載的範例媒體檔案上傳、 編碼，及下載。

## <a name="what-youll-learn"></a>您將學習

教學課程介紹如何完成下列工作︰

1.  建立媒體服務帳戶 （使用 Azure 入口網站）。
2.  設定串流端點 （使用 Azure 入口網站）。
3.  建立及設定 Visual Studio 專案。
5.  連線至媒體服務帳戶。
6.  建立新的資產上, 傳的視訊檔案。
7.  將一組調整位元率 MP4 檔案編碼來源檔案。
8.  發佈資產，並取得串流或漸進的下載的 Url。
9.  測試播放您的內容。

## <a name="prerequisites"></a>必要條件

下列步驟，才能完成教學課程。

- 若要完成此教學課程中，您需要 Azure 帳戶。 
    
    如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](/pricing/free-trial/?WT.mc_id=A261C142F)。 您會收到可以用來嘗試付費 Azure 服務的信用額度總計。 即使將貸項總計用完之後，您可以保留帳戶，並使用免費的 Azure 服務和功能，例如 Azure 應用程式服務的 Web 應用程式功能。
- 作業系統︰ Windows 8 或更新版本，Windows 2008 R2 Windows 7。
- .NET framework 4.0 或更新版本
- Visual Studio 2010 SP1 （Professional、 Premium、 Ultimate 或 Express） 或更新版本。


##<a name="download-sample"></a>下載範例

取得，並執行從[這裡](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)的範例。

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>建立使用 Azure 入口網站 Azure 媒體服務帳戶

本節中的步驟會顯示如何建立反飛彈系統帳戶。

1. 在[Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下**[+ 新增** > **媒體 + CDN** > **媒體服務**。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. 在**建立媒體服務帳戶**輸入必要的值。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. 在 [**帳戶名稱**] 中，輸入新的反飛彈系統帳戶的名稱。 媒體服務帳戶名稱是全部小寫的數字或字母不加空格，，3 至 24 個字元的長度。
    2. 在訂閱中，選取 [之間的不同的 Azure 訂閱，您有權存取]。
    
    2. 在 [**資源] 群組**中，選取新的或現有的資源。  資源群組是共用生命週期、 權限及原則的資源的集合。 深入瞭解[以下](azure-resource-manager/resource-group-overview.md#resource-groups)。
    3. 在 [**位置**]，選取地理區域用來儲存您的媒體服務帳戶的媒體及中繼資料記錄。 此區域用來處理和串流媒體。 使用媒體服務區域會出現在下拉式清單方塊中。 
    
    3. 在**儲存帳戶**，選取儲存的帳戶提供媒體內容媒體服務帳戶的 blob 儲存體。 您可以選取現有的儲存空間帳戶中相同的地理區域，為您的媒體服務帳戶，或者您也可以建立儲存帳戶。 在同一個區域中建立新的儲存空間帳戶。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。

        進一步瞭解儲存空間[以下](storage-introduction.md)。

    4. 選取 [**固定至儀表板**，若要查看帳戶部署的進度。
    
7. 按一下 [**建立**底部的表單]。

    帳戶已成功建立之後，狀態會變更為**執行**。 

    ![媒體服務設定](./media/media-services-portal-vod-get-started/media-services-settings.png)

    若要管理您的反飛彈系統帳戶 （例如上, 傳視訊編碼資產、 監控工作進度） 使用 [**設定**] 視窗。

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>使用 [Azure 入口網站的串流端點的設定

使用其中一個最常見的案例會將透過調整的位元率串流視訊至您的用戶端 Azure 媒體服務。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

媒體服務會提供可讓您在發表您調整的位元率串流媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 只的時間，您不必儲存的每個串流格式預先封裝的版本支援的格式編碼 MP4 內容的動態包裝。

若要充分利用動態包裝，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案 （示範稍後在本教學課程編碼步驟） 編碼 mezzanine （來源） 檔案。  
- 建立至少有一個串流單位*串流端點*從您打算傳送您的內容。 下列步驟說明如何變更串流單位數量。

動態包裝，您只需要儲存及支付中單一儲存格式的檔案與媒體服務建置，並提供適當的回應，根據用戶端的要求。

若要建立並變更數串流保留的單位，請執行下列動作︰


1. 在 [**設定**] 視窗中，按一下 [**串流結束點**]。 

2. 按一下 [預設串流結束點]。 

    **預設串流端點詳細資料**視窗隨即出現。

3. 若要指定串流單位數量，請將滑**串流單位**。

    ![串流單位](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. 按一下 [**儲存**] 按鈕，以儲存變更]。

    >[AZURE.NOTE]分派的任何新的單位花費 20 分鐘才能完成。

##<a name="create-and-configure-a-visual-studio-project"></a>建立及設定 Visual Studio 專案

1. 建立新 C# 主控台應用程式在 Visual Studio 2013、 Visual Studio 2012 或 Visual Studio 2010 SP1。 輸入**名稱**、**位置**，以及**方案名稱**，然後再按一下**[確定]**。

2. 使用[windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) NuGet 封裝安裝**Azure 媒體服務.NET SDK 擴充功能**。  媒體服務.NET SDK 擴充功能是一組擴充方法和協助函數來簡化程式碼，並使其更輕鬆地與媒體服務開發。 安裝此套件，也會安裝**媒體服務.NET SDK**並新增所有其他必要的相依性。

3. 新增 System.Configuration 組件的參考。 這個組件的內容會用來存取設定檔案，例如 App.config **System.Configuration.ConfigurationManager**類別。

4. 開啟 App.config 檔案 （如果未新增根據預設，檔案新增至您的專案） 並將*和 appSettings*區段新增至該檔案。 設定您 Azure 媒體服務帳戶名稱和帳戶金鑰] 的值，如下列範例所示。 若要取得客戶名稱] 和 [重要資訊，請移至[Azure 入口網站](https://portal.azure.com/)，選取您反飛彈系統帳戶。 然後，選取 [**設定** > **鍵**。 管理鍵 windows 顯示的帳戶名稱，並顯示主要和次要鍵。

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. 下列程式碼會覆寫現有**使用**陳述的式的開頭 Program.cs 檔案。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. 建立新專案目錄下的資料夾，然後複製您想要編碼及串流或漸進下載.mp4 或.wmv 檔案。 在此範例中，會使用 「 C:\VideoFiles 」 路徑。

##<a name="connect-to-the-media-services-account"></a>連線至媒體服務帳戶

時使用.NET 媒體服務，您必須使用**CloudMediaContext**類別大部分的媒體服務程式設計工作︰ 連線到媒體服務帳戶。建立、 更新、 存取和刪除下列物件︰ 資產、 資產檔案、 工作、 存取原則、 定位器等。

下列程式碼會覆寫預設程式的類別。 程式碼示範如何讀取連接值從 App.config 檔案，以及如何建立**CloudMediaContext**物件，才能連線到媒體服務。 如需有關如何連線至媒體服務的詳細資訊，請參閱[連線至與媒體服務 SDK.net 媒體服務。](http://msdn.microsoft.com/library/azure/jj129571.aspx)

**主要**函數呼叫會定義方法，進一步此區段中。

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>建立新的資產，然後上傳的視訊檔案

在媒體服務，您上傳 （或內嵌） 資產將數位檔案。 **資產**實體可以包含視訊、 音訊、 圖像、 縮圖的集合，文字追蹤及字幕檔案 （及這些檔案的中繼資料。） 上傳檔案後您的內容是安全地儲存在雲端的進一步處理和資料流。 資產中的檔案被稱為**資產檔案**。

**UploadFile**定義的方法下方通話**CreateFromFile** （.NET SDK 副檔名所定義）。 **CreateFromFile**會建立新上傳到指定的來源檔案的資產。

**CreateFromFile**方法需要**AssetCreationOptions** ，可讓您指定下列的資產建立選項的其中一個︰

- 使用**無**-不加密。 這是預設值。 請注意，使用此選項時，您的內容未受到保護傳送或儲存區中的其餘部分。
如果您打算將 MP4 使用漸進下載時，請使用這個選項。
- **StorageEncrypted** -使用這個選項，即可加密本機使用進階加密標準 (AES)-256 元加密，然後將其上傳的儲存位置的 Azure 儲存體清除內容加密其餘部分。 受到保護的儲存空間加密的資產會自動加密放在之前編碼，加密的檔案系統中和您也可以選擇重新加密之前上傳回到為新的輸出資產。 儲存加密的主要的使用情況時，您要保護磁碟上的使用加密靜止高品質輸入的媒體檔案。
- **CommonEncryptionProtected** -使用此選項，如果您要上傳的已加密並以一般加密或 PlayReady DRM （例如，平滑串流保護與 PlayReady DRM） 保護內容。
- **EnvelopeEncryptionProtected** – 使用這個選項，如果您要上傳 HLS 以 AES 加密。 請注意，檔案必須有已編碼加密轉換的管理員。

**CreateFromFile**方法也可讓您指定回撥，才能報表的檔案上傳進度。

在下列範例中，我們指定**無**[資產] 選項。

新增下列方法程式類別。

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>將一組調整位元率 MP4 檔案編碼來源檔案

之後將媒體服務 ingesting 資產，可媒體編碼，transmuxed 的浮水印，依此類推時，它會傳送至用戶端前。 這些活動的排程，並針對多個背景角色執行個體，以確保高效能與可用性執行。 這些活動的工作，及每項工作是由原子執行資產檔案的實際工時的任務。

上述，使用 Azure 媒體服務時，最常見的案例的其中一個進行調整串流至您的用戶端的位元率。 媒體服務可以動態封裝成下列格式的其中一個的一組調整位元率 MP4 檔案︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

若要充分利用動態包裝，您需要執行下列動作︰

- 編碼或轉換成一組調整位元率 MP4 檔案或調整位元率平滑串流檔案您 mezzanine （來源） 檔案。  
- 至少有一個串流單位取得串流端點從您打算傳送您的內容。

下列程式碼會顯示如何提交編碼的工作。 工作包含指定轉碼 mezzanine 檔案成一組調整位元率 MP4s 使用**Media Encoder 標準**的一項工作。 程式碼送出工作，並等待，直到完成為止。

一旦完成工作，您都可以將您的資產串流或漸進下載當做轉碼所建立的 MP4 檔案。
請注意，您不需要才能漸進下載 MP4 檔案中有多個 0 串流單位。

新增下列方法程式類別。

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>發佈資產並取得串流或漸進的下載的 Url

若要將串流或下載資產，您必須 「 發佈 」，您可以建立定位器。 定位器提供存取資產中所包含的檔案。 媒體服務支援兩種類型的定位器︰ OnDemandOrigin 定位器，用來串流媒體 （例如，MPEG 破折號、 HLS 或平滑串流） 和存取簽章 (SA) 定位器用來下載媒體檔案。

建立定位器之後，您可以建立可用於串流或下載檔案的 Url。


帶有平滑串流串流 URL 具有下列格式︰

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 串流 URL 具有下列格式︰

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG 虛線串流 URL 具有下列格式︰

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

下載檔案 SA URL 具有下列格式︰

    {blob container name}/{asset name}/{file name}/{SAS signature}

媒體服務.NET SDK 擴充功能提供方便協助方法，傳回格式的已發佈資產的 Url。

下列程式碼會使用.NET SDK 擴充功能建立定位器並取得串流和漸進下載 Url。 程式碼也會顯示如何將檔案下載到本機的資料夾。

新增下列方法程式類別。

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>測試播放您的內容  

一旦您執行的前一節中所定義的程式，類似以下的 Url 會顯示主控台視窗中。

調整串流 Url:

帶有平滑串流

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG 破折號

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

漸進下載 Url （音訊與視訊）。

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


若要串流視訊，使用[Azure 媒體服務播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

若要測試漸進下載，將 URL 貼到瀏覽器 （例如，Internet Explorer、 Chrome 或 Safari）。


##<a name="next-steps-media-services-learning-paths"></a>後續步驟︰ 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>想找其他項目？

如果本主題未包含什麼您所預期，就會缺少項目，或其他方式不符合您的需求，請提供我們使用 Disqus 執行緒下您的意見反應。


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
