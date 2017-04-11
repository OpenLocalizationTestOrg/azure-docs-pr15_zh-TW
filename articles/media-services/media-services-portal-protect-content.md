<properties 
    pageTitle="設定使用 Azure 入口網站的內容保護原則 |Microsoft Azure" 
    description="本文將示範如何使用 Azure 入口網站設定內容保護原則。 本文也會顯示如何啟用您的資產的動態加密。" 
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
    ms.date="10/24/2016"    
    ms.author="juliako"/>

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>設定使用 Azure 入口網站的內容保護原則

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="overview"></a>概觀

Microsoft Azure 媒體服務 （反飛彈系統） 可讓您以保護您的媒體離開您的電腦儲存、 處理及傳送的時間。 媒體服務可讓您發表內容加密動態與進階加密標準 (AES) （使用的是 128 位元加密金鑰），使用 PlayReady 及/或 Widevine DRM 和 Apple FairPlay 一般加密 (CENC)。 

反飛彈系統提供進行 DRM 授權的服務並 AES 清除授權用戶端的按鍵。 Azure 入口網站可讓您建立一份針對所有類型的加密**金鑰授權授權原則**。

本文將示範如何使用 Azure 入口網站設定內容保護原則。 本文也會顯示如何將動態加密套用至您的資產。

> [AZURE.NOTE]  如果您是使用 Azure 傳統入口網站建立保護原則，原則可能不會出現在[Azure 入口網站](https://portal.azure.com/)。 但是，所有舊原則仍然存在。 您可以檢查其使用 Azure 媒體服務.NET SDK 或[Azure 媒體服務-總管](https://github.com/Azure/Azure-Media-Services-Explorer/releases)工具 (若要查看的原則，用滑鼠右鍵按一下資產]-> [顯示資訊 (F4)]-> [內容索引鍵] 索引標籤上的 [按一下]-> 中的，按一下 [索引鍵)。 
> 
> 如果您想要加密資產使用新的原則，設定 Azure 入口網站，按一下 [儲存] 並重新套用動態加密。 

## <a name="start-configuring-content-protection"></a>開始設定內容的保護

若要開始設定內容保護使用入口網站，全域反飛彈系統帳戶，執行下列動作︰

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 選取 [**設定** > **內容保護**。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>金鑰/授權授權原則

反飛彈系統支援多種方式可以驗證使用者鍵或授權要求。 內容索引鍵的授權原則必須由您與符合您的金鑰/授權至 delived 用戶端的用戶端。 內容索引鍵的授權原則可能會有一或多個授權限制︰**開啟**或**token**限制。

Azure 入口網站可讓您建立一份針對所有類型的加密**金鑰授權授權原則**。

###<a name="open"></a>開啟 

開啟限制表示系統會進行索引鍵，可讓您的主要要求的任何人。 這項限制可能很適合用於測試之用。 

### <a name="token"></a>權杖

權杖限制的原則必須伴隨的權杖發出的安全性 Token 服務 (STS)。 媒體服務支援的簡單 Web 權杖 (SWT) 格式和 JSON Web Token (JWT) 格式的權杖。 媒體服務並不提供安全性權杖服務。 您可以建立自訂的 STS，或利用 Microsoft Azure ACS 這些問題權杖。 若要建立的權杖簽署您指定的權杖限制設定指定索引鍵和問題宣告必須設定 STS。 媒體服務索引鍵的遞送服務會傳回要求的索引鍵 （或授權） 如果權杖是有效的用戶端與索引鍵 （或授權） 的權杖符合這些設定中的宣告。

當設定權杖限制原則時，您必須指定驗證主索引鍵、 發行者和對象參數。 驗證主索引鍵包含索引鍵的權杖的簽名，發行者不安全的權杖服務的權杖中發生的問題。 （有時稱為範圍） 的對象說明的權杖或資源的權杖授與存取權。 媒體服務索引鍵的遞送服務會驗證這些權杖中的值相符的範本中的值。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>PlayReady 權限範本

如需 PlayReady 權限範本的詳細資訊，請參閱[媒體服務 PlayReady 授權範本概觀](media-services-playready-license-template-overview.md)。

### <a name="non-persistent"></a>非常設

如果您設定為非持續的授權，它只保存在記憶體時播放程式使用的授權。  

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>持續性

如果您設定為持續的授權，在用戶端上會儲存在持續存放區。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Widevine 權限範本

如需 Widevine 權限範本的詳細資訊，請參閱[Widevine 授權範本概觀](media-services-widevine-license-template-overview.md)。

### <a name="basic"></a>基本

選取 [**基本**]，將會建立範本，與預設值的值。

### <a name="advanced"></a>進階

如需詳細瞭解 Widevine 設定的進階選項的詳細說明，請參閱[本](media-services-widevine-license-template-overview.md)主題。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 設定

若要啟用 FairPlay 加密，您需要提供的應用程式憑證和應用程式密碼鍵 （詢問） 透過 FairPlay 設定選項。 如需 FairPlay 設定與需求的詳細資訊，請參閱[本文](media-services-protect-hls-with-fairplay.md)。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>套用至您的資產的動態加密

若要充分利用動態加密，必須執行下列動作︰

- 將一組調整位元率 MP4 檔案編碼來源檔案。
- 取得您要傳送您的內容串流端點至少有一個視串流單位。 如需詳細資訊，請參閱[如何不按比例縮放點播串流保留的單位](media-services-portal-manage-streaming-endpoints.md)。

### <a name="select-an-asset-that-you-want-to-encrypt"></a>選取您想要加密的資產

若要查看您所有的資產，選取 [**設定** > **資產**。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>以 AES 或 DRM 加密

當您按**加密**資產上時，您會看到兩個選擇︰ **AES**或**DRM**。 

#### <a name="aes"></a>AES

AES 清除加密會啟用所有串流通訊協定︰ 平滑串流 HLS 與 MPEG 虛線。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

當您選取 [DRM] 索引標籤時，您會看到的內容保護原則不同的選擇 （由您必須現在已設定） + 串流通訊協定一組。

- **PlayReady 和 Widevine MPEG 破折號**-會動態加密 PlayReady 與 Widevine DRMs 您 MPEG 虛線資料流。
- **PlayReady 和使用 MPEG 虛線 + 與 HLS FairPlay Widevine** -會動態加密您 PlayReady 與 Widevine DRMs MPEG 虛線資料流。 也會加密 FairPlay 與您 HLS 資料流。
- **只帶有平滑串流、 與 HLS MPEG 虛線 PlayReady** -會動態加密平滑串流、 HLS MPEG 虛線資料流時，使用 PlayReady DRM。
- **只使用 MPEG 虛線 Widevine** -動態加密您 MPEG-虛線與 Widevine DRM。
- **只使用 HLS FairPlay** -會動態加密 FairPlay 與您 HLS 資料流。

若要啟用 FairPlay 加密，必須先透過 FairPlay 設定選項的內容保護設定刀提供的應用程式憑證和應用程式密碼鍵 （詢問）。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection009.png)

加密選取範圍後，按 [**套用]**。

##<a name="next-steps"></a>後續步驟

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





