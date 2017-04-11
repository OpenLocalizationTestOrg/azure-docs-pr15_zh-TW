<properties
    pageTitle="Azure Active Directory 單一登入整合 SaaS 應用程式 | Microsoft Azure"
    description="啟用單一登入驗證與佈建集中的存取管理 SaaS Azure Active Directory 中的應用程式的使用者。 概略瞭解如何將整合 Azure Active Directory SaaS 應用程式。"
    services="active-directory"
      keywords="Azure AD 整合 SaaS 應用程式"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Azure Active Directory 單一登入整合 SaaS 應用程式  

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-enterprise-apps-manage-sso.md)
- [Azure 傳統入口網站](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

若要開始設定單一登入應用程式的帶入您的組織，您將會使用現有的目錄中 Azure Active Directory (Azure AD)。 您可以使用您取得透過 Microsoft Azure、 Office 365] 或 Windows Intune Azure AD 目錄。 如果您有兩個或更多的請參閱[管理您的 Azure AD 目錄](active-directory-administer.md)來決定要使用哪一個。

## <a name="authentication"></a>驗證

應用程式的支援 SAML 2.0，WS 同盟或 OpenID 連線的通訊協定，簽章憑證建立信任的關聯性的 Azure Active Directory 用途。 如需相關資訊，請參閱[管理憑證的同盟單一登入](active-directory-sso-certs.md)。

Azure Active Directory 只 HTML 表單型登入支援的應用程式，使用 「 密碼儲存庫存 」 建立信任的關聯性。 這可讓您組織中的使用者，會自動登入 SaaS 應用程式所使用的 SaaS 應用程式的使用者帳戶資訊的 Azure AD。 Azure AD，收集並安全地儲存的使用者帳戶資訊和相關的密碼。 如需詳細資訊，請參閱[密碼以單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)。

## <a name="authorization"></a>授權

提供的帳戶可讓使用者以他們有驗證透過單一登入之後，請使用應用程式授權。 使用者佈建方式，以手動方式，或是在某些情況下，您可以新增和移除根據 Azure Active Directory 中所做的變更 SaaS 應用程式中的使用者資訊。 如需有關如何使用現有的 Azure AD 連接器的自動化佈建的詳細資訊，請參閱[自動使用者佈建和不支援的 SaaS 應用程式](active-directory-saas-app-provisioning.md)。

否則，您可以手動將使用者資訊新增至應用程式]，或使用其他佈建的解決方案，可在市場中。

## <a name="access"></a>存取

Azure AD 提供可自訂的數種方式部署應用程式到您的組織中的使用者。 您不會鎖定任何特定部署或存取解決方案。 您可以使用[的最符合您需求的解決方案](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。

## <a name="additional-considerations-for-applications-already-in-use"></a>其他考量已經應用程式中使用

設定單一登入貴組織已使用的應用程式是不同的程序建立新的應用程式的新帳戶的程序。 有一些基本步驟，包括︰ 對應 Azure AD 身分識別，應用程式中的使用者身分識別，並瞭解使用者如何體驗整合之後，登入應用程式。

> [AZURE.NOTE] 若要設定現有的應用程式 SSO，您需要兩個 Azure AD 中具有全域管理員權限與 SaaS 應用程式。

### <a name="mapping-user-accounts"></a>對應的使用者帳戶

使用者的身分識別通常都有唯一識別碼，可能是在電子郵件地址或使用者主要名稱 (UPN)。 您將需要 （對應表） 連結至其個別的每個使用者的應用程式識別碼 Azure AD 身分識別。 有數種方式完成這項工作的方式視您的應用程式驗證的需求。

如需有關對應應用程式的身分識別與 Azure AD 身分識別的詳細資訊，請參閱[自訂宣告發出 SAML 權杖](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)和[佈建的自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)。

### <a name="understanding-the-users-log-in-experience"></a>了解經驗的使用者的記錄

當您已在使用中的應用程式整合 SSO 時，請務必發現會受影響的使用者體驗。 對於所有的應用程式，使用者會開始使用其 Azure AD 認證登入。 也可能是他們必須存取應用程式使用不同的入口網站。

某些應用程式 SSO 可以完成介面，但其他應用程式的應用程式的登入，使用者必須以瀏覽中央的入口網站，例如 （[我的應用程式](http://myapps.microsoft.com)或[Office365](http://portal.office.com/myapps)） 登入。 進一步瞭解不同類型的 SSO 和使用者經驗的[應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

其他實用的資源是*Suppressing 使用者同意* [Guiding 開發人員](active-directory-applications-guiding-developers-for-lob-applications.md)文件中。

## <a name="next-steps"></a>後續步驟


針對您在應用程式組件庫中找到的 SaaS 應用程式，Azure AD 會提供[如何整合 SaaS 應用程式的教學課程](active-directory-saas-tutorial-list.md)的數字。

如果應用程式不是在應用程式組件庫中，您可以[新增至 Azure AD 應用程式庫，以自訂應用程式](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)。

有更多詳細資料，以開始這些問題 Azure.com 文件庫中的所有[什麼是應用程式存取和單一登入與 Azure Active Directory。](active-directory-appssoaccess-whatis.md)。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
