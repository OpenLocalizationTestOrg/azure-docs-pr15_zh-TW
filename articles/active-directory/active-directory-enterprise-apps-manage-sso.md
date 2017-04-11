<properties
    pageTitle="單一登入管理企業應用程式中的 Azure Active Directory 預覽 |Microsoft Azure"
    description="瞭解如何管理單一登入企業應用程式使用 Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>管理單一登入新的 Azure 入口網站的企業應用程式的 preview:

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-enterprise-apps-manage-sso.md)
- [Azure 傳統入口網站](active-directory-sso-integrate-saas-apps.md)

本文將說明如何管理單一登入應用程式的設定，特別是已新增[Azure Active Directory (Azure AD) 應用程式組件庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)中的所要使用[Azure 入口網站](https://portal.azure.com)。 單一登入的 Azure AD 管理體驗目前公用預覽中，而本文將說明的新功能，以及一些就會在的位置，只有在預覽期間的暫時限制。 [什麼是在預覽中？](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>在新的入口網站尋找您的應用程式

迄年 9 月 2016年已設定的單一的所有應用程式登入在目錄中，使用[Azure Active Directory 應用程式庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)內[Azure 傳統入口網站](https://manage.windowsazure.com)目錄系統管理員，可以立即檢視與管理 Azure 入口網站中。

Azure 入口網站，可以在[入口網站](https://portal.azure.com)中的 [**更多服務**] 清單中找到要連結的 [**企業應用程式**] 區段中，可以找到這些應用程式。 企業應用程式的應用程式的部署，並使用您的組織內部的使用者。

![企業應用程式刀][1]

選取 [**所有應用程式**可檢視所有已設定，包括已從圖庫新增的應用程式的應用程式的清單。 選取應用程式會載入資源刀，該應用程式，在該應用程式，可以檢視報表，且可管理各種不同的設定。

若要管理單一登入設定，請選取**單一登入**。

![應用程式資源刀][2]


##<a name="single-sign-on-modes"></a>單一登入模式

**單一登入**刀開頭為**模式**] 功能表，可讓單一登入模式設定]。 可用的選項包括︰

* 如果應用程式支援完整同盟單一登入使用 SAML 2.0 通訊協定的 Azure Active Directory 使用**SAML 為基礎的登入**這個選項。

* 如果 Azure AD 支援填寫此應用程式的密碼表單使用**密碼以登入**這個選項。

* **連結登入**-稱為 「 現有單一登入]，此選項可讓系統管理員在使用者的 Azure AD 存取畫面或 Office 365 應用程式啟動器將此應用程式的連結。

如需有關這些模式的詳細資訊，請參閱[如何沒有單一登入與 Azure Active Directory 工作](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)。


##<a name="saml-based-sign-on"></a>以 SAML 為基礎的登入

[**以 SAML 為基礎的登入**] 選項會顯示四個區段中的除數刀︰

###<a name="domains-and-urls"></a>網域和 Url
這是應用程式的網域和 Url 的所有詳細會都新增至 Azure AD 目錄的位置。 單一登入工作應用程式所需的所有輸入會直接在螢幕上都顯示，而選取 [**顯示進階 URL 設定**核取方塊，即可檢視所有的選擇性輸入。 完整支援輸入清單包含︰

* **登入 URL** – 使用者移至 [登入這個應用程式的位置。 如果應用程式設定為執行服務提供者啟動的單一登入，然後當使用者瀏覽此 URL，服務提供者會驗證並登入的使用者 Azure AD 必要的重新導向。 如果已填入此欄位，Azure AD 會啟動應用程式從 Office 365 和 Azure AD 存取面板使用這個 URL。 如果省略此欄位，然後 Azure AD 改為執行身分識別提供者-發起的登入應用程式時啟動從 Office 365，Azure AD 存取] 面板中，或從 Azure AD 單一登入 URL。

* **識別碼**-此 URI 應該唯一識別應用程式，如正在設定的單一登入。 這是 Azure AD 傳送回應用程式的 SAML 權杖，做為簡報對象參數的值，而且應用程式應該驗證了。 此值也會出現與應用程式提供任何 SAML 中繼資料中的實體識別碼。

* **回覆 URL**回覆 URL 是應用程式預期接收 SAML 權杖的位置。 這也稱為判斷提示消費者服務 (ACS) URL。 這些輸入之後，請按一下 [下一步] 繼續進行下一個畫面。 這個畫面提供所需的設定讓它接受從 Azure AD SAML 權杖應用程式方的相關資訊。

* **轉送狀態**轉送狀態是選用的參數，可協助您判斷應用程式完成驗證後，將使用者導向的位置。 通常值是有效的 URL，在應用程式，不過部分應用程式中使用此欄位以不同方式 （在文件，如需詳細資訊，請參閱應用程式的單一登）。 設定轉送狀態的功能是唯一的新 Azure 入口網站的新功能。

###<a name="user-attributes"></a>使用者屬性
這是系統管理員可以檢視及編輯的屬性，以傳送至應用程式每個問題的 Azure AD SAML 權杖時間使用者登入的位置。

第一個預覽版本中，僅可編輯屬性支援是**使用者識別碼**屬性。 此屬性的值是唯一識別每位使用者的應用程式中的 Azure AD 中的欄位。 例如，如果應用程式的部署使用 「 電子郵件地址 」 作為使用者名稱和唯一識別碼，然後值會設為 「 user.mail 」 欄位中 Azure AD。

編輯其他屬性將會支援在後續的預覽。

###<a name="saml-signing-certificate"></a>SAML 簽章憑證
本節說明 Azure AD 用來登入 SAML 權杖發給每次使用者驗證的應用程式的憑證詳細資料。 讓目前的憑證要檢查的屬性包括 [到期日。

變換憑證的功能及管理後續預覽版本中會支援選項的其他憑證。 請注意完整管理憑證仍然可以執行[Azure 傳統入口網站](active-directory-sso-certs.md)中。

###<a name="application-configuration"></a>應用程式設定
最後一節提供的文件和/或設定應用程式本身作為 Azure Active Directory 的身分識別提供者所需的控制項。

**設定應用程式**] 飛出功能表提供新簡潔的內嵌的指示設定應用程式。 這是另一項新功能唯一新 Azure 入口網站。

> [AZURE.NOTE] 內嵌的文件的完整的範例，請參閱 Salesforce.com 應用程式。 在預覽已持續加入文件，其他應用程式。

![內嵌的文件][3]

##<a name="password-based-sign-on"></a>密碼以登入
如果應用程式的支援，請選取 [將密碼型 SSO 模式，選取**儲存**立即設定為執行密碼為基礎的 SSO。 更多有關部署密碼為基礎的 SSO 的詳細資訊，請參閱[如何沒有單一登入與 Azure Active Directory 工作](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)。

![密碼以登入][4]


##<a name="linked-sign-on"></a>連結的登入
如果應用程式的支援，請選取連結的 SSO 模式可讓您輸入您想要重新導向至當使用者按一下此應用程式的 Azure AD 存取面板或 Office 365 的 URL。 如需有關連結 SSO （先前稱為 「 現有 SSO 」） 的詳細資訊，請參閱[如何沒有單一登入與 Azure Active Directory 工作](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)。

![連結登入][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
