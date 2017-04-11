<properties
   pageTitle="Azure 身分識別管理安全性概觀 |Microsoft Azure"
   description=" Microsoft 身分識別和存取管理解決方案說明 IT 保護存取應用程式與資源跨公司的資料中心，然後放入雲端，啟用的驗證多重因素驗證等條件存取原則的其他層級。 本文提供的核心概觀 Azure 安全性功能的身分識別管理協助。 "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Azure 身分識別管理安全性概觀

Microsoft 身分識別和存取管理解決方案說明 IT 保護存取應用程式與資源跨公司的資料中心，然後放入雲端，啟用的驗證多重因素驗證等條件存取原則的其他層級。 透過進階安全性報告、 稽核及提醒有助於減少潛在安全性問題監控可疑的活動。 [Azure Active Directory 進階版](../active-directory/active-directory-editions.md)提供單一登入數以千計的雲端 (SaaS) 應用程式，並在您執行內部部署存取 web 應用程式。

安全性優點的 Azure Active Directory (AD) 包含的功能︰

- 建立及管理單一身分識別，為每位使用者在混合式部署企業，保留使用者、 群組及裝置同步
- 提供單一登入應用程式的功能包括數以千計的預先整合式 SaaS 應用程式
- 啟用 access 應用程式的安全性強制執行規則多重因素驗證的兩個內部部署與雲端應用程式
- 佈建安全遠端存取透過 Azure AD 應用程式 Proxy 的內部部署 web 應用程式

本文的目標是核心的提供概觀 Azure 安全性功能的身分識別管理協助。 我們也會提供可讓每項功能的詳細資料，因此您可以瞭解更多的文件的連結。  

本文著重於下列核心 Azure 身分識別管理功能︰

- 單一登入
- 反向 proxy
- 多重因素驗證
- 安全性監視、 通知及電腦學習型報表
- 消費者身分識別與存取權的管理
- 裝置註冊
- 有權限的身分識別管理
- 身分識別保護
- 混合式身分識別管理

## <a name="single-sign-on"></a>單一登入

單一登入 (SSO) 所代表的意義能夠存取的所有應用程式和資源，您需要執行商務登入一次使用單一使用者帳戶。 登入之後，您可以存取所有您需要而不需要驗證應用程式 （例如輸入密碼） 一次。

許多組織依賴為 Office 365、 方塊和 Salesforce 等使用者生產力的服務 (SaaS) 應用程式的軟體。 以往 IT 人員所需的個別建立及更新每個 SaaS 應用程式中的使用者帳戶，使用者必須請記住，每個 SaaS 應用程式的密碼。

Azure AD 延伸內部部署的 Active Directory 至雲端，讓使用者能夠使用不只登入他們的網域的裝置和公司的資源，其主要的組織帳戶，但也所有網頁和 SaaS 應用程式所需的工作。

不只使用者沒有管理多組使用者名稱和密碼，可以自動能夠或取消能夠根據組織群組和其狀態為員工應用程式存取。 Azure AD 介紹安全性和存取管理控制項，可讓您集中 SaaS 應用程式中管理使用者的存取權。

深入瞭解︰

- [單一登入的概觀](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [什麼是應用程式存取和單一登入與 Azure Active Directory？](../active-directory/active-directory-appssoaccess-whatis.md)
- [Azure Active Directory 單一登入整合 SaaS 應用程式](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>反向 proxy

Azure AD 應用程式 Proxy 可讓您發佈內部部署的應用程式，例如[SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US)網站、 [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)及[IIS](http://www.iis.net/)-內您私人的網路應用程式與網路以外的使用者提供的安全存取。 應用程式 Proxy 提供多種類型的內部部署 web 應用程式，使用千位分 SaaS 應用程式的支援 Azure AD 遠端存取和單一登入 (SSO)。 員工可以登入您的應用程式首頁上自己的裝置，並透過此雲端 proxy 驗證。

深入瞭解︰

- [啟用 Azure AD 應用程式 Proxy](../active-directory/active-directory-application-proxy-enable.md)
- [發佈應用程式使用 Azure AD 應用程式 Proxy](../active-directory/active-directory-application-proxy-publish.md)
- [單一登入與應用程式 Proxy](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [使用條件的存取權](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>多重因素驗證
Azure 多重因素驗證 (MFA) 是安全性的需要使用一個以上的驗證方法，並將關鍵的第二個層級新增到使用者登增益集和交易的驗證方法。 MFA 可協助保護資料和會議的簡單登入程序的使用者需求時的應用程式存取。 它提供強式驗證透過一系列的驗證選項 — 電話、 文字訊息或行動應用程式通知或驗證碼和 3rd 廠商 OAuth 權杖。

深入瞭解︰

- [多重因素驗證](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [什麼是 Azure 多重因素驗證？](../multi-factor-authentication/multi-factor-authentication.md)
- [Azure 多重因素驗證的運作方式](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>安全性監視、 通知及電腦學習型報表

安全性監視及提醒及電腦學習型報告，找出不一致的存取模式可協助您保護您的業務。 若要取得完善的整合與安全性貴組織的目錄，您可以使用 Azure Active Directory 的存取和使用情況報告。 此資訊，目錄管理員可以更有效地決定，好讓他們可以累積規劃來降低風險可能位於可能的安全性風險的位置。

在 Azure 傳統入口網站中報告分類以下列方式︰

- 異常報表 – 包含登入我們發現異常的事件。 我們的目標是讓您知道這類活動，並可讓您能夠進行判斷是否可疑事件。
- 整合式應用程式報表-提供獲得深入見解到雲端應用程式在您的組織中的使用方式。 Azure Active Directory 提供整合數以千計的雲端應用程式。
- 錯誤報告 – 指出佈建外部應用程式的帳戶時，可能會發生錯誤。
- 使用者特定報表-顯示裝置/登特定使用者的活動資料。
- 活動記錄 – 過去 24 小時、 過去 7 天，或過去 30 天，以及群組活動變更及密碼重設和註冊活動中包含所有稽核的事件的記錄。

深入瞭解︰

- [檢視您存取及使用方式的報表](../active-directory/active-directory-view-access-usage-reports.md)
- [快速入門 Azure [Active Directory 報告](../active-directory/active-directory-reporting-getting-started.md)
- [Azure Active Directory 報表指南](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>消費者身分識別與存取權的管理

Azure Active Directory B2C 是縮放到數百個數百萬個身分識別的可用性、 全域、 身分識別管理服務的消費者具應用程式。 在行動電話整合和 web 平台。 使用者可以登入可自訂的體驗到所有的應用程式使用其現有的社交帳戶或建立新的認證。

過去，應用程式開發人員想要登入並登入到應用程式的使用者皆會編寫自己的程式碼。 他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。 Azure Active Directory B2C 提供您的組織消費者身分識別管理整合應用程式協助保護、 標準的平台和大量的可延伸原則更好的方法。

當您使用 Azure Active Directory B2C 時，使用者可以註冊您的應用程式藉由使用其現有社交帳戶 （Facebook、 Google、 Amazon、 LinkedIn） 或建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）。

深入瞭解︰

- [什麼是 Azure Active Directory B2C？](https://azure.microsoft.com/services/active-directory-b2c/)
- [Azure Active Directory B2C 預覽︰ 登入設定，並登入您的應用程式中的使用者皆](../active-directory-b2c/active-directory-b2c-overview.md)
- [Azure Active Directory B2C Preview︰ 類型的應用程式](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>裝置註冊

Azure AD 裝置註冊是裝置[條件存取](../active-directory/active-directory-conditional-access-on-premises-setup.md)案例的基礎。 當註冊裝置之後時，Azure Active Directory 裝置註冊會提供可用於驗證裝置，當使用者登入身分識別的裝置。 經過驗證的裝置，並屬性的裝置，然後可用來強制執行條件存取原則會裝載於內部部署與雲端的應用程式。

結合與行動裝置管理 (MDM) 解決方案，例如 Intune，Azure Active Directory 中的裝置屬性是以其他裝置的相關資訊更新。 這個選項可讓您建立強制執行存取從裝置，以符合您的安全性和法規遵循標準的條件存取規則。

深入瞭解︰

- [Azure Active Directory 裝置註冊快速入門](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [設定使用 Azure Active Directory 裝置註冊內部部署條件存取權限](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [自動裝置註冊 Azure Active Directory Windows 的網域的裝置](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>有權限的身分識別管理
Azure Active Directory (AD) 有權限的身分識別管理可讓您管理、 控制，和監視您有權限的身分識別與 Azure AD 中的資源，以及其他 Microsoft 線上服務，例如 Office 365 或 Microsoft Intune 存取。

有時候，使用者需要執行 Azure 或 Office 365 的資源或其他 SaaS 應用程式中的權限作業。 這通常表示的組織必須為其授與永久權限的存取 Azure AD 中。 這是持續增加安全性風險的雲端裝載的資源，因為組織無法完全監控這些使用者以他們的管理員權限的執行。 此外，如果受到擁有存取權的使用者帳戶，該一個違反可能會影響整體雲端安全性。 Azure AD 權限的身分識別管理協助解決這類風險。

Azure AD 權限的身分識別管理可讓您︰

- 查看哪些使用者 Azure AD 管理員
- 視需要啟用，」 在 「 Office 365 和 Intune，例如 Microsoft Online Services 的管理權限
- 系統管理員工作分派中取得系統管理員存取歷程記錄 」 及 「 修訂的相關的報表
- 取得權限的角色的存取權的相關的警示

深入瞭解︰

- [Azure AD 權限的身分識別管理](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Azure AD 角色的權限的身分識別管理](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure 的 AD 有權限的身分識別管理︰ 如何新增或移除使用者的角色](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>身分識別保護
Azure AD 身分識別保護功能是一安全性服務，提供將風險事件與可能會影響您組織的身分識別的弱點合併的檢視。 身分識別保護運用現有 Azure Active Directory 的異常偵測功能 （可透過 Azure AD 異常活動報表），並會介紹新即時偵測異常的風險事件類型。

深入瞭解︰

- [Azure Active Directory 身分識別保護](../active-directory/active-directory-identityprotection.md)
- [頻道 9: Azure AD 與身分識別放映︰ 身分識別保護預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>混合式身分識別管理

內部部署和雲端，建立單一使用者身分識別的驗證及所有的資源，隨時隨地的授權，範圍包括 Microsoft 的身分識別的方式。

深入瞭解︰

- [混合式身分識別白皮書 （英文)](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Active Directory 小組部落格](https://blogs.technet.microsoft.com/ad/)
