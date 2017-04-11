<properties 
    pageTitle="保護內容概觀 |Microsoft Azure" 
    description="本文提供內容保護與媒體服務的概觀。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="juliako"/>

#<a name="protecting-content-overview"></a>保護內容的概觀


Microsoft Azure 媒體服務可讓您以保護您的媒體離開您的電腦儲存、 處理及傳送的時間。 媒體服務可讓您進行即時與視內容與進階加密標準 (AES) （使用的是 128 位元加密金鑰） 或任何主要 DRMs 動態加密︰ Microsoft PlayReady、 Google Widevine 和 Apple FairPlay。 媒體服務也會提供的服務進行 AES 索引鍵和 DRM 授權用戶端 （PlayReady、 Widevine 和 FairPlay） 的授權。 

下圖示範反飛彈系統支援的內容保護工作流程。 

![使用 PlayReady 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]若要使用動態加密，您必須先在您要傳送加密的內容串流串流的端點上取得至少有一個串流保留的單位。

本主題說明[概念與術語](media-services-content-protection-overview.md)相關了解內容保護與反飛彈系統。 主題也包含[連結](media-services-content-protection-overview.md#common-scenarios)主題，顯示如何達成內容保護的工作。 

##<a name="dynamic-encryption"></a>動態加密

Microsoft Azure 媒體服務可讓您傳送您的內容以清除金鑰 AES 或 DRM 加密動態加密︰ Microsoft PlayReady、 Google Widevine 和 Apple FairPlay。

目前，您可以將下列串流格式的加密︰ HLS、 MPEG 虛線，以及帶有平滑串流。 您無法加密 HDS 串流格式]，或循序下載。

如果您想要加密資產的媒體服務，您需要加密金鑰 （CommonEncryption 或 EnvelopeEncryption） 與您的資產也設定金鑰的授權原則。

您也需要設定資產的傳遞原則。 如果您想要將串流儲存空間的加密的資產，請確定，指定您要以設定資產傳遞原則的方式。

要求資料流時，播放程式，媒體服務會使用指定的按鍵來動態加密您使用 AES 清除重要的內容或 DRM 加密。 若要解密串流，播放程式會從主要傳遞服務要求索引鍵。 若要決定以取得金鑰授權的使用者，服務會評估您指定的金鑰的授權原則。

>[AZURE.NOTE]若要利用動態加密，必須先至少有一個視串流單位取得串流端點從您打算傳送加密的內容。 如需詳細資訊，請參閱[如何比例媒體服務](media-services-portal-manage-streaming-endpoints.md)。

##<a name="storage-encryption"></a>儲存加密

使用加密本機使用 AES 256 元加密清除內容，然後上傳至 Azure 儲存體儲存位置加密其餘儲存加密。 受到保護的儲存空間加密的資產會自動加密放在之前編碼，加密的檔案系統中和您也可以選擇重新加密之前上傳回到為新的輸出資產。 儲存加密的主要的使用情況時，您要保護高品質輸入的媒體檔案與在其他加密磁碟上。

才能進行儲存加密的資產，您必須設定資產的傳遞原則，讓媒體服務可讓您知道您要傳送您的內容的方式。 可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則 （例如，AES、 一般加密或不加密） 的內容。

## <a name="common-encryption-cenc"></a>一般加密 (CENC)

加密與 PlayReady 內容時，會使用一般加密或 / 和 Widewine。

## <a name="using-cbcs-aapl-encryption"></a>使用 cbcs aapl 加密

Cbcs aapl 是用來加密與 FairPlay 內容。

## <a name="envelope-encryption"></a>信封加密 

如果您想要使用 AES 128 清除金鑰保護您的內容，請使用這個選項。 若要更安全的選項，請選擇其中一個 DRMs 本主題中所列。 

##<a name="licenses-and-keys-delivery-service"></a>授權與索引鍵的遞送服務

媒體服務提供進行 DRM (PlayReady，Widevine，FairPlay) 授權的服務並 AES 清除授權用戶端的按鍵。 您可以使用[Azure 入口網站](media-services-portal-protect-content.md)、 REST API 或.NET 媒體服務 SDK 設定您的授權和金鑰的授權和驗證原則。

##<a name="token-restriction"></a>權杖限制

內容索引鍵的授權原則可能會有一或多個授權限制︰ 開啟或權杖限制。 權杖限制的原則必須伴隨的權杖發出的安全性 Token 服務 (STS)。 媒體服務支援的簡單 Web 權杖 (SWT) 格式和 JSON Web Token (JWT) 格式的權杖。 媒體服務並不提供安全性權杖服務。 您可以建立自訂的 STS，或利用 Microsoft Azure ACS 這些問題權杖。 若要建立的權杖簽署您指定的權杖限制設定指定索引鍵和問題宣告必須設定 STS。 媒體服務索引鍵的遞送服務會傳回要求的索引鍵 （或授權） 如果權杖是有效的用戶端與索引鍵 （或授權） 的權杖符合這些設定中的宣告。

當設定權杖限制原則時，您必須指定驗證主索引鍵、 發行者及簡報對象參數值。 驗證主索引鍵包含索引鍵的權杖的簽名，發行者不安全的權杖服務的權杖中發生的問題。 （有時稱為範圍） 的對象說明的權杖或資源的權杖授與存取權。 媒體服務索引鍵的遞送服務會驗證這些權杖中的值相符的範本中的值。

##<a name="streaming-urls"></a>串流 Url

如果您的資產使用一個以上的 DRM 加密，您應該使用加密標籤串流的 URL: (格式 ='m3u8-aapl' 加密 = '上述 」)。

下列事項︰

- 可指定只有零或一個加密類型。
- 如果只有一個加密已套用至資產指定 url 中沒有加密類型。
- 加密類型是不區分大小寫。
- 您可以指定下列的加密類型︰  
    - **cenc**︰ 一般加密 （Playready 或 Widevine）
    - **cbcs aapl**: Fairplay
    - **cbc**: AES 信封加密。

##<a name="common-scenarios"></a>常見的案例

下列主題示範如何以保護儲存區中的內容、 發表動態加密資料流媒體，請使用反飛彈系統金鑰/授權服務

- [使用 AES 保護](media-services-protect-with-aes128.md) 
- [使用 PlayReady 及/或 Widevine 保護](media-services-protect-with-drm.md)
- [使用 Apple FairPlay 及/或 PlayReady 您 HLS 內容受保護的串流](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>其他的分析藍本

- [如何將整合 Azure PlayReady 授權服務，與您自己的加密子/串流伺服器](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)。
- [使用 castLabs Azure 媒體服務進行 DRM 授權](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>相關的連結

[宣佈 PlayReady 使用的服務且 AES 動態加密與 Azure 媒體服務](http://mingfeiy.com/playready)

[Azure 媒體服務 PlayReady 授權傳遞價格說明](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[如何偵錯 aes 加密中 Azure 媒體服務串流](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT 權杖 authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[整合 Azure 媒體服務 OWIN MVC 應用程式與 Azure Active Directory 與限制內容索引鍵的傳遞根據 JWT 宣告](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[使用 Azure ACS 問題權杖](http://mingfeiy.com/acs-with-key-services)。

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
