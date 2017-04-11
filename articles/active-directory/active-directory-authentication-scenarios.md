
<properties
   pageTitle="Azure ad 驗證案例 |Microsoft Azure"
   description="五個常見驗證案例的 Azure Active Directory (AAD) 概觀"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Azure ad 驗證案例

Azure Active Directory (Azure AD) 簡化驗證適用於開發人員提供的服務]，以支援業界標準通訊協定，例如 OAuth 2.0 和 OpenID 連線，以及開啟 [來源文件庫，可協助您開始撰寫程式碼快速不同的平台的身分識別。 這份文件可協助您瞭解各種案例 Azure AD 支援，並會告訴您如何開始使用。 分成下列各節︰

- [Azure AD 中的驗證的基本概念](#basics-of-authentication-in-azure-ad)

- [Azure AD 安全性權杖中的宣告](#claims-in-azure-ad-security-tokens)

- [Azure AD 中登錄應用程式的基本概念](#basics-of-registering-an-application-in-azure-ad)

- [應用程式類型和案例](#application-types-and-scenarios)

  - [Web 應用程式的網頁瀏覽器](#web-browser-to-web-application)

  - [單一頁面應用程式] 選項](#single-page-application-spa)

  - [網路 API 原生應用程式](#native-application-to-web-api)

  - [網路 API 的 web 應用程式](#web-application-to-web-api)

  - [精靈或 Web API 伺服器應用程式](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Azure AD 中的驗證的基本概念

如果您熟悉的驗證 Azure AD 中的基本概念，閱讀本節。 否則，您可以直接跳到[應用程式類型和案例](#application-types-and-scenarios)。

我們來看看最基本的案例中需要身分識別的位置︰ 網頁瀏覽器中的使用者需要驗證的 web 應用程式。 [網頁瀏覽器 Web 應用程式](#web-browser-to-web-application)] 區段中，將詳細說明這種情況下，但很實用的起始點，以說明 Azure AD 的功能，並概念案例的運作方式。 請考慮下列圖表的這種情況︰

![登入 web 應用程式的概觀](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

注意上方圖表，以下是您需要瞭解的各種元件︰

- Azure AD 是身分識別提供者，負責驗證使用者與組織的目錄中的應用程式的身分識別和最後發出安全性權杖時成功的驗證這些使用者和應用程式。


- 想要外包 Azure AD 驗證的應用程式必須註冊 Azure AD，註冊，並可唯一識別目錄中的應用程式中。


- 開發人員可以使用開啟來源 Azure AD 驗證的文件庫來處理通訊協定詳細資料，讓您輕鬆驗證。 如需詳細資訊，請參閱[Azure Active Directory 驗證文件庫](active-directory-authentication-libraries.md)。


• 使用者驗證後，應用程式必須驗證使用者的安全性權杖，以確保驗證成功的預定的合作對象。 開發人員可以使用控點從 Azure AD，包括 JSON Web 權杖 (JWT) 或 SAML 2.0 任何權杖驗證提供的驗證的文件庫。 如果您想要手動執行驗證，請參閱[JWT Token 處理常式](https://msdn.microsoft.com/library/dn205065.aspx)的文件。


> [AZURE.IMPORTANT] Azure AD 使用公開金鑰登權杖，並請確認正確。 如需有關必要的邏輯您必須以確保該應用程式中的[重要資訊的相關簽章金鑰變換中 Azure AD](active-directory-signing-key-rollover.md)永遠會更新為最新的機碼，請參閱。


• 邀請和回應的驗證程序的流程由使用，例如 OAuth 2.0，OpenID 連線，驗證通訊協定 WS 同盟或 SAML 2.0。 更多詳細資料和以下各節中的[Azure Active Directory 驗證通訊協定](active-directory-authentication-protocols.md)主題討論這些通訊協定。

> [AZURE.NOTE] Azure AD 支援 OAuth 2.0 和大量 OpenID 連線標準使用承載者權杖，包括表示為 JWTs 承載者權杖。 承載者權杖是輕量型安全性權杖受保護的資源 」 承載者] 存取。 因此，「 承載者 」 是可以呈現權杖任何方。 如果所需的步驟不會移至安全的 token 傳輸和儲存空間，必須先派驗證與 Azure AD 接收承載者權杖，雖然是攔截及非預期的廠商所使用。 雖然某些安全性權杖有使用時，防止未經授權的合作對象的內建機制，承載者權杖沒有這個機制，且必須安全通道，例如傳輸層安全性 (HTTPS) 中傳輸。 如果承載者權杖會以清除傳輸，男人增益集攻擊可讓惡意派取得權杖，並用於未經授權存取受保護的資源。 儲存或快取承載者權杖，以供日後使用時，就會套用相同的安全性原則。 確保您的應用程式來傳送，並將承載者權杖儲存安全的方式。 更多承載者權杖上的安全性考量，請參閱[RFC 6750 步驟 5](http://tools.ietf.org/html/rfc6750)。


現在，您有基本概念的概觀，請閱讀以瞭解如何佈建 Azure AD 運作方式，以及常見的案例 Azure AD 支援以下各節。


## <a name="claims-in-azure-ad-security-tokens"></a>Azure AD 安全性權杖中的宣告

Azure AD 發出安全性權杖包含宣告或主旨經過驗證的相關資訊，判斷提示。 這些宣告可讓應用程式的各種工作。 例如，他們可以用來驗證的權杖，找出主旨的目錄租用戶，顯示使用者資訊、 決定主旨的授權，依此類推。 在任何指定的安全性 token 宣告是認證的相依於權杖，用來驗證使用者和此應用程式設定類型的類型。 下表提供每一種宣告 Azure AD 所發出的簡短描述。 如需詳細資訊，請參閱[支援權杖和宣告類型](active-directory-token-and-claims.md)。


| 宣告 | 描述 |
|-------|-------------|
| 應用程式識別碼 | 識別使用權杖應用程式。
| 對象 | 識別權杖適用於收件者的資源。 |
| 應用程式驗證內容類別參考 | 指出用戶端是經過驗證 （公用用戶端與機密的用戶端） 的方式。 |
| 立即驗證 | 日期及時間發生驗證記錄。 |
| 驗證方法 | 指出已驗證的權杖主旨的方式 （密碼、 憑證）。 |
| 名字 | Azure AD 設定為提供的使用者指定的名稱。 |
| 群組 | 包含物件識別碼 Azure AD 使用者群組的成員。 |
| 身分識別提供者 | 記錄驗證的權杖主旨的身分識別提供者。 |
| 在發行 | 記錄的權杖核發，通常用於 token 的有效期限的時間。 |
| 發行者 | 識別 STS 發出權杖，以及 Azure AD 租用戶。 |
| 姓氏 | Azure AD 設定為提供使用者的姓氏。 |
| 名稱 | 提供識別的權杖主旨的人力可讀取值。 |
| 物件識別碼 | Azure AD 中包含不變，唯一識別碼的主旨。 |
| 角色 | 包含授與使用者的 Azure AD 應用程式角色好記的名稱。 |
| 範圍 | 指出用戶端應用程式授予的權限。 |
| 主旨 | 指出哪些權杖判斷提示資訊的本金。 |
| 租用戶識別碼 | 包含目錄租用戶發出權杖不變，唯一識別碼。 |
| 權杖為單位的週期 | 定義權杖是有效的時間間隔。 |
| 使用者主要名稱 | 包含使用者主要名稱的主旨。 |
| 版本 | 包含權杖的版本號碼。 |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Azure AD 中登錄應用程式的基本概念

在目錄中必須註冊 outsources Azure AD 驗證的任何應用程式。 此步驟涉及告知 Azure AD 相關應用程式，包括具有位置，以驗證後，若要找出您的應用程式，以及其他 URI 傳送回覆 URL 的 URL。 這項資訊是必要的幾個重要的原因︰

- Azure AD 需要處理登入，或交換權杖時，應用程式與通訊座標。 這些包括下列各項︰

  - 應用程式識別碼 URI︰ 應用程式識別碼。 此值會傳送至 Azure AD 驗證期間，指出哪些應用程式來電者想用的權杖。 此外，此值會包含在權杖，讓應用程式可讓您知道是預期的目標。


  - 回覆 URL] 與 [重新導向 URI︰ 如果是網頁 API 或 web 應用程式，請回覆 URL 是要 Azure AD 會傳送驗證的回應，包括權杖，如果驗證成功的位置。 若是原生應用程式中，重新導向 URI 是要 Azure AD 會重新導向在 OAuth 2.0 邀請使用者代理程式的唯一識別碼。


  - 用戶端識別碼︰ Azure AD 應用程式已註冊時產生的應用程式識別碼。 當要求授權程式碼或權杖，用戶端識別碼和鍵會傳送到 Azure AD 驗證期間中。


  - 金鑰︰ 傳送的用戶端識別碼以及驗證時 Azure ad 撥打網路 API 金鑰。


- Azure AD 必須確定應用程式具有必要的權限來存取您的目錄資料，貴組織中的其他應用程式

佈建更清晰了解有兩種應用程式，可以開發和 Azure AD 與整合類別︰

- 單一租用戶應用程式︰ 單一租用戶應用程式適用於一個組織中。 這些是由企業開發人員撰寫通常線條的商務 (LoB) 應用程式。 在單一租用戶應用程式只需要在單一的目錄中的使用者存取，如此一來，它只需要在一個目錄佈建。 這些應用程式通常是由組織中的開發註冊。


- 多租用戶應用程式︰ 多租用戶應用程式適用於許多組織中不只一個組織。 這些是通常軟體-為-的-服務 (SaaS) 應用程式由獨立軟體廠商 (ISV)。 多租用戶應用程式需要在每個目錄位置，將會使用，需要它們的註冊的使用者或系統管理員同意佈建。 在應用程式目錄中已註冊，都可以存取圖形 API 或可能是另一個網路 API，就會啟動此同意程序。 當使用者或從不同的組織的系統管理員註冊使用應用程式時，他們會出現一個對話方塊，顯示應用程式所需要的權限。 使用者或系統管理員可以同意應用程式，可讓應用程式存取至指定的資料，並最後登錄他們的目錄中的 [應用程式。 如需詳細資訊，請參閱[同意架構的概觀](active-directory-integrating-applications.md#overview-of-the-consent-framework)。

開發多租用戶應用程式，而不是單一租用戶應用程式時，就會發生的其他事項。 比方說，如果您要製作您的應用程式使用多個目錄中的使用者，您需要判斷哪一個租用戶的機制它們是。 單一租用戶應用程式只需要查看自己的目錄，為使用者時多租用戶的應用程式需要找出 Azure AD 中的特定使用者從所有目錄。 若要完成此工作，Azure AD 提供常見的驗證端點多租用戶中的任何應用程式可以直接登入要求，而不是租用戶專屬結束點的位置。 此端點位於 https://login.microsoftonline.com/common 的所有目錄 Azure AD，而租用戶專屬端點可能 https://login.microsoftonline.com/contoso.onmicrosoft.com。 常見的端點是特別重要考量的事項開發應用程式，因為您需要登入，教具借出，與權杖驗證期間處理多個租用戶必要的邏輯。

如果您目前正在開發的單一租用戶應用程式，但想要使用於許多組織，您可以輕鬆地進行變更，使其多租用戶的應用程式和 Azure AD 其設定簡訊。 此外，Azure AD 使用相同的簽章鍵在所有的目錄中的所有權杖是否您提供的租用戶單一或多租用戶應用程式中的驗證。

這份文件中所列的每個案例包括子區段描述其佈建的需求。 深入瞭解如何佈建應用程式中 Azure AD 和單一與多租用戶應用程式之間的差異的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](active-directory-integrating-applications.md)如需詳細資訊。 繼續閱讀以瞭解 Azure AD 常見的應用程式案例。

## <a name="application-types-and-scenarios"></a>應用程式類型和案例

每個案例說明這份文件可以使用各種不同的語言與平台開發。 所有備份他們所完成的程式碼範例會使用我們[的程式碼範例指南](active-directory-code-samples.md)，或直接從對應[Github 範例存放庫](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory)。 此外，如果您的應用程式需要特定的項目或的端對端案例的區段，在大部分情況下，將功能新增獨立。 例如，如果您有電話網路 API 的原生應用程式，您可以輕鬆地新增也會撥打網路 API 的 web 應用程式。 下圖說明這些案例與應用程式類型，以及可以新增不同的元件︰

![應用程式類型和案例](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

以下是支援 Azure AD 的五個主要的應用程式案例︰

- [Web 應用程式的網頁瀏覽器](#web-browser-to-web-application)︰ 使用者必須登入 Azure AD 受保護的 web 應用程式。

- [單一網頁應用程式] 選項](#single-page-application-spa)︰ 使用者必須登入 Azure AD 受保護的單一頁面應用程式。

- [原生應用程式至網頁 API](#native-application-to-web-api)︰ 電話、 平板電腦或電腦執行的原生應用程式需要從 Azure AD 受保護的 API 的 web 取得資源的使用者進行驗證。

- [Web API 的 web 應用程式](#web-application-to-web-api)︰ web 應用程式所需的資源從 Azure AD 受保護的 API 網頁。

- [精靈或 Web API 伺服器應用程式](#daemon-or-server-application-to-web-api)︰ 精靈應用程式或 web 使用者介面與伺服器應用程式需要從網路 API 受到 Azure AD 取得資源。

### <a name="web-browser-to-web-application"></a>Web 應用程式的網頁瀏覽器

本節描述的驗證的 web 應用程式在網頁瀏覽器中使用者的應用程式。 在此案例中，web 應用程式會引導至 Azure AD 登入他們的使用者的瀏覽器。 Azure AD 傳回透過使用者的瀏覽器，其中包含宣告相關的安全性 token 使用者登入回應。 登入使用 WS 同盟 SAML 2.0 與 OpenID 連線的通訊協定，支援這種情況。


#### <a name="diagram"></a>圖表
![驗證的 web 應用程式的瀏覽器的流程](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>通訊協定流程的描述


1. 當使用者造訪應用程式和登入的需求時，它們會被重新導向驗證端點的登入要求透過 Azure AD 中。


2. 在登入頁面上，使用者登入。


3. 如果驗證成功，Azure AD 會建立一個驗證的權杖，並傳回應用程式的回覆 URL Azure 管理入口網站中所設定的登入回應。 生產應用程式，此回覆 URL 應該 HTTPS。 傳回的權杖包含應用程式，以驗證的權杖所需的使用者和 Azure AD 宣告。


4. 應用程式所用的簽章公開金鑰和可用的發行者資訊同盟中繼資料文件 Azure AD 驗證的權杖。 應用程式驗證的權杖之後，Azure AD 會開始新的工作階段與使用者。 這個工作階段允許使用者存取應用程式，直到到期。


#### <a name="code-samples"></a>程式碼範例


請參閱的程式碼範例的網頁瀏覽器到 Web 應用程式案例。 常返回檢查--我們一直新增新的範例。 [Web 應用程式的網頁瀏覽器](active-directory-code-samples.md#web-browser-to-web-application)。


#### <a name="registering"></a>註冊


- 單一租用戶︰ 如果您只為您的組織建立應用程式，它必須註冊貴公司的目錄中使用 Azure 管理入口網站。


- 多租用戶︰ 如果您要建立的應用程式，可讓組織外的使用者，它必須註冊公司目錄，但也必須使用應用程式的每一個組織的目錄中註冊。 若要讓您的應用程式提供他們的目錄，您可以包含註冊程序的客戶，讓它們同意應用程式。 當他們註冊您的應用程式時，其會顯示對話方塊會顯示應用程式所需要的權限，然後按一下 [同意的選項。 必要的權限，根據其他組織的系統管理員可能必須授與同意。 時，使用者或管理員 consents，則會在他們的目錄中登錄應用程式。 如需詳細資訊，請參閱[Azure Active Directory 的整合應用程式](active-directory-integrating-applications.md)。


#### <a name="token-expiration"></a>權杖到期日

使用者的工作階段到期時發出 Azure AD 的 token 的存留時間到期。 您的應用程式可以縮短這段時間內，如有需要，例如登出根據閒置一段的使用者。 當工作階段到期時，使用者就會提示您再次登入。





### <a name="single-page-application-spa"></a>單一頁面應用程式] 選項

本節描述驗證單一頁面應用程式，使用 Azure AD 與 OAuth 2.0 隱含授權授與安全性 API 回結束其網頁。 單一網頁應用程式通常是結構化為 JavaScript 簡報的圖層 （前端） 在瀏覽器和 Web API 的後端伺服器上執行，並實作應用程式的商務邏輯中執行。 若要進一步瞭解隱含授權授與]，並協助您決定是否適合您的應用程式的狀況，請參閱[瞭解 OAuth2 隱含授與流程 Azure Active Directory 中](active-directory-dev-understanding-oauth2-implicit-grant.md)。

在此案例中，當使用者登入，JavaScript 上層結束用途[Active Directory 驗證庫的 JavaScript (ADAL。JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev)和隱含授權授與]，從 Azure AD 取得識別碼權杖 (id_token)。 權杖快取並用戶端將其附加至邀請承載者權杖撥打電話其網頁 API 後端，使用 OWIN 介軟體受到保護時。 
#### <a name="diagram"></a>圖表

![單一網頁應用程式的圖表](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>通訊協定流程的描述

1. 使用者瀏覽到 web 應用程式。


2. 應用程式會傳回 JavaScript 前端 （簡報層） 至瀏覽器。


3. 使用者啟動登入，例如即可登入] 連結。 瀏覽器會傳送至 Azure AD 授權端點要求識別碼權杖取得。 此邀請中包含的用戶端識別碼和回覆 URL 中的查詢參數。


4. Azure AD 驗證回覆 URL 對已註冊 Azure 管理入口網站中設定回覆 URL。


5. 在登入頁面上，使用者登入。


6. 如果驗證成功，Azure AD 會建立一個 ID 權杖，並為 URL 片段 （#） 傳回應用程式的回覆 URL。 生產應用程式，此回覆 URL 應該 HTTPS。 傳回的權杖包含應用程式，以驗證的權杖所需的使用者和 Azure AD 宣告。


7. 在瀏覽器中執行的用戶端 JavaScript 程式碼會將權杖起保護 API 回結束的來電至] 應用程式的網頁中使用的回應。


8. 在瀏覽器通話 API 回結尾的存取權杖授權標頭中的應用程式的網頁。

#### <a name="code-samples"></a>程式碼範例


請參閱針對單一頁面應用程式] 選項案例的程式碼範例。 請務必常返回檢查--我們一直新增新的範例。 [單一頁面應用程式] 選項](active-directory-code-samples.md#single-page-application-spa)。


#### <a name="registering"></a>註冊


- 單一租用戶︰ 如果您只為您的組織建立應用程式，它必須註冊貴公司的目錄中使用 Azure 管理入口網站。


- 多租用戶︰ 如果您要建立的應用程式，可讓組織外的使用者，它必須註冊公司目錄，但也必須使用應用程式的每一個組織的目錄中註冊。 若要讓您的應用程式提供他們的目錄，您可以包含註冊程序的客戶，讓它們同意應用程式。 當他們註冊您的應用程式時，其會顯示對話方塊會顯示應用程式所需要的權限，然後按一下 [同意的選項。 必要的權限，根據其他組織的系統管理員可能必須授與同意。 時，使用者或管理員 consents，則會在他們的目錄中登錄應用程式。 如需詳細資訊，請參閱[Azure Active Directory 的整合應用程式](active-directory-integrating-applications.md)。

在註冊之後應用程式，必須將它設定為使用 OAuth 2.0 隱含授與通訊協定。 根據預設，這個通訊協定會停用的應用程式。 若要啟用您的應用程式的 OAuth2 隱含授與通訊協定，從 Azure 管理入口網站下載其應用程式資訊清單、 「 oauth2AllowImplicitFlow 」 值設為 true，然後再上傳資訊清單後入口網站。 如需詳細指示，請參閱[啟用 OAuth 2.0 隱含授與單一頁面應用程式](active-directory-integrating-applications.md)。


#### <a name="token-expiration"></a>權杖到期日

當您使用 ADAL.js 管理 Azure AD 驗證時，您可以從數個功能，方便更新過期的權杖，以及權杖取得其他 web 應用程式可能呼叫的 API 資源。 Azure AD 順利驗證使用者，會在瀏覽器和 Azure AD 之間的使用者建立 cookie 受保護的工作階段。 請務必注意的工作階段之間的使用者和 Azure AD 不之間以及使用者與伺服器上執行的 web 應用程式。 當權杖到期時，ADAL.js 會使用這個工作階段自動取得其他權杖。 它會使用隱藏的 iFrame 傳送及接收使用 OAuth 隱含授與通訊協定的要求。 ADAL.js 也可以使用相同的機制自動取得存取權杖從 Azure AD 其他網站的 API 資源的應用程式通話，只要這些資源支援十字形，資源共用 (CORS)，都登錄在使用者的目錄，或任何必要的同意使用者指定在登入。


### <a name="native-application-to-web-api"></a>網路 API 原生應用程式


本節說明通話代表使用者網路 API 的原生應用程式。 這種情況下建立 OAuth 2.0 授權的程式碼授與類型，使用公用的用戶端，4.1 的一節[OAuth 2.0 規格](http://tools.ietf.org/html/rfc6749)所述。 原生應用程式會使用 OAuth 2.0 通訊協定取得使用者存取權杖。 此存取權杖，然後會傳送至 web 其授權的使用者，並傳回您要的資源的 API 邀請中。

#### <a name="diagram"></a>圖表

![原生應用程式至網頁 API 圖表](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>驗證流程 api 的原生應用程式

#### <a name="description-of-protocol-flow"></a>通訊協定流程的描述


如果您使用 AD 驗證的文件庫，大部分的通訊協定詳細資料，如下所述處理，例如瀏覽器快顯、 token 快取及重新整理權杖之處理方式。

1. 使用瀏覽器快顯，原生應用程式中 Azure AD 授權端點提出要求。 此邀請中包含用戶端識別碼] 和 [重新導向 URI 原生應用程式的 [管理] 入口網站和網路 API 的應用程式識別碼 URI 中所示。 如果使用者未登入，系統會提示再次登入


2. Azure AD 授權的使用者。 如果是多租用戶應用程式，且同意，才能使用應用程式，您就會需要使用者同意如果您尚未這麼做。 授與同意之後，在驗證成功 Azure AD 問題回到用戶端應用程式的重新導向 URI 授權程式碼回應。


3. Azure AD 問題回到重新導向 URI 授權程式碼回應，用戶端應用程式停止瀏覽器互動，回應算起的驗證碼。 使用此授權程式碼，用戶端應用程式會傳送給 Azure AD token 端點包含授權程式碼，用戶端應用程式 （用戶端識別碼及重新導向 URI） 和您要的資源 （應用程式識別碼 URI 的網路 API） 的相關詳細資料的要求。


4. Azure AD 驗證的驗證碼和用戶端應用程式與網路 API 的相關資訊。 驗證成功，依據 Azure AD 傳回兩個權杖︰ JWT 存取權杖，JWT 重新整理權杖。 此外，Azure AD 傳回的使用者，例如他們的顯示名稱] 和 [租用戶識別碼的基本資訊


5. 在 HTTPS 上用戶端應用程式會使用 「 承載者 」 指定的 JWT 字串要求授權頁首中加入網路 API 傳回的 JWT 存取權杖。 網路 API 驗證 JWT 權杖，然後如果驗證成功，會傳回您要的資源。


6. 當存取權杖到期時，用戶端應用程式就會收到錯誤，指出使用者必須再次進行驗證。 如果應用程式的有效的重新整理權杖，就可取得新的存取權杖，而不會提示使用者，以再次登入。 如果重新整理權杖過期，應用程式必須互動的方式使用者進行驗證，一次一次。


> [AZURE.NOTE] Azure AD 發出的重新整理 token 可用來存取多個資源。 例如，如果您有來電兩個 web Api 的權限的用戶端應用程式，重新整理權杖可用來存取權杖其他網路 API 以及。


#### <a name="code-samples"></a>程式碼範例


請參閱 Web API 案例的原生應用程式的程式碼範例。 常返回檢查--我們一直新增新的範例。 [網路 API 原生應用程式](active-directory-code-samples.md#native-application-to-web-api)。


#### <a name="registering"></a>註冊


- 單一租用戶︰ 兩個的原生應用程式與網路 API 必須註冊相同的目錄中 Azure AD 中。 您可以設定網路 API 公開一組用來限制其資源的原生應用程式的存取權限。 然後用戶端應用程式從 」 權限以其他應用程式] 下拉式功能表 Azure 管理入口網站中選取所要的權限。


- 多租用戶︰ 首先，原生應用程式只有註冊開發人員或發行者的目錄中。 第二，原生應用程式設定為指出正常運作所需的權限]。 必要的權限這份清單會顯示] 對話方塊中，當使用者或目的目錄中的系統管理員可同意可讓您組織可用的應用程式。 部分應用程式要求只使用者層級權限，組織中的任何使用者可以同意。 其他應用程式需要系統管理員等級權限，組織中的使用者無法同意。 僅限 directory 系統管理員可以同意需要此權限等級的應用程式。 當使用者或系統管理員 consents 時，只是網路 API 已註冊其目錄中。 如需詳細資訊，請參閱[Azure Active Directory 的整合應用程式](active-directory-integrating-applications.md)。


#### <a name="token-expiration"></a>權杖到期日


原生應用程式會使用其授權的程式碼，以取得 JWT 存取權杖，也會收到 JWT 重新整理權杖。 當存取權杖到期時，重新整理權杖可用來重新使用者進行驗證，而不需要再次登入。 此重新整理權杖然後會用於驗證使用者，而導致的新存取權杖和重新整理權杖。





### <a name="web-application-to-web-api"></a>網路 API 的 web 應用程式


本節描述所需的資源從網路 API 的 web 應用程式。 在此案例中，有兩種身分識別的 web 應用程式可以使用項目會驗證並呼叫網路 API︰ 應用程式識別碼或委派的使用者身分識別。

*應用程式識別碼︰*這種情況下使用 OAuth 2.0 用戶端認證授與驗證為應用程式，並存取網路 API。 使用應用程式識別碼，API 只能偵測 web 應用程式時，呼叫的網頁時與 web API 沒有收到任何使用者的相關資訊。 如果應用程式收到使用者的相關資訊，將會傳送透過應用程式通訊協定和 Azure AD 未簽署。 網路 API 信任 web 應用程式已驗證的使用者。 因此，此模式稱為受信任的子系統。

*委派的使用者身分識別︰*兩種方法可以完成這種情況︰ OpenID 連線，並與機密的用戶端的 OAuth 2.0 授權的程式碼授與。 Web 應用程式會取得的使用者，證明網路 API 使用者成功通過驗證的 web 應用程式並 web 應用程式能夠取得委派的使用者身分撥打網路 API 的存取權杖。 此存取權杖會傳送至 web 其授權的使用者，並傳回您要的資源的 API 邀請中。

#### <a name="diagram"></a>圖表

![Web 應用程式至網頁 API 圖表](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>通訊協定流程的描述

將 [應用程式識別碼] 與 [委派的使用者身分識別類型流程以下所述。 它們之間的主要差異是委派的使用者身分識別必須先獲得授權碼之前使用者可以登入，並取得網路 API 的存取權。

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>使用 OAuth 2.0 用戶端應用程式識別碼認證授與

1. 使用者登入 web 應用程式中的 Azure AD （請參閱[Web 應用程式的網頁瀏覽器](#web-browser-to-web-application)上方）。


2. Web 應用程式需要取得存取權杖，使其可以驗證網路 API，並擷取所需的資源。 它 Azure AD token 端點，提供認證、 用戶端識別碼和網路 API 的應用程式識別碼 URI 提出要求。


3. Azure AD 驗證應用程式，並傳回用於呼叫網路 API JWT 存取權杖。


4. 透過 HTTPS，web 應用程式會使用 「 承載者 」 指定的 JWT 字串要求授權頁首中加入網路 API 傳回的 JWT 存取權杖。 網路 API 驗證 JWT 權杖，然後如果驗證成功，會傳回您要的資源。

##### <a name="delegated-user-identity-with-openid-connect"></a>委派的使用者身分識別與 OpenID 連線

1. 使用者登入 web 應用程式使用 Azure AD （請參閱[Web 應用程式的網頁瀏覽器](#web-browser-to-web-application)節）。 如果還不具有同意 web 應用程式的使用者允許代表自己撥打網路 API web 應用程式，使用者必須同意。 應用程式會顯示該要求的權限，如果有任何這些不是系統管理員等級權限，在目錄中的一般使用者將無法同意。 此同意程序只套用到多租用戶應用程式，不單一租用戶應用程式，必須已經應用程式的必要權限。 當使用者登入時，請 web 應用程式就會收到 ID 權杖，使用者，以及授權的程式碼的相關資訊。


2. 使用由 Azure AD 授權程式碼，web 應用程式會傳送給 Azure AD token 端點包含授權程式碼，用戶端應用程式 （用戶端識別碼及重新導向 URI） 和您要的資源 （應用程式識別碼 URI 的網路 API） 的相關詳細資料的要求。


3. Azure AD 驗證的驗證碼及網頁 API 與 web 應用程式的相關資訊。 驗證成功，依據 Azure AD 傳回兩個權杖︰ JWT 存取權杖，JWT 重新整理權杖。


4. 透過 HTTPS，web 應用程式會使用 「 承載者 」 指定的 JWT 字串要求授權頁首中加入網路 API 傳回的 JWT 存取權杖。 網路 API 驗證 JWT 權杖，然後如果驗證成功，會傳回您要的資源。

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>與 OAuth 2.0 授權碼授與的委派的使用者身分識別

1. 使用者已登入 web 應用程式，其驗證機制無關的 Azure AD。


2. Web 應用程式需要授權的程式碼來擷取存取權杖，讓它 Azure AD 授權端點，提供用戶端識別碼問題透過瀏覽器的要求，並將 URI web 應用程式驗證成功之後重新導向。 Azure AD 登入的使用者。


3. 如果還不具有同意 web 應用程式的使用者允許代表自己撥打網路 API web 應用程式，使用者必須同意。 應用程式會顯示該要求的權限，如果有任何這些不是系統管理員等級權限，在目錄中的一般使用者將無法同意。 此同意程序只套用到多租用戶應用程式，不單一租用戶應用程式，必須已經應用程式的必要權限。


4. 具有同意使用者之後，web 應用程式就會收到擷取存取權杖，需要將授權程式碼。


5. 使用由 Azure AD 授權程式碼，web 應用程式會傳送給 Azure AD token 端點包含授權程式碼，用戶端應用程式 （用戶端識別碼及重新導向 URI） 和您要的資源 （應用程式識別碼 URI 的網路 API） 的相關詳細資料的要求。


6. Azure AD 驗證的驗證碼及網頁 API 與 web 應用程式的相關資訊。 驗證成功，依據 Azure AD 傳回兩個權杖︰ JWT 存取權杖，JWT 重新整理權杖。


7. 透過 HTTPS，web 應用程式會使用 「 承載者 」 指定的 JWT 字串要求授權頁首中加入網路 API 傳回的 JWT 存取權杖。 網路 API 驗證 JWT 權杖，然後如果驗證成功，會傳回您要的資源。

#### <a name="code-samples"></a>程式碼範例

請參閱 Web API 案例的 Web 應用程式的程式碼範例。 常返回檢查--我們一直新增新的範例。 Web[應用程式來網路 API](active-directory-code-samples.md#web-application-to-web-api)。


#### <a name="registering"></a>註冊

- 單一租用戶︰ 應用程式的身分識別和的委派的使用者身分識別的情況下，web 應用程式和 web API 必須註冊相同的目錄中 Azure AD 中。 您可以設定網路 API 公開一組用來限制其資源的 web 應用程式的存取權限。 如果正在使用的委派的使用者身分識別類型，web 應用程式必須從 」 權限以其他應用程式] 下拉式功能表 Azure 管理入口網站中選取所要的權限。 如果正在使用的應用程式的身分識別類型，不需要此步驟。


- 多租用戶︰ 首先，web 應用程式設定為指出正常運作所需的權限。 必要的權限這份清單會顯示] 對話方塊中，當使用者或目的目錄中的系統管理員可同意可讓您組織可用的應用程式。 部分應用程式要求只使用者層級權限，組織中的任何使用者可以同意。 其他應用程式需要系統管理員等級權限，組織中的使用者無法同意。 僅限 directory 系統管理員可以同意需要此權限等級的應用程式。 當使用者或系統管理員 consents 時，web 應用程式和 web API 會同時註冊其目錄中。

#### <a name="token-expiration"></a>權杖到期日

當 web 應用程式會使用其授權的程式碼，以取得 JWT 存取權杖時，也會收到 JWT 重新整理權杖。 當存取權杖到期時，重新整理權杖可用來重新使用者進行驗證，而不需要再次登入。 此重新整理權杖然後會用於驗證使用者，而導致的新存取權杖和重新整理權杖。


### <a name="daemon-or-server-application-to-web-api"></a>精靈或 Web API 伺服器應用程式


本節描述所需的資源從網路 API 的精靈或伺服器應用程式。 有兩種子案例適用於此節︰ 需要呼叫網路 API，內建 OAuth 2.0 用戶端認證授與類型; 在精靈與需要呼叫網路 API，內建在 OAuth 2.0 On-Behalf-Of 草稿規格伺服器應用程式 （例如 web API)。

案例精靈應用程式需要通話的 web API，務必瞭解幾個項目。 首先，不能精靈應用程式，必須要有自己的身分識別的應用程式使用者互動。 精靈應用程式的範例是批次工作或在背景中執行的作業系統服務。 這種應用程式要求存取權杖使用其應用程式身分識別，簡報的用戶端識別碼]、 [認證 （密碼或憑證） 及 [應用程式識別碼 URI Azure AD。 順利驗證後，精靈會接收 Azure AD，然後用來呼叫網路 API 的存取權杖。

案例伺服器應用程式需要通話的 web API，還是有幫助範例。 假設使用者驗證的原生應用程式，在這個原生應用程式需要通話網路 API。 Azure AD 問題撥打網路 API JWT 存取權杖。 如果需要撥打另一個下游網路 API web API，它就可以使用代表的流程委派的使用者識別碼和驗證第二層網路 API。

#### <a name="diagram"></a>圖表

![精靈或 Web API 圖表伺服器應用程式](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>通訊協定流程的描述

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>使用 OAuth 2.0 用戶端應用程式識別碼認證授與

1. 首先，伺服器應用程式需要本身，沒有任何人的互動，例如互動式登入對話方塊的 Azure AD 驗證方法。 它 Azure AD token 端點，提供的認證，用戶端識別碼和應用程式識別碼 URI 提出要求。


2. Azure AD 驗證應用程式，並傳回用於呼叫網路 API JWT 存取權杖。


3. 透過 HTTPS，web 應用程式會使用 「 承載者 」 指定的 JWT 字串要求授權頁首中加入網路 API 傳回的 JWT 存取權杖。 網路 API 驗證 JWT 權杖，然後如果驗證成功，會傳回您要的資源。


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>委派的使用者身分識別與 OAuth 2.0 代表的草稿規格

如下所示的流程假設已經過使用者驗證 （例如的原生應用程式），另一個應用程式，並已使用其使用者身分識別，以取得第一層網路 API 的存取權杖。

1. 原生應用程式會傳送到第一層網路 API 的存取權杖。


2. 第一層 web API 傳送要求給 Azure AD token 端點，提供其的用戶端識別碼和認證，以及使用者的存取權杖。 此外，會傳送要求 on_behalf_of 參數，指出網路 API 要求新權杖撥打下游網路 API 代表原始的使用者。


3. Azure AD 驗證第一層網路 API 具有第二層網路 API 的存取權限，並確認邀請中，傳回 JWT 存取權杖 JWT 重新整理至第一層網路 API 的 token。


4. 在 HTTPS 上第一層 web API 然後呼叫第二層網路 API 附加在邀請中的授權標頭中的權杖字串。 第一層網路 API 可以繼續通話第二層網路 API，只要存取權杖和重新整理權杖有效。

#### <a name="code-samples"></a>程式碼範例

精靈或 Web API 案例伺服器應用程式，請參閱的程式碼範例。 常返回檢查--我們一直新增新的範例。 [伺服器或網路 API 精靈應用程式](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>註冊

- 單一租用戶︰ 應用程式的身分識別和的委派的使用者身分識別的情況下，精靈或伺服器應用程式必須註冊相同的目錄中 Azure AD 中。 您可以設定網路 API 公開一組用來限制精靈] 或 [資源] 伺服器的存取權限。 如果正在使用的委派的使用者身分識別類型，伺服器應用程式必須從 」 權限以其他應用程式] 下拉式功能表 Azure 管理入口網站中選取所要的權限。 如果正在使用的應用程式的身分識別類型，不需要此步驟。


- 多租用戶︰ 首先，精靈或伺服器應用程式設定表示正常運作所需的權限。 必要的權限這份清單會顯示] 對話方塊中，當使用者或目的目錄中的系統管理員可同意可讓您組織可用的應用程式。 部分應用程式要求只使用者層級權限，組織中的任何使用者可以同意。 其他應用程式需要系統管理員等級權限，組織中的使用者無法同意。 僅限 directory 系統管理員可以同意需要此權限等級的應用程式。 當使用者或系統管理員 consents 時，這兩個 web Api 註冊其目錄中。

#### <a name="token-expiration"></a>權杖到期日

第一個應用程式會使用其授權的程式碼，以取得 JWT 存取權杖，也會收到 JWT 重新整理權杖。 當存取權杖到期時，重新整理權杖可用來重新使用者進行驗證，而不提示輸入認證。 此重新整理權杖然後會用於驗證使用者，而導致的新存取權杖和重新整理權杖。

## <a name="see-also"></a>另請參閱

[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)

[Azure Active Directory 程式碼範例](active-directory-code-samples.md)

[Azure AD 中簽署金鑰變換的重要資訊](active-directory-signing-key-rollover.md)

[Azure AD 中 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)
