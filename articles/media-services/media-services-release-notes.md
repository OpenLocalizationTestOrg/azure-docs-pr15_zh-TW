<properties 
    pageTitle="媒體服務版本資訊 |Microsoft Azure" 
    description="媒體服務版本資訊" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Azure 媒體服務版本資訊

這些版本資訊摘要從先前的版本和已知的問題的變更。

>[AZURE.NOTE] 我們想要聆聽我們的客戶，並著重於修正會影響您的問題。 若要報告問題或提出問題，請張貼[Azure 媒體服務 MSDN 論壇]。


##<a id="issues"></a>目前已知的問題

### <a id="general_issues"></a>媒體服務一般問題

問題|描述
---|---
REST API 不提供數種常見的 HTTP 標頭。|若要開發使用 REST API 的媒體服務應用程式，您找到的一些常見的 HTTP 標題欄位 (包括用戶端要求識別碼，要求識別碼和傳回用戶端-要求識別碼) 不受支援。 在未來的更新，便會新增標題。
不允許的百分比編碼。|媒體服務串流內容 (例如，http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters。) 建置 Url 時，請使用 IAssetFile.Name 屬性的值因此，不允許的百分比編碼。 [**名稱**] 屬性的值不能有下列[百分比編碼-保留字元](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)︰ !*'();:@&=+$,/?%#[]"。 此外，只能有一個 「。 」 針對檔案的副檔名。
屬於 Azure 儲存體 SDK 版本 3.x 失敗 ListBlobs 方法。|媒體服務會產生 SA Url [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx)版本。 如果您想要使用 blob 容器中的清單 blob Azure 儲存體 SDK，使用[CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx)方法屬於 Azure 儲存體 SDK 版本的 2.x。 組件的 ListBlobs 方法 Azure 儲存體 SDK 版本的 3.x 會失敗。
節流機制媒體服務限制應用程式服務進行過多的要求，資源使用的狀況。 服務可能會傳回服務無法使用 (503) HTTP 狀態碼。|如需詳細資訊，請參閱 503 HTTP 狀態碼[Azure 媒體服務錯誤碼](http://msdn.microsoft.com/library/azure/dn168949.aspx)主題中描述。
當查詢項目，就會傳回一次，因為公用其餘 v2 限制 1000年結果的查詢結果的實體 1000年的限制。 | 您需要使用**[略過**並**採取**(.NET) /**頂端**（剩餘） 為所述[本.NET 範例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)，[此 REST API 範例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)。 
跨平滑串流資訊清單中的重複標籤問題可能有些用戶端。|如需詳細資訊，請參閱[本節](media-services-deliver-content-overview.md#known-issues)。
Azure 媒體服務.NET SDK 物件無法序列化，因此不適用於 Azure 快取。|如果您嘗試序列化 SDK AssetCollection 物件，將其新增至 Azure 快取，例外狀況。
編碼的工作失敗訊息字串 「 階段︰ DownloadFile。 程式碼︰ System.NullReferenceException 」。|一般編碼的工作流程是將輸入視訊檔案上傳至資產輸入並提交的輸入資產的輸入資產，而不需要進一步修改的一個或多個編碼的工作。 不過，如果您修改輸入的資產 （例如藉由新增/刪除/重新命名檔案的資產內），後續的工作可能會失敗，DownloadFile 錯誤。 因應措施若要刪除輸入的資產，並重新上傳至新的資產的輸入的檔案。 

##<a id="rest_version_history"></a>REST API 版本歷程記錄

如媒體服務 REST API 版本歷程記錄的相關資訊，請參閱[Azure 媒體服務 REST API 參考]。

##<a id="july_changes16"></a>2016 年 7 月發行

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>資訊清單檔案的更新 (*。ISM) 產生編碼工作

當 Media Encoder 標準或 Azure Media Encoder 提交編碼的任務時，編碼的工作會產生[串流資訊清單的檔案](media-services-deliver-content-overview.md)(*.ism) 中，輸出檔案資產。 最新的服務版本已更新此串流資訊清單檔案的語法。

>[AZURE.NOTE]資料流資訊清單 (.ism) 檔案的語法保留內部使用，並會有所變更未來的版本。 請勿修改或處理此檔案的內容。

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>新的用戶端資訊清單 (*。輸出中會產生 ISMC) 檔案時編碼的任務將輸出的一或多個 MP4 檔案的資產

最新的服務版本之後, 開始產生一個編碼任務的完成更多的 MP4 檔案，輸出資產也會包含串流的用戶端資訊清單 (*.ismc) 檔案。 .Ismc 檔案可協助改善效能的動態資料流。 

>[AZURE.NOTE]用戶端資訊清單 (.ismc) 檔案的語法保留內部使用，並會有所變更未來的版本。 請勿修改或處理此檔案的內容。

如需詳細資訊，請參閱[此](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/)部落格。

### <a name="known-issues"></a>已知的問題

某些用戶端可能 aross 平滑串流資訊清單中的重複標籤問題。 如需詳細資訊，請參閱[本節](media-services-deliver-content-overview.md#known-issues)。

##<a id="apr_changes16"></a>2016 年 4 月發行

### <a name="azure-media-analytics"></a>Azure 媒體狀況分析

Azure 媒體 Servces 功能強大的視訊智慧的簡略 Azure 媒體分析。 如詳細資訊，請參閱[Azure 媒體服務分析概觀](media-services-analytics-overview.md)。

### <a name="apple-fairplay-preview"></a>Apple FairPlay （預覽版本）

Azure 媒體服務現在可讓您以動態方式加密您 HTTP Live 串流 (HLS) 內容與 Apple FairPlay。 您也可以使用反飛彈系統授權服務，用戶端進行 FairPlay 授權。 如需詳細資訊，請參閱[使用 Azure 媒體服務串流您 HLS 與 Apple FairPlay 受保護的內容](media-services-protect-hls-with-fairplay.md)。
  
##<a id="feb_changes16"></a>2016 年 2 月發行

最新版 Azure 媒體服務 SDK.net (3.5.3) 包含 Widevine 相關的錯誤修正。 問題︰ AssetDeliveryPolicy 無法以 Widevine 加密的多個資產的可重複使用。 屬於此修正 sdk 的變更新增下列屬性︰ **WidevineBaseLicenseAcquisitionUrl**。
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>2016 年 1 月發行

以減少混淆 Encoder 名稱重新命名編碼保留單位。

基本、 標準和進階版編碼保留的單位重新命名為 S1 S2，且 S3 保留單位，分別。  使用基本編碼 RUs 今天客戶會看到 S1 Azure 入口網站中，在的帳單的標籤時標準與進階版會標籤 S2 和 S3 分別。 

##<a id="dec_changes_15"></a>2015 年 12 月發行

Azure SDK 小組發佈[PHP Azure SDK](http://github.com/Azure/azure-sdk-for-php)套件，其中包含 Microsoft Azure 媒體服務更新與新功能的新的版本。 Azure 媒體服務 SDK PHP，現在支援最新的[內容保護](media-services-content-protection-overview.md)功能︰ 動態加密 AES 與 DRM （PlayReady 和 Widevine） 和權杖限制。 也支援縮放[編碼單位](media-services-dotnet-encoding-units.md)。

如需詳細資訊，請參閱︰

- [Microsoft Azure 媒體服務 SDK PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/)部落格。
- 下列[程式碼範例](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)，以協助您快速入門︰
    - **vodworkflow_aes.php**︰ 這是顯示如何使用 AES 128 動態加密和金鑰遞送服務 PHP 檔案。 [本文](media-services-protect-with-aes128.md)所述的.NET 範例為基礎。
    - **vodworkflow_aes.php**︰ 這是顯示如何使用 PlayReady 動態加密和授權服務 PHP 檔案。 [本文](media-services-protect-with-drm.md)所述的.NET 範例為基礎。
    - **scale_encoding_units.php**︰ 這是顯示如何調整編碼保留的單位 PHP 檔案。


##<a id="nov_changes_15"></a>2015 年 11 月發行

Azure 媒體服務現在提供在雲端的 Google Widevine 授權遞送服務。 如需詳細資訊，請參閱[此公告部落格](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)。 此外，請參閱[本教學課程](media-services-protect-with-drm.md)和[GitHub 存放庫](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)。 

請注意，Azure 媒體服務所提供的 Widevine 授權傳遞服務預覽中。 如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)。

##<a id="oct_changes_15"></a>2015 年 10 月發行

Azure 媒體服務 （反飛彈系統） 現在位於即時下列資料中心︰ 巴西南部、 印度西、 印度南部和印度中央。 您現在可以使用 Azure 傳統入口網站建立[媒體服務帳戶](media-services-portal-create-account.md)，並執行各種工作描述[以下](https://azure.microsoft.com/documentation/services/media-services/)。 不過，即時編碼中未啟用這些資料中心。 此外，並非所有類型的編碼保留單位都中, 找到這些資料中心。

- 巴西南部︰ 僅可使用標準及基本編碼保留單位
- 印度西、 印度南部和印度︰ 僅基本編碼保留單位


##<a id="september_changes_15"></a>2015 年 9 月發行 

- 反飛彈系統現在可讓您以保護 Video-On-Demand (VOD) 和 Widevine 模組化 DRM 技術的即時資料流。 您可以使用下列傳遞服務合作夥伴以協助您進行 Widevine 授權︰ [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)， [EZDRM](http://ezdrm.com/)， [castLabs](http://castlabs.com/company/partners/azure/)。 如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)。

    您可以使用 （從開始 3.5.1 的版本） 的[反飛彈系統.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)或 REST API 來設定您要使用 Widevine AssetDeliveryConfiguration。  

- 反飛彈系統加入 Apple ProRes 影片的支援。 您現在可以上傳您使用 Apple ProRes 或其他轉碼器的 QuickTime 來源視訊檔案。 如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)。

- 您現在可以使用媒體 Encoder 標準執行子剪輯和即時封存擷取。 如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)。

- 所做的下列篩選更新︰ 

    - 您現在可以使用 Apple HTTP Live 串流 (HLS) 格式的純音訊的篩選。 此更新可讓您藉由指定移除純音訊追蹤 (純音訊 = false) URL 中。
    - 在定義您的資產的篩選時，您現在可以合併多個 （最多 3) 在單一 URL 中的篩選。

    如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)部落格。

- 反飛彈系統現在會在 HLS v4 支援我的外框。 I 畫面支援最佳化快轉] 和 [播放後自動倒帶] 作業。 根據預設，所有 HLS v4 輸出都包含 I 畫面播放清單 (EXT-X-I-FRAME-STREAM-INF)。
 
    如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)部落格。

##<a id="august_changes_15"></a>2015 年 8 月發行

- Azure 媒體服務 SDK Java V0.8.0 版本和新的範例現在可供使用。 如需詳細資訊，請參閱︰

    - [部落格文章](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Java 範例存放庫](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- 具有多重音訊串流支援 azure Media Player 更新。 如需詳細資訊，請參閱︰
    - [部落格文章](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>2015 年 7 月發行

- 宣佈使用一般的顯示狀態的媒體 Encoder 標準。 如需詳細資訊，請參閱[此部落格文章](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)。

    媒體 Encoder 標準使用[本節](http://go.microsoft.com/fwlink/?LinkId=618336)所述的預設格式。 請注意時使用預設 4 k 編碼，您應該取得**進階版**保留單位類型。 如需詳細資訊，請參閱[如何比例編碼](media-services-scale-media-processing-overview.md)。
- Azure 媒體服務與程式的即時即時標題。 如需詳細資訊，請參閱[此部落格文章](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>媒體服務.NET SDK 更新

Azure 媒體服務.NET SDK 現在版本 3.4.0.0。 在這個版本中新增下列功能︰  

- 即時封存實作的支援。 請注意，您無法下載包含即時封存的資產。
- 實作的支援動態篩選。
- 實作的功能，讓使用者可以刪除資產時保留存放容器。
- 重試頻道中的原則相關錯誤修正。
- 啟用**媒體 Encoder 進階版工作流程**。

##<a id="june_changes_15"></a>2015 年 6 月發行

###<a name="media-services-net-sdk-updates"></a>媒體服務.NET SDK 更新

Azure 媒體服務.NET SDK 現在版本 3.3.0.0。 在這個版本中新增下列功能︰  

- 支援 OpenId 連線探索規格
- 處理鍵變換身分識別提供者端的支援。 

如果您使用公開 OpenID 連線探索文件的身分識別提供者 (與下列提供者︰ Azure Active Directory Google，Salesforce)，您可以指示以取得簽章金鑰的驗證 JWT 權杖從 OpenID 連線探索規格 Azure 媒體服務。 

如需詳細資訊，請參閱[使用 Json Web 鍵 OpenID 連線探索規格使用 JWT Azure 媒體服務中的權杖驗證](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)。


##<a id="may_changes_15"></a>2015 發行

宣佈使用下列的新功能︰

- [預覽的即時編碼與媒體服務](media-services-manage-live-encoder-enabled-channels.md)
- [動態資訊清單](media-services-dynamic-manifest-overview.md)
- [Azure 媒體 Hyperlapse 媒體處理器的預覽](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>2015 年 4 月發行

 ###<a name="general-media-services-updates"></a>一般媒體服務的更新

- [宣佈使用 Azure 媒體播放程式](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)。
- 媒體服務其餘 2.10，頻道設定為內嵌 RTMP 通訊協定從開始建立與主要和次要內嵌 Url。 如需詳細資訊，請參閱[頻道內嵌設定](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Azure 媒體重新更新
- 西班牙文的語言的支援
- 設定新的 xml 格式

如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)。
###<a name="media-services-net-sdk-updates"></a>媒體服務.NET SDK 更新

Azure 媒體服務.NET SDK 現在版本 3.2.0.0。

以下是一些對更新客戶︰

- **新變更**︰ **TokenRestrictionTemplate.Issuer**和**TokenRestrictionTemplate.Audience**為字串類型的變更。
- 若要建立自訂的相關更新重試原則。
- 若要上傳/下載檔案的相關錯誤修正。
- **MediaServicesCredentials**類別現在接受主要和次要存取控制端點進行驗證。



##<a id="march_changes_15"></a>2015 年 3 月發行

### <a name="general-media-services-updates"></a>一般媒體服務的更新

- 媒體服務現在提供 Azure CDN 整合。 若要支援整合， **StreamingEndpoint**加入**CdnEnabled**屬性。  可利用 REST Api 版本 2.9 開始著手使用**CdnEnabled** （如需詳細資訊，請參閱[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)）。  可利用.NET SDK 版本 3.1.0.2 開始著手使用**CdnEnabled** （如需詳細資訊，請參閱[StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)）。
- **媒體 Encoder 進階版**工作流程的通知。 如需詳細資訊，請參閱[介紹編碼 Azure 媒體服務中的進階版](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)。
 


##<a id="february_changes_15"></a>2015 年 2 月發行

### <a name="general-media-services-updates"></a>一般媒體服務的更新

媒體服務 REST API 現在版本 2.9。 開始使用此版本，您可以啟用 Azure CDN 整合串流結束點。 如需詳細資訊，請參閱[StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx)。

##<a id="january_changes_15"></a>2015 年 1 月發行

### <a name="general-media-services-updates"></a>一般媒體服務的更新

宣告的一般可用性 (GA) 的內容保護動態加密。 如需詳細資訊，請參閱[Azure 媒體服務增強串流證券 DRM 可用性一般技術](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)。

###<a name="media-services-net-sdk-updates"></a>媒體服務.NET SDK 更新

Azure 媒體服務.NET SDK 現在版本 3.1.0.1。

此版本標示為已過時的預設 Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate 建構函式。 新建構函式 TokenType 做為引數。

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>2014 年 12 月發行

###<a name="general-media-services-updates"></a>一般媒體服務的更新

- Azure 重新媒體處理器加入部分更新與新功能。 如需詳細資訊，請參閱[Azure 媒體重新版本 1.1.6.7 版本資訊](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)。
- 新增新的 REST API，可讓您更新編碼保留單位︰[與其他 EncodingReservedUnitType](http://msdn.microsoft.com/library/azure/dn859236.aspx)。
- 新增的 CORS 支援按鍵遞送服務。
- 已完成的查詢授權原則選項的效能提升。
- 在 china （中國） 資料中心，[鍵傳遞 URL](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url)是現在每位客戶 （就像其他資料中心）。
- 新增的 HLS 自動目標工期。 在執行即時資料流時，會永遠動態封裝 HLS。 根據預設，媒體服務會自動計算 HLS 區段包裝比 (FragmentsPerSegment) 根據畫面間隔 (KeyFrameInterval)，也稱為群組圖片的 – GOP 從即時 encoder 接收到的。 如需詳細資訊，請參閱[使用 Azure 媒體服務即時資料流]。
 
###<a name="media-services-net-sdk-updates"></a>媒體服務.NET SDK 更新

- [Azure 媒體服務.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/)現在版本 3.1.0.0。
- 升級至.net 4.5 的.Net SDK 相依性。
- 加入新的 API 可讓您更新編碼保留的單位。 如需詳細資訊，請參閱[更新保留單位類型] 及 [增加編碼 RUs 使用.NET](http://msdn.microsoft.com/library/azure/jj129582.aspx)。
- 新增的 JWT （JSON Web 權杖） 支援權杖驗證。 如需詳細資訊，請參閱[JWT token 驗證 Azure 媒體服務和動態加密](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。
- 新增相對位移 BeginDate 和 ExpirationDate PlayReady 授權範本中。


##<a id="november_changes_14"></a>2014 年 11 月發行

- 媒體服務現在可讓您透過 SSL 連線內嵌即時平滑串流 (FMP4) 的內容。 若要內嵌透過 SSL，請務必一併更新 ingest URL 為 HTTPS。  如需有關 live 串流，請參閱[Azure 媒體服務 Live 串流中使用]。
- 請注意，目前，您無法內嵌 RTMP 即時資料流透過 SSL 連線。
- 您也可以透過 SSL 連線串流內容。 若要這麼做，請確定您串流 Url 的開頭為 HTTPS。
- 請注意，您可以只串流透過 SSL 是否 2014 年 9 月 10 日之後建立串流端點進行您的內容。 如果您串流的 Url 根據串流年 9 月 10 以後建立的結束點，URL 會包含 「 streaming.mediaservices.windows.net 」 （新格式）。 包含 「 origin.mediaservices.windows.net 」 （舊格式） 串流 Url 不支援 SSL。 如果您的 URL 是以舊版格式，而且您希望能夠串流透過 SSL，[建立新的資料流結束點](media-services-portal-manage-streaming-endpoints.md)。 若要將您的內容串流透過 SSL 使用根據新的資料流端點建立的 Url。

##<a id="october_changes_14"></a>2014 年 10 月發行

### <a id="new_encoder_release"></a>媒體服務編碼器發行

宣佈使用媒體服務 Azure Media Encoder 的新版本。 與最新版 Azure Media Encoder 您只會針對輸出 Gb，但新 encoder 否則是與上一個 encoder 相容的功能。 如需詳細資訊[媒體服務價格詳細資料]）。

### <a id="oct_sdk"></a>媒體服務.NET SDK 

.NET 副檔名的媒體服務 SDK 現在版本 2.0.0.3。

媒體服務 SDK.net 現在版本 3.0.0.8。

進行下列變更︰

- 重構重試原則類別中。
- 新增使用者代理程式字串 http 要求標頭。
- 新增 nuget 還原建置步驟。
- 修正案例測試使用 x509 憑證從存放庫。
- 更新頻道與串流結束時，請驗證設定。
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Host （主機） 媒體服務範例的新 GitHub 存放庫

範例位於[Azure 媒體服務範例 GitHub 存放庫](https://github.com/Azure/Azure-Media-Services-Samples)。


##<a id="september_changes_14"></a>2014 年 9 月發行

媒體服務其餘的中繼資料現在版本 2.7。 如需最新的其餘部分更新的詳細資訊，請參閱[Azure 媒體服務 REST API 參考]。

現在版本 3.0.0.7.NET 媒體服務 SDK。
 
### <a id="sept_14_breaking_changes"></a>重大變更

* **原點**已重新命名為 [ [StreamingEndpoint]。
* 使用**Azure 傳統入口網站**編碼，然後將發佈 MP4 檔案時的預設行為中的變更。

先前，當使用 Azure 傳統入口網站發佈單一檔案 MP4 視訊資產 SA URL 建立 （SA Url 可讓您從 blob 儲存體下載視訊）。 目前，當您使用編碼，然後再發佈 [單一檔案 MP4 視訊資產 Azure 傳統入口網站，產生的 URL 就會指向串流端點 Azure 媒體服務。  這項變更並不會影響直接上傳至媒體服務，而不加以編碼 Azure 媒體服務發佈 MP4 視訊。

目前，您有下列兩個選項來解決問題。

* 啟用串流單位，然後使用動態包裝串流.mp4 資產成帶有平滑串流簡報。

* 建立下載 （或漸進播放）.mp4 SA url。 如需有關如何建立 SA 定位器的詳細資訊，請參閱[發表內容]。


### <a id="sept_14_GA_changes"></a>新功能/案例屬於 GA 發行

* **重新媒體處理器**。 如需詳細資訊，請參閱[Azure 媒體索引編製索引作業媒體檔案]。

* [StreamingEndpoint]實體現在可讓您新增 （主機） 的自訂網域名稱。

    要做為媒體服務串流結束點名稱的自訂網域名稱，必須先將自訂主機名稱新增至您的資料流結束點。 若要新增自訂主機名稱中使用的媒體服務 REST Api 或.NET SDK。
    
    下列事項︰
    
    * 您必須擁有自訂的網域名稱的擁有權。
    
    * Azure 媒體服務必須驗證的網域名稱的擁有權。 若要驗證的網域，建立地圖 CName <MediaServicesAccountId>。<parent domain> 若要 verifydns。 < mediaservices-dns 區域的 >。 
    
    * 您必須建立自訂主機名稱 (例如，sports.contoso.com) 對應到您的媒體服務 StreamingEndpont 主機名稱 (例如，amstest.streaming.mediaservices.windows.net) 的其他 CName。


    如需詳細資訊，請參閱[StreamingEndpoint]主題中的 [ **CustomHostNames** ] 屬性。

### <a id="sept_14_preview_changes"></a>新功能/案例屬於公用預覽版本

* 即時串流的預覽。 如需詳細資訊，請參閱[使用 Azure 媒體服務即時資料流]。

* 索引鍵的遞送服務。 如需詳細資訊，請參閱[使用 AES 128 動態加密和金鑰遞送服務]。

* 動態的 AES 加密。 如需詳細資訊，請參閱[使用 AES 128 動態加密和金鑰遞送服務]。

* PlayReady 授權服務。 如需詳細資訊，請參閱[使用 PlayReady 動態加密和授權服務]。

* PlayReady 動態加密。 如需詳細資訊，請參閱[使用 PlayReady 動態加密和授權服務]。

* 媒體服務 PlayReady 授權範本。 如需詳細資訊，請參閱[媒體服務 PlayReady 授權範本概觀]。

* 串流儲存加密資產。 如需詳細資訊，請參閱[串流儲存加密內容]。

##<a id="august_changes_14"></a>2014 年 8 月發行

當您編碼資產時，編碼工作完成時，會產生輸出資產。 在這個版本中，直到 Azure 媒體服務編碼器所產生的輸出資產的中繼資料。 開始使用此版本編碼器也會產生輸入資產的中繼資料。 如需詳細資訊，請參閱[輸入中繼資料]和[輸出中繼資料]的主題。


##<a id="july_changes_14"></a>2014 年 7 月發行

下列錯誤修正所做的 Azure 媒體服務封裝程式和加密子︰

* 僅限音訊播放時 transmuxing HTTP Live 串流 – 即時封存資產這個問題已經修正和音訊和視訊的播放現在。

* 包裝 HTTP Live 串流和 AES 128 位元信封加密資產，而且資料流時無法播放 Android 裝置上 – 已修正這個問題，封裝的串流播放支援 HTTP Live 串流的 Android 裝置上。

##<a id="may_changes_14"></a>2014 發行

### <a id="may_14_changes"></a>一般媒體服務的更新

您現在可以使用串流 HTTP Live 串流 (HLS) v3[動態包裝]。 若要串流 HLS v3，新增下列格式的原點定位器路徑: *.ism/manifest(format=m3u8-aapl-v3)。 如需詳細資訊，請參閱[Nick Drouin 部落格]。

動態包裝現在支援進行 HLS （v3 和 v4） 以 PlayReady 加密也會根據平滑串流靜態以 PlayReady 加密。 如何加密與 PlayReady 平滑串流的資訊，請參閱[使用 PlayReady 保護平滑的資料流]。

### <a name="may_14_donnet_changes"></a>媒體服務.NET SDK 更新

下列改良功能都包含在內媒體服務.NET SDK 3.0.0.5 版本︰

* 更好的速度與恢復上傳/下載媒體資產。

* 在 [重試邏輯和暫時性例外處理的改良功能︰ 

    * 暫時的錯誤偵測和重試邏輯已改良所造成的查詢，將變更儲存、 上傳或下載檔案的例外狀況。 
    
    * 取得時 Web 例外狀況 （例如，在 ACS 權杖的要求） 時，您會發現的嚴重錯誤而失敗更快速現在。

如需詳細資訊，請參閱[重試邏輯媒體服務 SDK.net 中]。

##<a id="april_changes_14"></a>2014 年 4 月 Encoder 發行

### <a name="april_14_enocer_changes"></a>媒體服務編碼器更新

* 新增的支援 ingesting AVI 檔案撰寫草闊谷 EDIUS 非線性編輯器，視訊在哪裡輕輕壓縮使用總部 HQX 方式草闊谷轉碼器。 如需詳細資訊，請參閱[草闊谷宣告 EDIUS 7 串流到雲端]。

* 加入的支援指定 Media Encoder 產生的檔案的命名慣例。 如需詳細資訊，請參閱[控制媒體服務編碼器輸出檔案名稱]。

* 新增視訊或音訊覆疊的支援。 如需詳細資訊，請參閱[建立覆疊]。

* 加入的支援訂放在一起的多個視訊的區段。 如需詳細資訊，請參閱[訂視訊區段]。

* 固定相關的音訊位置編碼 mpeg-1 音訊層 3 (又稱 MP3) MP4s 轉碼的錯誤。


##<a id="jan_feb_changes_14"></a>2014 年 1 月/年 2 月的版本

### <a name="jan_fab_14_donnet_changes"></a>Azure 媒體服務.NET SDK 3.0.0.1、 3.0.0.2 和 3.0.0.3

在 3.0.0.1 和 3.0.0.2 變更，包括︰

* 固定的排序方式陳述式的 LINQ 查詢的使用狀況相關的問題。

* 分割測試方案[GitHub]到單位為基礎的測試和案例為基礎的測試。

如需詳細資訊所做的變更，請參閱︰ [Azure 媒體服務.NET SDK 3.0.0.1 和 3.0.0.2 發行]。

在 3.0.0.3 進行下列變更︰

* 若要使用版本 3.0.3.0 升級 Azure 儲存體相依性。 

* 修正 3.0 的回溯相容性問題。*.* 發行版本。 


##<a id="december_changes_13"></a>2013 年 12 月發行

### <a name="dec_13_donnet_changes"></a>Azure 媒體服務.NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x 版本不相容 2.4.x.x 版本。

最新版本的媒體服務 SDK 現在 3.0.0.0。 您可以從 Nuget 下載最新的套件，或從[GitHub]取得位元。

開始使用媒體服務 SDK 3.0.0.0 版，您可以重複使用的[Azure Active Directory Access 控制項服務 (ACS)]權杖。 如需詳細資訊，請參閱[連線至媒體服務與媒體服務 SDK.net]主題中的 「 重複使用 Access 控制項服務權杖 」 區段。

### <a name="dec_13_donnet_ext_changes"></a>Azure 媒體服務.NET SDK 擴充功能 2.0.0.0

Azure 媒體服務.NET SDK 擴充功能是一組擴充方法和協助函數來簡化程式碼，並更容易開發 Azure 媒體服務使用。 從[Azure 媒體服務.NET SDK 擴充功能]，您可以取得最新的位元。

##<a id="november_changes_13"></a>2013 年 11 月發行

### <a name="nov_13_donnet_changes"></a>Azure 媒體服務.NET SDK 的變更

開始使用此版本，.NET 媒體服務 SDK 控點的暫時性錯誤可能會發生錯誤時撥打電話給媒體服務 REST API 圖層。

##<a id="august_changes_13"></a>2013 年 8 月發行

### <a name="aug_13_powershell_changes"></a>包含在 Azure Sdk 工具中的媒體服務 PowerShell Cmdlet

[Azure sdk 工具]中現在包含下列的媒體服務 PowerShell cmdlet。

* 取得 AzureMediaServices 

    例如， `Get-AzureMediaServicesAccount`。

* 新 AzureMediaServicesAccount 

    例如， `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`。

* 新 AzureMediaServicesKey 

    例如， `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`。

* 移除 AzureMediaServicesAccount 

    例如， `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`。

##<a id="june_changes_13"></a>2013 年 6 月發行

### <a name="june_13_general_changes"></a>Azure 媒體服務變更

此區段中所提及的變更會包含在 2013 年 6 月媒體服務版本中的更新。

* 連結多個儲存帳戶媒體服務帳戶的能力。 

    StorageAccount
    
    Asset.StorageAccountName 和 Asset.StorageAccount

* 更新 Job.Priority 的能力。 

* 通知相關的實體和屬性︰ 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    工作

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure 媒體服務.NET SDK 的變更

下列變更會包含在 2013 年 6 月發行媒體服務 SDK。 最新的媒體服務 SDK 會提供 GitHub。

* 開始使用版本 2.3.0.0，帳戶媒體服務帳戶的連結多個儲存媒體服務 SDK 支援。 下列 Api 支援此功能︰
    
    IStorageAccount 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts 屬性。
    
    StorageAccount 屬性。
    
    StorageAccountName 屬性。
    
    如需詳細資訊，請參閱[管理媒體服務資產跨多個儲存的帳戶]。

* 通知相關的 Api。 您可以聆聽 Azure 佇列中儲存通知的版本 2.2.0.0 開始著手。 如需詳細資訊，請參閱[處理媒體服務工作通知]。
    
    Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions 屬性。
    
    Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 類型。

* Azure 儲存用戶端 SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll) 上的相依性。

* 在 [OData 5.5 (Microsoft.Data.OData.dll) 上的相依性。


##<a id="december_changes_12"></a>2012 年 12 月發行

### <a name="dec_12_dotnet_changes"></a>Azure 媒體服務.NET SDK 的變更

* Intellisense︰ 新增多種類型的遺失 Intellisense 文件。

* Microsoft.Practices.TransientFaultHandling.Core︰ 修正問題 SDK 仍有這個組件的舊版本的相依性。 SDK 現在參照版本 5.1.1209.1 這個組件。

修正在年 11 月 2012 SDK 中找到的問題︰

* IAsset.Locators.Count︰ 此計算會立即正確報告新 IAsset 介面在刪除所有定位器之後。

* IAssetFile.ContentFileSize︰ 此值現在已正確設定上傳後，IAssetFile.Upload(filepath)。

* IAssetFile.ContentFileSize︰ 此屬性可以立即設定時建立檔案的資產。 是先前唯讀狀態。

* IAssetFile.Upload(filepath)︰ 修正問題，這個同步的上傳的方法擲回下列錯誤時將多個檔案上傳到資產。 錯誤是 「 無法驗證要求的伺服器。 請確定授權標頭的值的正確正確包括簽章。 」

* IAssetFile.UploadAsync︰ 修正問題，可能會同時上傳不超過 5 的檔案。

* IAssetFile.UploadProgressChanged: SDK 現在提供此事件。

* IAssetFile.DownloadAsync （字串，BlobTransferClient，ILocator，CancellationToken）︰ 現在提供此方法超量的狀況。

* IAssetFile.DownloadAsync︰ 修正問題位置不超過 5 下載檔案，可能會同時。

* IAssetFile.Delete()︰ 修正問題位置呼叫刪除可能引發例外狀況如果 IAssetFile 的上傳檔案的時間。

* 工作︰ 修復問題位置鏈結 」 MP4 平滑資料流工作 」 與 「 PlayReady 保護工作 」 使用的工作範本會建立任何任務。

* EncryptionUtils.GetCertificateFromStore()︰ 這個方法不再會擲回 null 參考例外狀況失敗，尋找憑證組態問題所根據的憑證。

##<a id="november_changes_12"></a>2012 年 11 月發行

此區段中所提及的變更已更新年 11 月 2012 （版本 2.0.0.0） 中包含 SDK。 這些變更可能需要為 2012 年 6 月預覽 SDK 放開修改或重新撰寫的任何程式碼。

* 資產
    
    IAsset.Create(assetName) 是只能資產建立函數。 IAsset.Create 無法再上傳檔案做為呼叫的一部分。 使用 IAssetFile 上傳。
    
    IAsset.Publish 方法和 AssetState.Publish 列舉值已經移除，從服務 SDK。 任何程式碼，必須使用此值必須重新撰寫。

* FileInfo

    具有已移除此課程，然後 IAssetFile 被取代。

    IAssetFiles

    IAssetFile 取代 FileInfo，而且有不同的行為。 若要使用它，產生 IAssetFiles 物件，後面接著請使用 [媒體服務 SDK 或 Azure 儲存體 SDK 檔案上傳。 您可以使用下列 IAssetFile.Upload 多載︰

    * IAssetFile.Upload(filePath): 同步方法會封鎖執行緒，並將單一檔案上傳時才建議使用。
    
    * 檔案路徑、 blobTransferClient、 定位器 (cancellationToken） IAssetFile.UploadAsync︰ 非同步的方法。 這是慣用的上傳機制。 

    已知的問題︰ 使用 cancellationToken 確實會取消上傳。不過，可以是任何狀態的數字的取消狀態的工作。 您必須正確地攔截，並處理的例外狀況。

* 定位器
    
    已移除特定原點的版本。 SA 特定內容。已遭取代，或依 GA.移除，就會標示 Locators.CreateSasLocator （資產，accessPolicy） 請參閱更新行為的 [定位器] 區段底下的新功能。


##<a id="june_changes_12"></a>2012 年 6 月的預覽版本

下列功能已年 11 月版本中的新 SDK。

* 刪除項目

    IAsset、 IAssetFile、 ILocator IAccessPolicy，IContentKey 物件會立即刪除在物件層級，亦即 IObject.Delete()，而不需要的集合，是 cloudMediaContext.ObjCollection.Delete(objInstance) 中刪除。

* 定位器

    定位器必須現在建立使用 CreateLocator 方法和 LocatorType.SAS 或 LocatorType.OnDemandOrigin 列舉值作為您想要建立特定類型的定位的引數。

    輕鬆取得您內容的可用 Uri 定位器加入新的屬性。 此重新設計的定位器已是用來未來的協力廠商擴充提供更多的彈性，並增加輕鬆使用媒體用戶端應用程式。

* 非同步方法支援

    非同步支援已新增至所有的方法。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure 媒體服務 MSDN 論壇]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure 媒體服務 REST API 參考]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[媒體服務定價詳細資料]: http://azure.microsoft.com/pricing/details/media-services/
[輸入的中繼資料]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[輸出中繼資料]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[將內容]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[將媒體檔案與 Azure 媒體索引編製索引]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Azure 媒體服務使用 Live 串流]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[使用 AES 128 動態加密和金鑰遞送服務]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[使用 PlayReady 動態加密和授權服務]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[媒體服務 PlayReady 授權範本概觀]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[串流儲存加密內容]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[動態包裝]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin 部落格]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[保護與 PlayReady 平滑串流]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[重試邏輯媒體服務 SDK.NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[草闊谷宣告 EDIUS 7 串流到雲端]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[控制媒體服務編碼器輸出檔案名稱]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[建立覆疊]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[訂視訊的區段]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure 媒體服務.NET SDK 3.0.0.1 和 3.0.0.2 發行]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory 存取控制服務 (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[連線到媒體服務與媒體服務 SDK.NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure 媒體服務.NET SDK 擴充功能]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure sdk 工具]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[管理媒體服務資產跨多個儲存帳戶]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[處理媒體服務工作通知]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
