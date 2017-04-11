<properties
    pageTitle="使用條件存取規則 Azure Active Directory 中的應用程式 |Microsoft Azure"
    description="使用條件存取控制項，Azure Active Directory 檢查特定條件時驗證使用者，並允許應用程式存取。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>使用條件存取規則 Azure Active Directory 中的應用程式

條件存取規則支援的 Azure Active Directory (Azure AD)-連接的應用程式、 預先整合的同盟的軟體與服務 (SaaS) 應用程式，使用密碼單一登入 (SSO)、 線條的商務應用程式和 Azure AD 應用程式 proxy 伺服器的應用程式的應用程式。 您可以使用條件的 access 應用程式的詳細清單，請參閱[使用條件 access 啟用服務](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access)。 條件存取有效行動與桌面應用程式使用的現代化驗證。 本文中，我們會說明如何條件存取運作行動和桌面應用程式中。

您可以使用的現代化驗證的應用程式中使用 Azure AD 登入頁面。 登入頁面，使用者會提示多重因素驗證。 訊息會顯示已封鎖使用者的存取權。 讓裝置條件存取原則會評估需要驗證 Azure AD，裝置的現代化驗證。

請務必瞭解條件存取規則及的步驟，您可能需要採取其他應用程式進入點的安全性，可以使用哪些應用程式。

## <a name="applications-that-use-modern-authentication"></a>使用的現代化驗證的應用程式

> [AZURE.NOTE] 如果您在 Office 365 有對等的 Azure AD 中有一個條件存取原則，設定兩個條件的存取原則。 這會套用，例如到 Exchange Online 或 SharePoint Online 的條件的存取原則。

下列應用程式支援條件存取 Office 365 和其他 Azure AD 連線的服務應用程式︰

| 目標服務  | 平台  | 應用程式                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | 在 Windows 10|郵件/行事曆/連絡人應用程式，在 Outlook 2016、 Outlook 2013 （含現代化驗證） （含現代化驗證） 的商務用 Skype|
|Office 365 Exchange Online| Windows 8.1、 Windows 7 |Outlook 2016，Outlook 2013 （含現代化驗證）、 商務用 Skype （含現代化驗證）|
|Office 365 Exchange Online|iOS Android|  Outlook 行動應用程式|
|Office 365 Exchange Online|Mac OS X| 多重因素驗證和位置，只; 版 outlook 2016裝置的原則支援計劃供日後使用，取得企業支援規劃未來的 Skype|
|Office 365 SharePoint Online|在 Windows 10| Office 2016 應用程式、 通用的 Office 應用程式，（含現代化驗證） 的 Office 2013 OneDrive （下一步產生同步處理用戶端或 NGSC） 的商務應用程式支援的計劃供日後使用，供日後使用，SharePoint 應用程式支援規劃未來規劃 Office 群組支援|
|Office 365 SharePoint Online|Windows 8.1、 Windows 7|Office 2016 應用程式，（含現代化驗證） 的 Office 2013、 商務應用程式 （Groove 同步處理用戶端） 的 OneDrive|
|Office 365 SharePoint Online|iOS Android|  Office mobile 應用程式 |
|Office 365 SharePoint Online|Mac OS X| 多重因素驗證和位置，只; 版的 office 2016 應用程式規劃未來的裝置的原則支援|
|Office 365 Yammer|在 Windows 10、 iOS 和 Android 版 | Office Yammer 應用程式|
|Dynamics CRM|Windows 10、 Windows 8.1、 Windows 7、，在 iOS 和 Android | Dynamics CRM 應用程式|
|中服務|Windows 10、 Windows 8.1、 Windows 7、，在 iOS 和 Android | 中的應用程式|
|Azure Remote 應用程式服務|在 Windows 10、 Windows 8.1、 Windows 7、 iOS、 Android 和 Mac OS X |Azure Remote 應用程式|
|任何我的應用程式的應用程式服務|Android 和 iOS|任何我的應用程式的應用程式服務 |


## <a name="applications-that-do-not-use-modern-authentication"></a>不使用的現代化驗證的應用程式

目前，您必須使用其他方法來封鎖存取應用程式不是使用的現代化驗證。 不使用的現代化驗證的應用程式存取規則不會強制執行由條件 access。 這種主要是 Exchange 和 SharePoint 存取考量。 大部分的舊版應用程式使用舊版 access 控制通訊協定。

### <a name="control-access-in-office-365-sharepoint-online"></a>在 Office 365 SharePoint Online 中的控制存取權限
您可以使用設定 SPOTenant 指令程式來停用舊版 SharePoint 存取通訊協定。 您可以使用這個 cmdlet 來防止使用非現代驗證通訊協定，存取 SharePoint Online 資源的 Office 用戶端。

**範例命令**︰    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>在 Office 365 Exchange Online 中的控制存取權限

Exchange 使用者提供兩種通訊協定的主要類別。 檢閱下列選項，然後再選取 [最適合貴組織的原則。

-   **Exchange ActiveSync**。 根據預設，不會強制多重因素驗證與位置的條件存取原則的 Exchange ActiveSync。 您需要保護直接，設定 Exchange ActiveSync 原則，或使用 Active Directory Federation Services (AD FS) 規則封鎖 Exchange ActiveSync 存取這些服務。
-   **舊版通訊協定**。 您可以封鎖與 AD FS 舊版通訊協定。 較舊版本的 Office 用戶端，例如沒有啟用的現代化驗證與舊版 Office 的 Office 2013 此組塊存取。


### <a name="use-ad-fs-to-block-legacy-protocol"></a>使用 AD FS 封鎖舊版的通訊協定

您可以使用下列規則的範例，封鎖 AD FS 層級的舊版的通訊協定存取。 選擇 [從兩個一般設定]。

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>選項 1︰ 讓 Exchange ActiveSync]，並允許舊版應用程式，但只有在內部網路

使用 Microsoft Office 365 身分識別的平台的廠商信任 AD FS 套用下列三個規則，Exchange ActiveSync 流量和瀏覽器的現代化驗證流量擁有存取權。 舊版的應用程式已封鎖來自外部網路。

##### <a name="rule-1"></a>規則 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>規則 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>規則 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>選項 2︰ 允許 Exchange ActiveSync]，並封鎖舊版應用程式

使用 Microsoft Office 365 身分識別的平台的廠商信任 AD FS 套用下列三個規則，Exchange ActiveSync 流量和瀏覽器的現代化驗證流量擁有存取權。 舊版的應用程式會遭到封鎖從任何位置。

##### <a name="rule-1"></a>規則 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>規則 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>規則 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
