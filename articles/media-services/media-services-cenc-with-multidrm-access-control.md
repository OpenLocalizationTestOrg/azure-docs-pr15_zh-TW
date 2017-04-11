<properties 
    pageTitle="使用多重 DRM 和存取控制 CENC︰ 的參照設計及實作 Azure 和 Azure 媒體服務 |Microsoft Azure" 
    description="進一步瞭解如何以授權此 Microsoft® 平滑串流用戶端移轉套件。" 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>使用多重 DRM 和存取控制 CENC︰ 的參照設計及實作 Azure 和 Azure 媒體服務

##<a name="key-words"></a>關鍵字
 
Azure Active Directory Azure 媒體服務，Azure Media Player 動態加密授權傳遞 PlayReady、 Widevine、 FairPlay、 常見 Encryption(CENC)、 多重 DRM、 Axinom、 虛線、 EME、 MSE、 JSON Web Token (JWT)、 宣告、 現代化的瀏覽器、 鍵變換、 對稱金鑰、 非對稱金鑰、 OpenID 連線，X509 憑證。 

##<a name="in-this-article"></a>本文中

本文涵蓋以下主題︰

- [簡介](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [這份文件的概觀](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [參照設計](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [將設計對應至實作技術](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [實作](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [實作程序](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [在實作一些陷阱](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [實作其他主題](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP 或 HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [登入金鑰變換的 azure Active Directory](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [在哪裡存取權杖？](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [應該做什麼 Live 串流嗎？](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [Azure 媒體服務以外的授權伺服器呢？](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [如果我想要使用自訂的 STS 嗎？](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [完成的系統和測試](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [使用者登入](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [使用 PlayReady 加密的媒體副檔名](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [使用 Widevine EME](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [不具備資格的使用者](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [執行自訂安全的權杖服務](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [摘要](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>簡介

因此知名很複雜的工作，來設計和建立 OTT DRM 子系統或線上串流解決方案。 而且外包特殊 DRM 服務提供者的這個部分運算子/線上視訊的提供者的一般做法。 這份文件的目標是要展示的參照設計及實作的端對端 DRM 子系統 OTT 或線上串流的方案中。

這份文件的目標的讀者是工程師 OTT 或線上串流/多重-screen 解決方案，或任何讀者感興趣的 DRM 子系統 DRM 子系統中的工作。 假設是讀者很熟悉至少一個市場，例如 PlayReady、 Widevine、 FairPlay 或 Adobe 存取上 DRM 技術。

依 DRM，我們也包含 CENC （一般加密） 使用多重 DRM。 主要趨勢線上串流和 OTT 產業中的是使用 CENC 多重-native-DRM 各種不同的用戶端平台上使用的是從各種不同的用戶端平台的使用單一 DRM 和其 SDK 的用戶端的上一個趨勢 shift。 時使用多 native DRM CENC，請同時 PlayReady 和 Widevine 會根據[一般加密 (ISO IEC 23001 7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)規格加密。

使用多重 DRM CENC 優點是，如下所示︰

1. 降低成本因為單一加密處理用針對其原生 DRMs; 與其他平台的加密
1. 降低的管理加密的資產，因為只加密資產的單一複本，需要;
1. 排除 DRM 授權成本，因為原生 DRM 用戶端通常是免費的原生的平台上的用戶端。

Microsoft 已與一些主要產業玩家作用中的 promoter 破折號和 CENC。 Microsoft Azure 媒體服務具有已提供支援破折號和 CENC。 最近的相關公告，請參閱 Mingfei 的部落格︰ [Azure 媒體服務中的宣佈使用 Google Widevine 授權傳送服務](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)和[Azure 媒體服務會將 Google Widevine 包裝，可將多 DRM 資料流](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)。  

### <a name="overview-of-this-article"></a>這份文件的概觀

本文的目標包括下列各項︰

1. 提供 DRM 子系統 CENC 使用多重 DRM; 參照設計
1. 在 Microsoft Azure/Azure 媒體服務平台; 提供參考實作
1. 將告訴您一些設計及實作主題。

在文章中，「 多重 DRM 」 涵蓋下列項目︰

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay （尚未支援 Azure 媒體服務）

下表摘要列出的原生的平台/原生應用程式及每個 DRM 所支援的瀏覽器。

**用戶端的平台**|**原生 DRM 支援**|**在瀏覽器/應用程式**|**串流格式**
----|------|----|----
**智慧電視、 運算子盒 OTT 盒**|PlayReady 主要，及/或 Widevine，及/或其他|Linux Opera、 WebKit，其他|各種不同的格式
**在 Windows 10 裝置 （Windows 電腦上、 Windows 平板電腦、 Windows Phone、 Xbox）**|PlayReady|MS 邊緣/IE11/EME<br/><br/><br/>UWP|虛線 （如 HLS、 PlayReady 不支援時）<br/><br/>虛線、 平滑串流 （如 HLS、 PlayReady 不支援時） 
**Android 的裝置 （電話、 平板電腦電視）**|Widevine|Chrome/EME|虛線
**iOS （iPhone、 iPad）、 OS X 用戶端和 Apple 電視**|FairPlay|Safari 8 + / EME|HLS
**外掛程式︰ Adobe 寶貴時間**|寶貴時間存取|在瀏覽器增益集|HDS HLS

目前狀態的每個 DRM 的部署，因為服務通常會想要實作 2 或 3 DRMs，請確定您的最佳方式處理所有類型的結束點。

有複雜的服務邏輯和複雜度達到特定的層級的各種不同的用戶端上的使用者體驗的用戶端之間折衷。

若要讓您的選取範圍，請謹記下列重點︰

- 在每個 Windows 裝置中，在部分 Android 的裝置，並可透過軟體 Sdk 幾乎任何平台上使用原生實作 PlayReady
- 在每個 Android 裝置、 Chrome、 和一些其他裝置中，將這些實作 Widevine
- 使用僅在 iOS 和 Mac OS 用戶端，或透過 iTunes FairPlay。

因此一般多重 DRM:

- 選項 1: PlayReady 和 Widevine
- 選項 2: PlayReady、 Widevine 和 FairPlay


## <a name="a-reference-design"></a>參照設計

在此區段中，我們會提供的參照設計是以用來實作技術。

DRM 子系統可能會包含下列元件︰

1. 金鑰管理
1. DRM 包裝
1. DRM 授權傳遞
1. 授權的核取
1. 驗證和授權
1. 播放程式
1. 原點/CDN

下圖說明 DRM 子系統中的元件的高層級互動。

![使用 CENC DRM 子系統](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
設計中有三個基本 」 層級 」:

1. 這不外部，公開 （以黑色） 的辦公室圖層
1. 包含所有對公用; 結束點 」 DMZ 」 層 （藍色）
1. 公用網際網路階層 （淺藍色） 包含公用網際網路上的 CDN 和流量的播放程式。
 
應該控制 DRM 保護的內容管理工具，無論它是靜態和動態加密。 DRM 加密的輸入應該包括︰

1. MBR 視訊內容。
1. 內容索引鍵。
1. 授權擷取 Url。

在播放時間高層級的流程是︰

1. 驗證使用者;
1. 授權權杖會自動建立使用者。
1. 受保護的 DRM 內容 （資訊清單） 下載到播放程式。
1. 播放程式送出與索引鍵的識別碼和授權的授權伺服器授權擷取要求 token。

前移動至下一個主題相關的金鑰管理設計的幾個字。

**ContentKey – 資產**|**案例**
------|---------------------------
1-1|最簡單的大小寫。 它會提供最優秀的控制項。 不過，這通常會產生的最高的授權傳遞成本。 在 [最小值的一個授權要求是必要的每一個受保護的資產。
1-多|您可以使用相同的內容索引鍵的多個資產。 例如，內容類型或子集內容類型 （或影片基因） 等邏輯群組中的資產，您可以使用單一的內容索引鍵。
多 – 1|每個資產必須要有多個內容索引鍵。 <br/><br/>例如，如果您要套用的多重-DRM MPEG 破折號的動態 CENC 保護與 HLS 動態 AES 128 加密，您會需要兩個不同內容索引鍵，各有其專屬 ContentKeyType。 (動態 CENC 保護所用的內容鍵，ContentKeyType.CommonEncryption 應該使用動態 AES 128 加密所使用的內容索引鍵的同時，ContentKeyType.EnvelopeEncryption 應該用於。)<br/><br/>另一個範例中，在虛線中的內容，其中一個內容索引鍵可以用來保護視訊及其他內容的金鑰來保護音訊串流的理論 CENC 保護。 
許多 – 到-許多|上述兩個案例的組合︰ 針對相同的資產 「 群組 」 的多個資產的各個使用的內容索引鍵的一組。


另一個要考慮的重要因素，是使用常設和非持續授權。

為什麼有這些考量重要？ 

如果您使用公用雲端授權傳遞，他們擁有授權傳遞成本直接的影響。 現在就讓我們請考慮下列兩個不同的設計案例說明︰



1. 按月訂閱︰ 使用常設授權及 1 對多內容索引鍵的資產對應。 例如︰ 若是所有們過影片，我們使用單一內容加密金鑰。 在此例中︰ 

    總 # 授權要求所有們過影片/裝置 = 1

1. 按月訂閱︰ 使用非持續的授權及 1-1 對應內容索引鍵與資產之間。 在此例中︰

    總 # 授權要求所有們過影片/裝置 = [監看之 # 影片] x [[# 工作階段]

您可以輕鬆地看到兩個不同的設計產生不同的授權要求圖樣因此授權傳遞成本如果授權服務由公用的雲端，例如 Azure 媒體服務。

## <a name="mapping-design-to-technology-for-implementation"></a>將設計對應至實作技術

接下來，我們對應我們一般設計到 Microsoft Azure/Azure 媒體服務的平台上的技術來指定要使用的每個建置組塊的技術。

下表顯示對應︰

**建置組塊**|**技術**
------|-------
**播放程式**|[Azure 媒體播放程式](https://azure.microsoft.com/services/media-services/media-player/)
**身分識別提供者 (IDP)**|Azure Active Directory
**安全性權杖服務 (STS)**|Azure Active Directory
**DRM 保護的工作流程**|Azure 媒體服務動態保護
**DRM 授權傳遞**|1.azure 媒體服務授權傳遞 (PlayReady，Widevine，FairPlay)，或 <br/>2.Axinom 授權伺服器， <br/>3.自訂 PlayReady 授權伺服器
**原點**|Azure 媒體服務串流端點
**金鑰管理**|不需要的參考實作
**內容管理**|C# 主控台應用程式

換句話說，身分識別提供者 (IDP) 和安全性 Token 服務 (STS) 都將會 Azure AD。 播放程式中，我們將使用[Azure 媒體播放程式 API](http://amp.azure.net/libs/amp/latest/docs/)。 Azure 媒體服務和 Azure Media Player 支援破折號和 CENC 多重 DRM。

下圖顯示的整體的結構及使用上述技術對應流程。

![反飛彈系統上 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

若要設定動態 CENC 加密，內容管理工具會使用下列輸入︰

1. 開啟內容。
1. 內容索引鍵產生/管理;
1. 授權擷取 Url;
1. 從 Azure AD 資訊的清單。

內容管理工具的輸出會是︰

1. ContentKeyAuthorizationPolicy 包含在授權傳遞會驗證 JWT 權杖與 DRM 授權規格; 規格
1. 包含串流格式、 DRM 保護以及授權擷取 Url 的規格 AssetDeliveryPolicy。

在執行階段流程做為如下︰

1. 在 [使用者驗證]，則會產生 JWT 權杖;
1. 宣告 JWT 權杖中所包含的內容之一是包含 「 EntitledUserGroup 」 的群組物件識別碼的 「 群組 」 宣告。 此宣告會用於傳遞 「 權益核取 」。
1. 播放程式下載用戶端資訊清單的 CENC 受保護的內容，並 」 會看到 「 下列動作︰
    1. 金鑰識別碼， 
    1. 該內容會受到保護 CENC
    1. 授權擷取 Url。

1. 播放程式可讓您根據支援的瀏覽器/DRM 授權擷取要求。 在授權取得邀請中，金鑰識別碼，也會送出的 JWT token。 授權遞送服務會驗證 JWT 權杖，則發出所需的授權之前所包含的宣告。

##<a name="implementation"></a>實作

###<a name="implementation-procedures"></a>實作程序

實作會包含下列步驟︰

1. 準備測試 asset(s)︰ 編碼/套件測試影片以多重位元率分散 MP4 中 Azure 媒體服務。 這項資產不受保護的 DRM。 動態保護稍後會完成 DRM 保護。
1. 建立索引鍵的識別碼和內容 （您也可以從金鑰種子） 鍵。 我們的目的，金鑰管理系統，不需要因為我們正在處理只有一組按鍵 ID 與內容的金鑰的幾個測試資產。
1. 使用反飛彈系統 API 設定的測試資產的多重 DRM 授權傳送服務。 如果您使用自訂的授權伺服器，您的公司或貴公司的廠商，而不是 Azure 媒體服務中的授權服務，您可以略過此步驟，並指定授權擷取 Url 中的步驟，設定授權傳遞。 反飛彈系統 API 需要指定一些詳細的設定，例如授權原則限制、 授權等不同 DRM 授權服務的回覆範本。此時，Azure 入口網站並未尚未提供所需的使用者介面這項設定。 您可以找到 API 層級的資訊及 Julia Kornich 文件中的程式碼的範例︰[使用 PlayReady 及/或 Widevine 動態一般加密](media-services-protect-with-drm.md)。 
1. 使用反飛彈系統 API 設定測試資產的資產傳遞原則。 您可以找到 API 層級的資訊及 Julia Kornich 文件中的程式碼的範例︰[使用 PlayReady 及/或 Widevine 動態一般加密](media-services-protect-with-drm.md)。
1. 建立及設定的 Azure Active Directory 租用戶中 Azure;
1. 建立您的 Azure Active Directory 租用戶中的幾個使用者帳戶和群組︰ 您必須至少建立 「 EntitledUser 」 群組，並將使用者新增至 [此群組。 在這個群組中的使用者會取得授權傳遞權益核取並不在此群組中的使用者將無法通過驗證檢查無法擷取任何授權。 此 「 EntitledUser 」 群組的成員是由 Azure AD JWT 權杖中所需的 「 群組 」 宣告。 設定多重 DRM 授權傳遞步驟應該指定此宣告需求。
1. 建立主控您的視訊播放程式 ASP.NET MVC 應用程式。 此 ASP.NET 應用程式會受保護的 Azure Active Directory 租用戶對使用者驗證。 適當的宣告會包含在使用者驗證後取得存取權杖。 OpenID 連線 API 建議，為此步驟。 您需要安裝下列 NuGet 套件︰
    - 安裝套件 Microsoft.Azure.ActiveDirectory.GraphClient
    - 安裝套件 Microsoft.Owin.Security.OpenIdConnect
    - 安裝套件 Microsoft.Owin.Security.Cookies
    - 安裝套件 Microsoft.Owin.Host.SystemWeb
    - 安裝套件 Microsoft.IdentityModel.Clients.ActiveDirectory
1. 建立使用[Azure 媒體播放程式 API](http://amp.azure.net/libs/amp/latest/docs/)播放程式。 [Azure Media Player 的 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/)可讓您指定不同的 DRM 平台上使用哪種 DRM 技術。
1. 測試矩陣︰

**DRM**|**在瀏覽器**|**具備資格的使用者的結果**|**取消具備資格的使用者的結果**
---|---|-----|---------
**PlayReady**|MS 邊緣或 Windows 10 上 IE11|成功|會失敗。
**Widevine**|在 Windows 10 上的 chrome|成功|會失敗。
**FairPlay** |TBD||

喬治 Trifonov 的 Azure 媒體服務小組所撰寫的部落格提供詳細的步驟，設定 ASP.NET MVC 播放程式應用程式的 Azure Active Directory︰[整合 Azure 媒體服務 OWIN MVC 應用程式與 Azure Active Directory 與限制內容索引鍵的傳遞根據 JWT 宣告](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

喬治也有[JWT Azure 媒體服務和動態加密的權杖驗證](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)在撰寫部落格。 以下是他[Azure AD 整合 Azure 媒體服務主要傳遞的範例](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/)。

如需的 Azure Active Directory 資訊︰

- 您可以尋找[Azure Active Directory 開發人員指南](../active-directory/active-directory-developers-guide.md)開發人員資訊。
- 您可以在 [[管理您的 Azure AD 目錄](../active-directory/active-directory-administer.md)中找到管理員資訊。

### <a name="some-gotchas-in-implementation"></a>在實作一些陷阱

在實作有一些 「 問題 」。 希望 」 陷阱 」 的下列清單可協助您疑難排解萬一遇到問題。

1. **發行者**URL 應該結尾**「 / 」**。  

    **對象**應為播放程式應用程式的用戶端識別碼，您也應該發行者 URL 的結尾新增**「 / 」** 。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    在[JWT 解碼器](http://jwt.calebb.net/)，您應該會看到**aud**和**iss**為下方 JWT 權杖中︰
    
    ![第 1 豪](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. 將 AAD （用的應用程式的設定] 索引標籤） 上的應用程式中的權限。 這是必要的每個應用程式 （本機及部署版本）。

    ![第 2 豪](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. 使用正確的發行者在動態 CENC 保護設定︰

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    以下將無法運作︰
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    GUID 是 AAD 租用戶識別碼。 GUID 在結束點快顯功能表中找 Azure 入口網站。

4. 授與群組成員資格宣告權限。 請確定 AAD 應用程式資訊清單檔案中，我們有下列

    「 groupMembershipClaims 」: 「 所有 」，（預設值為 null）

5. 建立限制需求時，請設定適當 TokenType。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    自新增支援的 JWT (AAD) 除了 SWT (ACS)，預設 TokenType 為 TokenType.JWT。 如果您是使用 SWT/ACS，您必須設定為 TokenType.SWT。

## <a name="additional-topics-for-implementation"></a>實作其他主題
接下來我們將光碟對一些額外的主題，在我們的設計及實作。

###<a name="http-or-https"></a>HTTP 或 HTTPS 嗎？

我們內建的 ASP.NET MVC 播放程式應用程式必須支援下列功能︰

1. 透過 Azure AD 會用來在 HTTPS; 底下的 [使用者驗證
1. JWT 權杖 exchange 用戶端和 Azure AD 會用來在 HTTPS; 之間
1. 取得所需遭到 HTTPS，如果授權傳遞提供 Azure 媒體服務的用戶端的 DRM 授權。 當然，PlayReady 產品套件不會強制 HTTPS 授權傳遞。 如果您的 PlayReady 授權伺服器超出 Azure 媒體服務時，可以用 HTTP 或 HTTPS。

因此，ASP.NET 播放程式應用程式會使用 HTTPS 最佳做法。 這表示 Azure 媒體播放程式會在頁面的下 HTTPS。 不過，為了串流我們想 HTTP，因此我們需要考慮混合的內容問題。

1. 在瀏覽器不允許混合的內容。 但平滑的 Silverlight 和 OSMF 外掛程式等虛線外掛程式允許。 混合的內容是安全性顧慮-這是因為潛在威脅的插入惡意 JS 這可能會造成風險的客戶資料的能力。  瀏覽器會封鎖此根據預設，為止，以避免的唯一方式是位於伺服器 （來源），以允許所有網域 （無論 https 或 http）。 這可能是不建議您先表示。
1. 我們應避免混合的內容︰ 同時使用 HTTP 或同時使用 HTTPS。 當播放混合的內容，silverlightSS 技術需要清除混合的內容警告。 flashSS 技術處理混合的內容，但不混合的內容發出警告。
1. 如果您串流端點建立 2014 年 8 月之前，它將不支援 HTTPS。 在此情況下，請建立並使用新的資料流結束點的 HTTPS。

在參照實作中，為 DRM 受保護的內容，應用程式和串流將會在 HTTTPS 之下。 開啟的內容，播放程式不需要驗證或授權，因此您必須使用 HTTP 或 HTTPS 自由。

### <a name="azure-active-directory-signing-key-rollover"></a>登入金鑰變換的 azure Active Directory

這是您實作的考慮很重要的一點。 如果您不認為這個實作中，已完成的系統最後會完全中最多 6 週停止運作。

Azure AD 使用業界標準本身和應用程式使用 Azure AD 之間建立信任。 具體來說，Azure AD 使用公開及私密金鑰組組成的簽章金鑰。 Azure AD 建立包含使用者的相關資訊的安全性權杖，此 token 加簽署時傳送回應用程式之前，請使用其私密金鑰的 Azure AD。 若要驗證的權杖是有效且實際起始從 Azure AD，應用程式必須驗證使用公開金鑰公開 Azure AD 租用戶的同盟中繼資料文件中所包含的權杖的簽章。 此公開金鑰 – 並簽署金鑰衍生出 – 是同一個 Azure AD 中使用適用於所有承租人。

Azure AD 金鑰變換的詳細的資訊，請參閱文件︰ [Azure AD 中登入鍵變換的重要資訊](../active-directory/active-directory-signing-key-rollover.md)。

[公開私密金鑰組](https://login.windows.net/common/discovery/keys/)，之間 

- 私人識別碼是由 Azure Active Directory 用來產生的 JWT 權杖;
- 驗證的 JWT token; 例如反飛彈系統 DRM 授權傳遞服務應用程式使用公開金鑰
 
安全性用途的 Azure Active Directory 旋轉這個憑證定期 （每個 6 週）。 若安全性漏洞，金鑰變換可能會發生任何時間。 因此，需要更新用 Azure AD 旋轉金鑰組，否則反飛彈系統中的權杖驗證將會失敗，並將發出沒有授權的公開金鑰反飛彈系統中的授權傳遞服務。 

這是由設定 DRM 授權傳遞服務時，設定 TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument 達成。

JWT 權杖流程做為如下︰

1.  Azure AD 會發出 JWT 權杖，目前已驗證的使用者; 私密金鑰
2.  時播放程式發現 CENC 與多重 DRM 受保護的內容，它會先找出 Azure AD 發出的 JWT token。
3.  播放程式會與 JWT 權杖授權擷取要求傳送給反飛彈系統; 授權傳遞服務
4.  反飛彈系統中的授權遞送服務會使用從 Azure AD 目前/有效公開金鑰驗證 JWT 權杖，發出授權之前。

從 Azure AD 目前/有效公開金鑰一律會檢查 DRM 授權傳送服務。 公開金鑰由 Azure AD 將會用於驗證 Azure AD 發出的 JWT 權杖的金鑰。

該怎麼辦 AAD 產生的 JWT 權杖，但之前 JWT 權杖會傳送玩家 DRM 授權傳遞的服務反飛彈系統以供驗證後，會發生情況金鑰變換？ 

因為可能隨時復原鍵，很多個有效公開金鑰同盟中繼資料文件中使用。 Azure 媒體服務授權傳遞可以使用任何機碼中的文件中，指定一個按鍵可能推出復原，因為另一個可能會及其取代項目，等等。

### <a name="where-is-the-access-token"></a>在哪裡存取權杖？

如果您看看如何在 web 應用程式呼叫 API 應用程式[與 OAuth 2.0 用戶端認證授與應用程式](active-directory-authentication-scenarios.md#web-application-to-web-api)身分，驗證流程是作為如下︰

1.  使用者登入 web 應用程式中的 Azure AD （請參閱[Web 應用程式的網頁瀏覽器](active-directory-authentication-scenarios.md#web-browser-to-web-application)。
2.  Azure AD 授權端點會將使用者代理程式重新導向至驗證碼與用戶端應用程式。 使用者代理程式傳回的用戶端應用程式重新導向 URI 授權程式碼。
3.  Web 應用程式需要取得存取權杖，使其可以驗證網路 API，並擷取所需的資源。 它 Azure AD token 端點，提供認證、 用戶端識別碼和網路 API 的應用程式識別碼 URI 提出要求。 它會顯示以證明使用者具有同意授權程式碼。
4.  Azure AD 驗證應用程式，並傳回用於呼叫網路 API JWT 存取權杖。
5.  透過 HTTPS，web 應用程式會使用 「 承載者 」 指定的 JWT 字串要求授權頁首中加入網路 API 傳回的 JWT 存取權杖。 網路 API 驗證 JWT 權杖，然後如果驗證成功，會傳回您要的資源。

在此 」 應用程式識別碼 」，網路 API 信任 web 應用程式已驗證的使用者。 因此，此模式稱為受信任的子系統。 [在此頁面上的圖表](http://msdn.microsoft.com/library/azure/dn645542.aspx/)會說明如何授權程式碼授與流程運作。

在授權擷取 token 限制，我們如下所述的相同的信任的子系統模式。 授權服務中 Azure 媒體服務 web API 資源 」 後端資源 」 web 應用程式需要存取。 因此，在哪裡存取權杖？

事實上，我們會取得 Azure AD 存取權杖。 成功使用者驗證後，會傳回授權程式碼。 授權程式碼會再使用與用戶端識別碼和應用程式鍵，以存取權杖用於 exchange。 和存取權杖適用於存取指向 [指標] 應用程式，或代表 Azure 媒體服務授權服務。

我們需要註冊 Azure AD 中設定 「 指標 」 應用程式，遵循下列步驟︰

1.  Azure AD 租用戶中

    - 新增應用程式 （資源） 登入 url: 

    https://[resource_name].azurewebsites.net/ 和 

    - 應用程式識別碼 URL: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]。 
2.  新增新的金鑰資源應用程式。
3.  更新的應用程式資訊清單的檔案，以便 groupMembershipClaims 屬性有以下的值: 「 groupMembershipClaims 」: 「 所有 」  
4.  Azure AD 應用程式，指向 [播放程式 web 應用程式，在 「 其他應用程式權限] 區段中新增已在上述步驟 1 中新增資源應用程式。 在 [委派的權限] 核取 「 Access [resource_name]] 核取記號。 這會提供 web 應用程式建立的權限存取權杖，以存取 [資源] 應用程式。 您應該執行此動作的 web 應用程式的本機及部署版本如果您正在使用 Visual Studio 和 Azure web 應用程式開發。
    
因此，由 Azure AD JWT 權杖是確實存取權杖，以存取此 「 指標 」 資源。

### <a name="what-about-live-streaming"></a>應該做什麼 Live 串流嗎？

在上方，我們討論具有已將焦點放在視資產。 應該做什麼 live 串流嗎？

好是，您可以使用完全相同的設計及實作保護即時資料流中 Azure 媒體服務，藉由將相關聯的程式為 「 VOD 資產 」 的資產。

具體來說，是知名執行即時串流中 Azure 媒體服務，您需要建立頻道，然後程式] 底下的頻道。 若要建立程式，您需要建立資產，其中會包含該程式的即時封存。 以提供 CENC 即時內容的多重 DRM 保護，您需要執行，套用相同設定/處理資產它當成 「 VOD 資產 」 之前您啟動程式。

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Azure 媒體服務以外的授權伺服器呢？

通常，可能會有客戶投資授權伺服器陣列中自己的資料中的其中一項置中或裝載 DRM 服務提供者。 所幸，Azure 媒體服務內容保護 」 可讓您在混合式模式中作業︰ 內容裝載和動態 Azure 媒體服務，在保護時 DRM 授權傳送的外 Azure 媒體服務的伺服器。 在此情況下，有變更的下列事項︰

1. 安全性權杖服務必須議題接受並由授權伺服器陣列，可以驗證的權杖。 例如，提供的 Axinom Widevine 授權伺服器需要特定的 JWT 權杖其中包含 「 權益郵件 」。 因此，您需要有發出這類 JWT token STS。 作者完這類實作，而且您可以尋找下列[Azure 文件中心](https://azure.microsoft.com/documentation/)中的文件詳細資料︰[使用 Axinom 進行 Widevine 授權 Azure 媒體服務](media-services-axinom-integration.md)。 
1. 您不再需要設定 Azure 媒體服務授權服務 (ContentKeyAuthorizationPolicy)。 您需要提供授權擷取 Url （適用於 PlayReady、 Widevine 和 FairPlay） 是當您在設定 CENC 與多重 DRM 設定 AssetDeliveryPolicy。
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>如果我想要使用自訂的 STS 嗎？

可能會有客戶可以選擇使用自訂的 STS （安全性 Token 服務），提供 JWT 權杖的幾個原因。 有些是︰

1.  客戶使用身分識別提供者 (IDP) 不支援 STS。 在此情況下自訂 STS，可能是一個選項。
2.  客戶可能需要更有彈性或更緊密地中的控制項與客戶的訂閱計費系統整合 STS。 例如，MVPD 運算子可能提供多個 OTT 訂閱者套件，例如進階版基本、 運動等。運算子可能會想要符合的權杖訂閱者的套件中的宣告，以便只在右封裝內容提供。 在此情況下，為自訂 STS 所提供的所需的彈性和控制項。

使用自訂的 STS 時需要兩個變更︰

1.  在設定的資產授權服務，必須指定自訂 STS （下方更多詳細資料），而不是從 Azure Active Directory 目前的金鑰以供驗證使用的安全性索引鍵。
2.  安全性金鑰產生的 JTW 權杖時，指定，而不是私密金鑰的 [目前的 X509 憑證 Azure Active Directory 中的。

有兩種類型的安全性機碼︰

1.  對稱金鑰︰ 相同的金鑰用於產生和驗證 JWT 權杖;
2.  非對稱金鑰︰ X509 憑證使用加密/產生的 JWT 權杖和驗證的權杖公開金鑰私密金鑰公用私密金鑰組。

####<a name="tech-note"></a>技術記事

如果您是使用.NET Framework / C# 作為您開發平台上的 [X509 憑證用於非對稱式安全性金鑰必須金鑰長度至少 2048年。 這是類別 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 於.NET Framework 的需求。 否則，下列例外狀況︰

IDX10630︰ 進行簽章 」 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 」 不能小於 '2048 「 位元。 

## <a name="the-completed-system-and-test"></a>完成的系統和測試

我們會逐步執行幾個案例中已完成的端對端系統，讓讀者可以之前登入帳戶有基本的行為的 [圖片]。

播放程式 web 應用程式，以及其登入，請參閱[以下](https://openidconnectweb.azurewebsites.net/)。

如果您需要的是 「 非整合 」 案例︰ 視訊資產裝載中的 [取消保護，或 DRM 保護 Azure 媒體服務，但沒有權杖驗證 (無論發出授權要求其)，測試沒有登入 （依，如果您的視訊資料流是透過 HTTP，請切換至 HTTP）。

如果您的需要端對端整合的案例︰ 視訊資產動態 DRM 保護中 Azure 媒體服務，權杖驗證與 JWT 由 Azure AD 產生的權杖下，您需要登入。

### <a name="user-login"></a>使用者登入

若要測試的端對端整合式的 DRM 系統，必須有一個 「 帳戶 」 建立或新增。 

什麼帳戶？

雖然 Azure 原本允許只能由 Microsoft 帳戶使用者的存取，現在可讓使用者從兩個系統的存取權。 這是完成所有 Azure 屬性信任 Azure AD 進行驗證，不必 Azure AD 驗證組織的使用者，而且建立同盟關係 Azure AD 信任驗證消費者使用者的 Microsoft 帳戶消費者身分識別系統的位置。 如此一來，Azure AD 是可以驗證 「 來賓 」 Microsoft 帳戶，以及 「 原生 」 Azure AD 帳戶。

由於 Azure AD 信任 Microsoft 帳戶 (MSA) 網域，您可以新增從任何下列網域的所有帳戶，以便自訂 Azure AD 租用戶，並使用的帳戶登入︰

**網域名稱**|**網域**
-----------|----------
**自訂 Azure AD 租用戶網域**|somename.onmicrosoft.com
**公司網域**|microsoft.com
**Microsoft 帳戶 (MSA) 網域**|outlook.com、 live.com、 hotmail.com

您可能會連絡任何的作者，有一個建立或新增您的帳戶。 

以下是使用不同的網域帳戶不同的登入頁面的螢幕擷取畫面。

**自訂 Azure AD 租用戶的網域帳戶**︰ 在此情況下，請參閱自訂登入頁面的自訂 Azure AD 租用戶網域。

![自訂 Azure AD 租用戶的網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft 的網域帳戶使用智慧卡登**︰ 在此情況下您看到登入頁面自訂 Microsoft 公司的 IT 與雙因素驗證。

![自訂 Azure AD 租用戶的網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帳戶 (MSA)**: 在此情況下，您看到的 Microsoft 帳戶登入頁面的消費者。

![自訂 Azure AD 租用戶的網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>使用 PlayReady 加密的媒體副檔名

PlayReady 支援使用，例如 IE 11 Windows 8.1 上和和在 Windows 10 上的 Microsoft Edge 瀏覽器在新瀏覽器使用加密媒體副檔名 (EME) PlayReady 會 EME 為基礎的 DRM。

![使用 PlayReady EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

深色的播放程式區域就是視訊的因為用 PlayReady 保護可防止從進行受保護的螢幕擷取畫面。 

下列畫面顯示播放程式外掛程式] 和 [MSE/EME 支援。

![使用 PlayReady EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

在 Microsoft Edge 和 Windows 10 版的 IE 11 EME 可讓支援 Windows 10 裝置上的[PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/)叫用。 PlayReady SL3000 解除鎖定的增強型的進階內容流程 (4 K，首，等) 和新的內容傳遞模型 （增強內容的最早視窗）。

將焦點集中在 Windows 裝置上︰ PlayReady 是在 Windows 裝置 (PlayReady SL3000) 才可使用 HW 只 DRM。 使用 PlayReady 透過 EME 或 UWP 應用程式串流的服務，提供高視訊品質的成品使用大於另一個 DRM PlayReady SL3000。 一般而言，2 K 內容將通過 Chrome 或 Firefox 和 4 K 透過 Microsoft Edge/IE11 或 UWP 應用程式在同一個裝置 （根據服務設定和實作） 上的內容。


#### <a name="using-eme-for-widevine"></a>使用 Widevine EME

新式 EME/Widevine 的支援，例如 Chrome 41 + Windows 10、 Windows 8.1、 Mac OSX Yosemite，以及 Chrome Android 4.4.4，在瀏覽器上 Google Widevine 是前 EME DRM。

![使用 Widevine EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

請注意，Widevine 不會阻止進行螢幕擷取畫面的其中一個受保護的影片。

![使用 Widevine EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>不具備資格的使用者

如果使用者不是 「 資格的使用者] 群組的成員，使用者將無法傳遞 」 權益核取 」，然後多重 DRM 授權服務將拒絕發出要求的授權，如下所示。 詳細的描述是 「 授權擷取失敗 」，這是因為設計。

![取消具備資格的使用者](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>執行自訂安全的權杖服務

執行自訂安全性 Token 服務 (STS) 的案例中，JWT 權杖將發行自訂 STS 使用對稱或非對稱的金鑰。 

使用 （使用 Chrome） 的情況︰

![執行自訂 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

大小寫的使用對稱金鑰，透過 X509 憑證 （使用 Microsoft 現代瀏覽器）。

![執行自訂 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

在這兩個以上的情況下，使用者驗證保持不變 – 透過 Azure AD。 唯一的差別 JWT 權杖由自訂 STS，而不是 Azure AD。 當然，在設定動態 CENC 保護時，授權服務限制指定類型的 JWT 權杖對稱或非對稱鍵。

## <a name="summary"></a>摘要

在此文件中討論 CENC 與多 native DRM 和存取控制項，透過權杖驗證︰ 設計及實作使用 Azure、 Azure 媒體服務和 Azure Media Player。

- 參照設計呈現其中包含所有必要的元件，在 DRM/CENC 子系統中;
- Azure、 Azure 媒體服務和 Azure Media Player 參考實作。
- 直接參與的設計及實作一些主題也會討論。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>通知 

威廉丈 （中國）、 Mingfei Yan、 Roland Le 法郎、 Kilroy Hughes、 Julia Kornich
