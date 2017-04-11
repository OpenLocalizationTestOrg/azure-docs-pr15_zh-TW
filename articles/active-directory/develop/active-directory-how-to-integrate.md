<properties
   pageTitle="如何與 Azure Active Directory 整合 |Microsoft Azure"
   description="好處和 Azure Active Directory 與整合的資源指南。"
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

# <a name="integrating-with-azure-active-directory"></a>整合與 Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory 提供企業級身分識別管理雲端應用程式的組織。  Azure AD 整合精簡的登入體驗，可讓您的使用者，並協助您符合 IT 原則的應用程式。

## <a name="how-to-integrate"></a>如何將整合

有數種方式與 Azure AD 整合應用程式。  利用許多或幾種情況，視您的應用程式。

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>支援 Azure AD，用來登入您的應用程式

**減少登入人事並減少支援成本。** 藉由使用 Azure AD 登入您的應用程式，您的使用者不會有多個名稱和要記住的密碼。  為開發人員]，則必須小於密碼來儲存及保護。  不需要處理忘記的密碼重設可能會有效節約了解單獨使用。  Azure AD 某些全球規模最常用雲端應用程式，包括 Office 365 與 Microsoft Azure 力量登入。  數百數百萬數百萬之組織的使用者，可能是您的使用者已登入 Azure AD。  進一步瞭解[新增支援 Azure AD 登入](../active-directory-authentication-scenarios.md)。

**簡化登設定，您的應用程式。**  在註冊您的應用程式，讓您可以預先填入您註冊表單，或將它完全移除 Azure AD 時，可以傳送使用者的基本資訊。  使用者可以註冊使用熟悉的同意體驗︰ 與社交媒體與行動應用程式中透過 Azure AD 帳戶的應用程式。  任何使用者可以登入並登入應用程式，而不需要 IT 參與 Azure AD 整合。  深入瞭解[登入向上 Azure AD 帳戶登入您的應用程式](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>瀏覽使用者、 管理使用者佈建，以及控制存取您的應用程式

**瀏覽目錄中的使用者]。**  使用圖形 API 來協助使用者搜尋並邀請其他人時，瀏覽讓組織中其他人，或授與存取權，而不需要輸入電子郵件地址。  使用者可以使用熟悉的地址書籍樣式介面，包括檢視的組織階層的詳細資料，瀏覽]。  深入瞭解[圖表 API](../active-directory-graph-api.md)。

**重複使用 Active Directory 群組與已為管理客戶的通訊群組清單。**  Azure AD 包含客戶已用於電子郵件通訊與管理存取權的群組。  使用圖形 API，重複使用這些群組，而不是需要您的客戶，來建立和管理一組另一個應用程式中的群組。  群組的資訊也可以傳送您的應用程式中登入權杖。  深入瞭解[圖表 API](../active-directory-graph-api.md)。

**使用 Azure AD，控制可存取您的應用程式。**  系統管理員和應用程式中 Azure AD 的擁有者可以指定 access 應用程式，以特定的使用者和群組。  使用圖形 API，您可以在簡報中閱讀這份清單，並用它來控制佈建和不支援的資源，並在應用程式中存取。

**使用 Azure AD 的角色以存取控制項。**  系統管理員和應用程式的擁有者可以指定使用者和群組 Azure AD 中註冊您的應用程式時，將由您定義的角色。  角色資訊會傳送至您的應用程式登入權杖，也能讀取使用圖形 API。  瞭解更多關於[使用 Azure AD 的授權](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx)。

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>使用者設定檔、 行事曆、 電子郵件、 連絡人、 檔案，以及更多的存取

**Azure AD 是 Office 365 或其他 Microsoft 商務服務授權伺服器。**  如果您登入您的應用程式或將您目前的使用者帳戶連結至 Azure AD 使用者帳戶使用 OAuth 2.0 支援支援 Azure AD，您可以要求已讀取和寫入存取權的使用者設定檔、 行事曆、 電子郵件、 連絡人、 檔案，以及其他資訊。  您可以順暢完美地事件寫入使用者的行事曆，並讀取或寫入檔案到他們的 OneDrive。  進一步瞭解[存取 Office 365 Api](https://msdn.microsoft.com/office/office365/howto/platform-development-overview)。

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>升級您的應用程式中的 Azure 及 Office 365 市場

**升級您的組織已經是使用 Azure AD 數百萬的應用程式。**  搜尋並瀏覽這些市場的使用者已使用一或更多雲端服務，使其符合雲端服務的客戶。  進一步瞭解升級您的應用程式中[Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)。

**當使用者註冊您的應用程式時，它會出現在其 Azure AD 存取面板和 Office 365 應用程式啟動器。**  使用者將能夠快速且輕鬆地返回您的應用程式之後，改善使用者的互動。  進一步瞭解[Azure AD 存取面板](../active-directory-saas-access-panel-introduction.md)。

### <a name="secure-device-to-service-and-service-to-service-communication"></a>安全性裝置-服務及服務-服務的通訊

**使用 Azure AD 的身分識別管理的服務與裝置可減少您需要撰寫程式碼，可讓 IT 來管理存取權。**  服務和裝置可以從 Azure AD 使用 OAuth 取得權杖，並使用這些權杖存取 web Api。  使用 Azure AD 可避免撰寫複雜的驗證碼。  身分識別的服務和裝置儲存於 Azure AD，因為 IT 管理索引鍵和撤銷在同一個位置，而不是由獨立應用程式中執行此動作。

## <a name="benefits-of-integration"></a>整合的優點

Azure AD 整合隨附不需要您撰寫其他程式碼的優點。

### <a name="integration-with-enterprise-identity-management"></a>企業身分識別管理 「 整合

**協助您遵守 IT 原則的應用程式。**  組織整合企業身分識別管理系統使用 Azure AD，使當人員離開組織，他們會自動會失去存取您的應用程式，而不需要採取額外步驟的 IT。  IT 管理人員可以存取您的應用程式，並決定哪些存取原則所需的範例多重因素驗證-縮減您需要撰寫程式碼遵循複雜的公司原則。  Azure AD 提供系統管理員的使用者登入您的應用程式，因此詳細的稽核記錄 IT 可以追蹤使用方式。

**Azure AD 將 Active Directory 延伸至雲端，好讓您的應用程式可以使用 AD 整合。**  世界各地的許多組織使用 Active Directory 以及其主要登入身分識別管理系統，，並要求他們使用 AD 的應用程式。  整合與 Azure AD 與 Active Directory 整合應用程式。

### <a name="advanced-security-features"></a>進階的安全性功能

**多重因素驗證。**  Azure AD 提供原生多重因素驗證。  IT 系統管理員可以要求多重因素驗證，存取您的應用程式，以便您不需要自行撰寫程式碼此支援。  進一步瞭解[多重因素驗證](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)。

**異常的登入偵測。**  Azure AD 處理多個億登增益集一天，請使用電腦學習演算法偵測可疑的活動和通知 IT 系統管理員可能發生的問題時。  藉由支援 Azure AD 登入，您的應用程式會取得此保護的優點。 進一步瞭解[檢視 Azure Active Directory 存取報表](../active-directory-view-access-usage-reports.md)。

**條件的存取權。**  多重因素驗證，除了系統管理員可以需要符合特定條件才能使用者可以登入您的應用程式。  您可以設定的條件包含 IP 位址範圍的用戶端裝置中指定的群組的成員資格，用於存取裝置的狀態。  瞭解更多有關[Azure Active Directory 條件存取](../active-directory-conditional-access.md)。

### <a name="easy-development"></a>簡易的開發

**工業標準通訊協定。**  Microsoft 將致力於支援業界標準。  Azure AD 支援 SAML 2.0 與 OpenID 連線 1.0、 OAuth 2.0 WS 同盟 1.2 驗證通訊協定。  圖形 API 是 OData 4.0 相容。  如果您的應用程式已經支援 SAML 2.0 或 OpenID 連線 1.0 通訊協定同盟登入，請新增 Azure AD 支援可乾淨的簡單。  進一步瞭解[Azure AD 支援驗證通訊協定](../active-directory-authentication-protocols.md)。

**開啟來源文件庫。**  Microsoft 提供常用的語言與速度開發平台的完整支援開啟來源文件庫。  下 Apache 2.0 授權的程式碼，而且您是免費分叉及參與專案。  進一步瞭解[Azure AD 驗證文件庫](../active-directory-authentication-libraries.md)。

### <a name="worldwide-presence-and-high-availability"></a>世界各地的目前狀態與可用性

**Azure AD 世界各地的資料中心的部署及管理，監視繞著時鐘。**  Azure AD 身分識別管理系統的 Microsoft Azure 和 Office 365，而世界各地的 28 資料中心的部署。  目錄資料複製到至少三個資料中心保證。  全域負載平衡器確保使用者存取權的 Azure AD 包含其資料，最接近的複本，並偵測到問題時自動重新傳送邀請其他地理。

## <a name="next-steps"></a>後續步驟

[開始撰寫程式碼](../active-directory-developers-guide.md#getting-started)。

[在使用 Azure AD 登使用者](../active-directory-authentication-scenarios.md)
