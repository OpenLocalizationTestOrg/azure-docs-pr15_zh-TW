<properties
   pageTitle="列出您的應用程式中 Azure Active Directory 應用程式組件庫"
   description="如何在清單的單一登入支援 Azure Active Directory 圖庫中的應用程式 |Microsoft Azure"
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


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>列出您的應用程式中 Azure Active Directory 應用程式組件庫

若要支援單一登入與 Azure Active Directory [Azure AD 圖庫](https://azure.microsoft.com/marketplace/active-directory/all/)中的應用程式] 清單中，應用程式必須先實作下列整合模式的其中一個︰

* **OpenID 連線**-直接使用 [驗證和 Azure AD 同意 API 設定 [OpenID 連線 Azure AD 整合。 如果您只啟動整合應用程式不支援 SAML，這是建議模式。

* **SAML** – 您的應用程式中已經有設定使用 SAML 通訊協定的協力廠商身分識別提供者的能力。

清單的每一個模式的需求如下。

##<a name="openid-connect-integration"></a>OpenID 連線整合

若要將應用程式整合使用 Azure AD，[開發人員指示](active-directory-authentication-scenarios.md)執行。 然後完成下列問題，並傳送至waadpartners@microsoft.com。

* 您可用於 Azure AD 小組測試整合的應用程式提供測試租用戶或帳戶認證。  

* 如何登入並使用[Azure AD 同意架構](active-directory-integrating-applications.md#overview-of-the-consent-framework)的應用程式連線 Azure AD 的執行個體 Azure AD 小組提供的指示。 

* 提供任何 Azure AD 小組測試單一登入您的應用程式所需的進一步相關指示。 

* 提供下列資訊︰

> 公司名稱︰
> 
> 公司網站︰
> 
> 應用程式名稱︰
> 
> 應用程式描述 （256 個字元限制）︰
> 
> 應用程式網站 （資訊）︰
> 
> 應用程式的技術支援網站或連絡人的資訊︰
> 
> 用戶端識別碼的應用程式，在 https://manage.windowsazure.com 應用程式詳細資料中所示︰
> 
> 客戶在哪裡註冊的應用程式註冊 URL 和/或購買應用程式︰
> 
> 選擇要在列出 （如可用的類別，請參閱 Azure Active Directory 服務商場） 應用程式的三個類別︰
> 
> 附加 （PNG 檔案，以 45px，實心背景色彩的 45px） 應用程式小圖示︰
> 
> 附加 （PNG 檔案，以純色背景色彩，215px 215px） 應用程式大型圖示︰
> 
> 附加應用程式標誌 （PNG 檔案，以 122px，透明背景色彩的 150px）︰

##<a name="saml-integration"></a>SAML 整合

支援 SAML 2.0 任何應用程式可以使用[這些指示新增自訂的應用程式](active-directory-saas-custom-apps.md)Azure AD 租用戶的直接整合。 當您測試您的應用程式整合搭配 Azure AD 時，傳送下列資訊<waadpartners@microsoft.com>。

* 您可用於 Azure AD 小組測試整合的應用程式提供測試租用戶或帳戶認證。  

* 您的應用程式，以說明[以下](active-directory-saas-custom-apps.md)提供 SAML 登入 URL、 發行者 URL (實體 ID)，以及回覆 URL （判斷提示消費者服務） 值。 如果您通常會提供這些值做為 SAML 中繼資料檔案的一部分，然後請傳送的以及。

* 提供如何將 Azure AD 設定為在您的應用程式使用 SAML 2.0 身分識別提供者的簡短描述。 如果您的應用程式支援設定 Azure AD，透過自助的系統管理入口網站的身分識別提供者為，然後請確定在上面提供的認證包含此設定的功能。

* 提供下列資訊︰

> 公司名稱︰
> 
> 公司網站︰
> 
> 應用程式名稱︰
> 
> 應用程式描述 （256 個字元限制）︰
> 
> 應用程式網站 （資訊）︰
> 
> 應用程式的技術支援網站或連絡人的資訊︰
> 
> 客戶在哪裡註冊的應用程式註冊 URL 和/或購買應用程式︰
> 
> 選擇要在列出 （如可用的類別，請參閱[Azure Active Directory 服務商場](https://azure.microsoft.com/marketplace/active-directory/)） 應用程式的三個類別）︰
> 
> 附加 （PNG 檔案，以 45px，實心背景色彩的 45px） 應用程式小圖示︰
> 
> 附加大型應用程式圖示 （PNG 檔案，以 215px，實心背景色彩的 215px）︰
> 
> 附加的應用程式標誌 （PNG 檔案，以 122px，透明背景色彩的 150px）︰
