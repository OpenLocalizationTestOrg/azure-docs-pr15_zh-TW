<properties
    pageTitle="Azure Active Directory B2C︰ 常見問題集 |Microsoft Azure"
    description="Azure Active Directory B2C 相關的常見問題集"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C︰ 常見問題集

此頁面將回答有關 Azure Active Directory (Azure AD) B2C 的常見問題集的問題。 保留檢查回更新。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>可以在我現有的員工 Azure AD 租用戶中使用 Azure AD B2C 功能嗎？

目前 Azure AD B2C 功能無法開啟現有 Azure AD 租用戶。 我們建議您建立另一個租用戶管理您的使用者皆使用 Azure AD B2C 功能。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>我可以使用 Azure AD B2C 提供社交登入 （Facebook 和 Google +） 至 Office 365 嗎？

Azure AD B2C 不能使用 Microsoft Office 365。 一般而言，就無法用於提供驗證任何 SaaS 應用程式 （Office 365、 Salesforce、 Workday 等）。 它提供身分識別和存取管理僅適用於消費者面對 web 和行動應用程式，並不適用於員工或協力廠商的案例。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C 本機帳戶是什麼？ 他們如何不同的公司或學校帳戶中有 Azure AD？

Azure AD 租用戶中 （除了屬於與現有的 Microsoft 帳戶使用者） 租用戶中的每位使用者登入表單的電子郵件地址`<xyz>@<tenant domain>`，其中`<tenant domain>`是一個租用戶或初始中驗證網域`<...>.onmicrosoft.com`網域。 此類型是帳戶的公司或學校帳戶。

Azure AD B2C 租用戶中大部分的應用程式要讓使用者使用任何任意的電子郵件地址登入 (例如， joe@comcast.net, bob@gmail.com, sarah@contoso.com,或jim@live.com)。 此類型是帳戶的本機帳戶。 現在，我們也支援任意使用者名稱 （只是純文字字串） 做為本機帳戶 （例如，王、 百勝、 幼或 jim）。 您可以選擇下列其中一種這些本機帳戶 Azure AD B2C 服務。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>您現在支援哪些社交的身分識別提供者？ 您打算在未來支援哪些？

目前我們支援 Facebook、 Google +、 LinkedIn 及 Amazon。 我們會將支援根據客戶要求其他熱門的社交身分識別提供者。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>設定收集從各種不同的社交身分識別提供者的詳細資訊使用者皆範圍？

否，但這項功能在我們的藍圖。 我們支援組社交身分識別提供者所用的預設範圍是︰

- Facebook︰ 電子郵件
- Google +: 電子郵件
- Microsoft 帳戶︰ openid 電子郵件設定檔
- Amazon︰ 設定檔
- LinkedIn: r_emailaddress r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>我的應用程式有的它可以搭配 Azure AD B2C Azure 上執行嗎？

否，您也可以裝載任何位置 （在內部部署或雲端） 應用程式。 所有需要互動 Azure AD B2C 是傳送及接收公開存取的端點上 HTTP 要求的能力。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>我有多個 Azure AD B2C 租用戶。 如何管理其 Azure 入口網站上？

每個 Azure AD B2C 租用戶有自己的 B2C 功能刀 Azure 入口網站上。 請參閱[Azure AD B2C︰ 註冊您的應用程式](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)若要瞭解如何您可以瀏覽 Azure 入口網站上的特定租用戶 B2C 功能刀。 Azure AD B2C 目錄 Azure 入口網站上之間切換，不會保留在大部分的瀏覽器中開啟刀您 B2C 功能。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>如何自訂驗證電子郵件 (內容和 」 從:] 欄位) 傳送 Azure AD B2C？

若要自訂驗證電子郵件的內容，您可以使用[公司商標功能](../active-directory/active-directory-add-company-branding.md)。 具體來說，您可以自訂這些的電子郵件的兩個項目︰

- **橫幅標誌**︰ 顯示在右下角。
- **背景色彩**︰ 顯示在上方。

    ![自訂的驗證電子郵件的螢幕擷取畫面](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

電子郵件簽名會包含您第一次建立 B2C 租用戶時所提供的 B2C 租用戶的名稱。 您可以變更名稱使用這些指示︰

- 在訂閱管理員身分登入[Azure 傳統入口網站](https://manage.windowsazure.com/)中。
- 瀏覽至您的 B2C 租用戶。
- 按一下 [**設定**] 索引標籤。
- 變更的**目錄內容**] 區段下的 [**名稱**] 欄位。
- 按一下 [在頁面底部的 [**儲存**]。

目前沒有方法若要變更 「 從: 「 電子郵件] 欄位。 如果您有興趣和完整自訂驗證電子郵件的本文中這項功能，請投票[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails)上的功能。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>如何可以移轉我現有的使用者名稱、 密碼及設定檔從我的資料庫至 Azure AD B2C？

您可以使用 Azure AD 圖形 API 撰寫您移轉工具。 請參閱[圖形 API 範例](active-directory-b2c-devquickstarts-graph-dotnet.md)，如需詳細資訊。 我們會提供不同的移轉選項和工具--現成的未來。

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>密碼原則用於 Azure AD B2C 本機帳戶？

本機帳戶的 Azure AD B2C 密碼原則根據 Azure AD 原則。 Azure AD B2C 的註冊、 註冊或登入和密碼重設原則會使用 「 強式 「 密碼強度，並不會過期任何的密碼。 閱讀更多詳細資料[Azure AD 密碼原則](https://msdn.microsoft.com/library/azure/jj943764.aspx)。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>可以使用 Azure AD Connect 移轉儲存在我的內部部署 Active Directory 至 Azure AD B2C 消費者身分識別嗎？

否，Azure AD 連線不是使用 Azure AD B2C。 我們會提供不同的移轉選項和工具--現成的未來。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C 與搭配運作例如 Microsoft Dynamics CRM 系統？

目前不支援。 整合這些系統是我們藍圖。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>沒有 Azure AD B2C 與 SharePoint 內部部署 2016年搭配運作或更舊版本？

目前不支援。 Azure AD B2C 沒有內建 SharePoint 內部部署需要在入口網站和電子商務應用程式的 SAML 1.1 （英文） 權杖支援。 請注意，Azure AD B2C 不是 SharePoint 外部合作夥伴共用案例。不過，請參閱[Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) 。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>我應該使用 Azure AD B2C 或 B2B 管理外部的身分識別嗎？

閱讀本文有關[外部的身分識別](../active-directory/active-directory-b2b-compare-external-identities.md)，若要進一步瞭解您的外部的身分識別分析藍本套用適當的功能。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>哪些報告及稽核功能 Azure AD B2C 提供？ 它們是與 Azure AD 進階版相同嗎？

否，Azure AD B2C 不支援相同的一組報告為 Azure AD 進階版。 Azure AD B2C 會在基本報表及推出稽核 Api 放開。

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>由 Azure AD B2C 頁面的 UI 可以當地語系化嗎？ 支援哪些語言？

目前，Azure AD B2C 最適合英文只。 我們計畫越快越推行已進行本地化的功能。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>可以使用自己的 Url 由 Azure AD B2C 我註冊和登入頁面上嗎？ 例如，我可以變更 URL 從 login.microsoftonline.com 為 login.contoso.com 嗎？

目前不支援。 此功能是我們藍圖。 另請注意，驗證您的網域，在您的租用戶 Azure 傳統入口網站上的 [**網域**] 索引標籤中將不會做此。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>如何刪除我的 Azure AD B2C 租用戶？

請遵循下列步驟，刪除您 Azure AD B2C 租用戶︰

- 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
- 瀏覽至**應用程式**，**身分識別提供者**及**所有的原則**刀，並刪除每個進行中的所有項目。
- 登入至[Azure 傳統入口網站](https://manage.windowsazure.com/)以訂閱系統管理員。 （此為相同的工作或學校帳戶或您用來註冊 Azure 的相同 Microsoft 帳戶）。
- 瀏覽至左側的 Active Directory 副檔名，然後按一下您 B2C 租用戶。
- 按一下 [**使用者**] 索引標籤。
- 選取每一位使用者在關閉 （不含您目前登入，亦即訂閱系統管理員的使用者）。 按一下 [**刪除**頁面的底部，按一下 [ **]**出現提示時。
- 按一下 [**應用程式**] 索引標籤。
- 選取 [**顯示**] 下拉式清單欄位中的 [**我的公司擁有的應用程式**，然後按一下核取記號。
- 您會看到呼叫**b2c 副檔名-應用程式**下方所列的應用程式。 按一下 [**刪除**頁面的底部，按一下 [ **]**出現提示時。
- 再次瀏覽至 Active Directory 分機號碼，然後選取您 B2C 租用戶。
- 按一下 [在頁面底部的 [**刪除**]。 依照指示完成程序螢幕上。

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>可以取得 Azure AD B2C 做為企業版行動性套件的一部分嗎？

否，Azure AD B2C pay-as-you-go Azure 服務，而不是企業版行動性套件的一部分。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>我要如何提 Azure AD B2C 問題？

請參閱[支援的 Azure Active Directory B2C 要求的檔案](active-directory-b2c-support.md)。

## <a name="more-information"></a>詳細資訊

您也可能會想要檢閱目前的[服務限制的限制，與限制](active-directory-b2c-limitations.md)。
