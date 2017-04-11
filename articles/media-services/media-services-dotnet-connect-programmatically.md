<properties 
    pageTitle="使用.NET 媒體服務帳戶的連線" 
    description="本主題示範如何連線至媒體服務 uisng.NET。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>連線到使用.NET 媒體服務 SDK 的媒體服務帳戶

> [AZURE.SELECTOR]
- [其餘](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


本主題說明如何使用媒體服務 SDK.net 設計程式時，取得以程式設計方式連線至 Microsoft Azure 媒體服務。


## <a name="connecting-to-media-services"></a>連線至媒體服務

若要以程式設計方式連線媒體服務，您必須有先前設定 Azure 帳戶、 設定媒體服務帳戶，然後設定 Visual Studio 專案的開發與媒體服務 SDK.net。 如需詳細資訊，請參閱設定開發與媒體服務 SDK.net。

媒體服務帳戶設定程序結尾您取得所需的連線為以下值。 若要以程式設計方式連線媒體服務中使用這些。

- 您媒體服務帳戶的名稱。

- 您的媒體服務帳戶金鑰。

若要尋找這些值，移至 Azure 安全性群組入口網站選取您的媒體服務帳戶，然後按一下視窗底部的入口網站上的 「**管理鍵**」 圖示。 每個文字方塊旁的圖示上按一下 [將值複製到剪貼簿] 系統。


## <a name="creating-a-cloudmediacontext-instance"></a>建立 CloudMediaContext 執行個體

若要開始程式設計媒體服務必須建立**CloudMediaContext**執行個體，表示伺服器內容。 **CloudMediaContext**包含重要的集合，其中包括工作、 資產、 檔案、 存取原則，與定位器的參照。

>[AZURE.NOTE] **CloudMediaContext**類別不安全的執行緒。 您應該建立新的 CloudMediaContext 每個執行緒或一組作業。


CloudMediaContext 有五個建構函式多載。 建議使用使用**MediaServicesCredentials**做為參數建構函式。 如需詳細資訊，請參閱**重複使用 Access 控制項服務權杖**所示。 

下列範例會使用公用 CloudMediaContext(MediaServicesCredentials credentials) 建構函式︰

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>重複使用 Access 控制項服務權杖

本節說明如何藉由使用使用做為參數 MediaServicesCredentials CloudMediaContext 建構函式重複使用 Access 控制項服務權杖。


[Azure Active Directory 存取控制](https://msdn.microsoft.com/library/hh147631.aspx)（也稱為存取控制服務或 ACS） 是雲端服務，讓您輕鬆的驗證及授權的使用者存取他們的 web 應用程式。 Microsoft Azure 媒體服務控制透過存取其服務所需 ACS 權杖 OAuth 通訊協定。 媒體服務接收 ACS 權杖從授權伺服器。

在開發與媒體服務 SDK，您可以選擇不處理權杖，因為 SDK 程式碼管理員為您的地方。 不過，讓 SDK 完全管理 ACS 權杖會導致不必要的權杖要求。 要求權杖花的時間，並會使用用戶端與伺服器資源。 此外，ACS 伺服器如果利率為得太高節流處理要求。 限制是 30 秒的要求，請參閱[ACS 服務限制](https://msdn.microsoft.com/library/gg185909.aspx)的更多詳細資料。

開始使用媒體服務 SDK 3.0.0.0 版，您可以重複使用 ACS 權杖。 使用**MediaServicesCredentials**做為參數**CloudMediaContext**建構函式啟用共用 ACS 權杖，多個內容之間。 MediaServicesCredentials 類別封裝媒體服務的認證。 如果 ACS 權杖有已知到期時間，您可以建立新的 MediaServicesCredentials 執行個體與權杖，並為了 CloudMediaContext 的建構函式。 請注意，媒體服務 SDK 自動重新整理權杖時過期。 有兩種方式可重複使用 ACS 權杖，如下列範例所示。

- 您可以快取**MediaServicesCredentials**記憶體中的物件 （例如，在靜態類別變數）。 然後，為了 CloudMediaContext 建構函式的快取的物件。 MediaServicesCredentials 物件包含可重複使用它是否仍然有效的 ACS 權杖。 如果權杖不是有效的它會重新整理所使用的認證提供給 MediaServicesCredentials 建構函式媒體服務 SDK。

    請注意**MediaServicesCredentials**物件取得有效的權杖之後 RefreshToken 稱為。 **CloudMediaContext**通話建構函式**RefreshToken**方法。 如果您打算將 token 值儲存到外部的儲存空間，請務必檢查 TokenExpiration 值之前儲存的權杖的資料是否有效。 如果您不正確，請撥打 RefreshToken 之前快取。

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- 您也可以快取 AccessToken 字串及 TokenExpiration 值。 若要建立新的 MediaServicesCredentials 物件快取的權杖資料可稍後到值。  這是位置權杖能安全地共用多個程序或電腦的案例的尤其有用。

    下列程式碼片段呼叫 SaveTokenDataToExternalStorage GetTokenDataFromExternalStorage，與 UpdateTokenDataInExternalStorageIfNeeded 定義的方法，是不在此範例中。 您可以定義這些方法來儲存、 擷取和更新外部的儲存空間中的權杖資料。 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    若要建立 MediaServicesCredentials 使用已儲存的 token 值。


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    更新 token 複本，以避免權杖已被媒體服務 SDK 的更新。 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- 如果您有多個媒體服務帳戶 （例如，針對載入共用或地理通訊群組），您可以快取 MediaServicesCredentials 物件使用 System.Collections.Concurrent.ConcurrentDictionary 集合 （ConcurrentDictionary 集合表示執行緒安全的集合可以同時存取的多執行緒索引鍵/值組）。 您可以再使用 [GetOrAdd 方法以取得快取的認證。 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>連線到位於中國北美地區的媒體服務帳戶

如果您的帳戶是北美 china （中國） 區域中，請使用下列建構函式︰

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

例如︰


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>在 [設定儲存連線的值

則強烈建議儲存設定連線的值，例如您的帳戶名稱和密碼，尤其是敏感的值。 此外，則若要將加密機密設定資料的建議。 您可以使用 Windows 加密檔案系統 (EFS)，來加密整個的設定檔。 若要啟用 EFS 檔案，以滑鼠右鍵按一下檔案**摘要資訊**]，然後選取啟用 [**進階**設定] 索引標籤中的加密。 或者，您可以建立自訂解決方案，來加密的設定檔選取的部分使用受保護的設定。 [加密設定資訊使用受保護的設定](https://msdn.microsoft.com/library/53tyfkaw.aspx)，請參閱。

下列 App.config 檔案包含所需的連線。 中的值<appSettings>項目會從媒體服務帳戶設定程序取得的必要的值。

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


若要從設定擷取連接值，您可以使用**ConfigurationManager**類別，並在程式碼然後指派給] 欄位的值︰
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
