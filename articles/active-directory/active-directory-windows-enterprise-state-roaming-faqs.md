<properties
    pageTitle="設定和資料漫遊常見問題集 |Microsoft Azure"
    description="提供一些解答 IT 系統管理員可能會有相關設定] 和 [應用程式的資料同步處理。"
    services="active-directory"
    keywords="企業狀態漫遊設定，windows 雲端上企業狀態漫遊的常見問題集"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="settings-and-data-roaming-faq"></a>設定和資料漫遊常見問題集

本主題回答一些 IT 系統管理員可能會有關於設定和應用程式的資料同步處理的問題。

## <a name="what-data-roams"></a>什麼資料漫遊？
**Windows 設定**︰ 內建至 Windows 作業系統的電腦設定。 一般而言，這些個人化您的電腦的設定，並包含下列主要類別︰

- *佈景主題*，包括桌面的佈景主題和工作列設定等功能。
- *Internet Explorer 設定*，包括最近開啟的索引標籤和 [我的最愛]。
- *Edge 瀏覽器設定*，例如 [我的最愛] 和 [讀取清單。
- *密碼*，包括網際網路密碼 Wi-fi 設定檔，與其他人。
- *語言喜好設定*，包括鍵盤配置、 系統語言、 日期及時間，以及更多的設定。
- *輕鬆存取功能*，例如高對比佈景主題、 朗讀程式，以及 [放大鏡]。
- *Windows 的其他設定*，例如命令提示字元中設定及應用程式的清單。


**應用程式的資料**︰ 通用的 Windows 應用程式可以撰寫設定資料至漫遊資料夾]，並將其寫入到這個資料夾的任何資料會自動同步處理。 個別的應用程式開發人員設計應用程式，利用此功能是由。 如需詳細資訊如何開發通用的 Windows 應用程式，使用漫遊，請參閱[appdata 儲存 API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx)和[Windows 8 appdata 漫遊開發人員部落格](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)。

## <a name="what-account-is-used-for-settings-sync"></a>設定同步用什麼帳戶？
在 Windows 8 和 Windows 8.1 中，設定同步處理一律使用的消費者 Microsoft 帳戶。 企業使用者必須以 Microsoft 帳戶連線到 Active Directory 網域帳戶以存取設定同步處理的功能。 在 Windows 10 中，此連線功能會取代主要/次要帳戶架構的 Microsoft 帳戶。

主要的帳戶被定義為登入 Windows 時所用的帳戶。 這可以是 Microsoft 帳戶、 Azure Active Directory (Azure AD) 帳戶、 內部部署 Active Directory 帳戶或本機的帳戶。 除了主要帳戶，Windows 10 使用者可以將一個或多個的次要雲端帳戶新增至他們的裝置。 次要帳號通常是 Microsoft 帳戶、 Azure AD 帳戶或一些其他帳戶，例如 Gmail 或 facebook 的連絡人。 這些次要帳戶提供存取等單一登入的其他服務和 Windows 市集，但不是能電源設定同步處理。

在 Windows 10 中的主要帳戶的裝置可用於設定同步處理 (請參閱[我該如何升級 Windows 8 中的 Microsoft 帳戶設定同步處理從 Azure AD Windows 10 中的設定同步處理？](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?))。

永遠不會在裝置上不同的使用者帳戶之間混合資料。 有兩種規則設定同步處理︰

- Windows 設定一律會漫遊主要的帳戶。
- 使用用來擷取應用程式的帳戶，就會標記應用程式的資料。 僅限標記主要帳戶的應用程式會同步處理。 應用程式是透過 Windows 市集或行動裝置管理 (MDM) 側載入時，會決定應用程式擁有權標記。

如果找不到應用程式的擁有者，它將漫遊主要的帳戶。 如果裝置從 Windows 8 或 Windows 8.1 升級至 Windows 10，為取得 Microsoft 帳戶來標記所有應用程式。 這是因為大部分的使用者取得 Windows 市集，透過應用程式，而且已經 Azure AD 帳戶在 Windows 10 之前不 Windows 市集支援。 如果透過離線授權安裝應用程式，則會在裝置上使用的主要帳戶標記應用程式。

>[AZURE.NOTE]  
> Windows 10 裝置的是企業擁有並連線到 Azure AD 無法再連線至的網域帳戶其 Microsoft 帳戶。 連線至網域帳戶的 Microsoft 帳戶，而且所有使用者資料同步處理的 Microsoft 帳戶 （亦即漫遊透過 Active Directory 功能與已連接的 Microsoft 帳戶的 Microsoft 帳戶） 的功能會從連線的 Active Directory 或 Azure AD 環境，連接的 Windows 10 裝置移除。

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>我該如何升級 Windows 8 中的 Microsoft 帳戶設定同步處理從 Azure AD Windows 10 中的設定同步處理？
如果您使用連接的 Microsoft 帳戶在執行 Windows 8 或 Windows 8.1 的 Active Directory 網域加入，您會透過您的 Microsoft 帳戶來同步處理設定。 升級至 Windows 10 之後，您還是會繼續同步處理使用者透過 Microsoft 帳戶的設定，只要您是在網域的使用者，以及 Active Directory 網域不會與 Azure AD 連線。

如果內部部署的 Active Directory 網域是否和 Azure AD 連線，您的裝置會嘗試同步處理設定連線 Azure AD 帳戶。 如果 Azure AD 管理員不會啟用企業狀態漫遊，您連線 Azure AD 帳戶會停止同步處理設定。 如果您是在 Windows 10 的使用者，您使用 Azure AD 身分登入，您會立即開始同步處理 windows 設定為您的系統管理員啟用透過 Azure AD 設定同步處理。

如果您儲存在您公司的裝置上的任何個人資料，您應該知道的 Windows 作業系統與應用程式的資料會開始同步處理至 Azure AD。 這會有下列影響︰

- 您的個人 Microsoft 帳戶設定會設定與沒有您的工作或學校 Azure AD 帳戶。 這是因為的 Microsoft 帳戶和 Azure AD 設定同步處理現在使用的不同的帳戶。
- 如需使用 Wi-fi 密碼、 web 認證及先前已透過連接的 Microsoft 帳戶同步處理的 Internet Explorer 我的最愛個人資料會透過 Azure AD 同步處理。


## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>如何 Microsoft 帳戶和 Azure AD 企業狀態漫遊互通性工作？
在 Windows 10 年 11 月 2015年或更新版本中，企業版狀態漫遊只支援單一帳戶一次。 如果您登入 Windows 使用公司或學校 Azure AD 帳戶時，所有的資料會同步處理透過 Azure AD。 如果您登入 windows 使用個人 Microsoft 帳戶，所有的資料會同步處理透過 Microsoft 帳戶。 通用 appdata 將漫遊在裝置上，使用只主要登入帳戶，它將漫遊才主要帳戶所擁有的應用程式授權。 通用 appdata 所擁有的任何次要帳戶應用程式不會同步處理。

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>從多個租用戶 Azure AD 帳戶同步處理設定？
當來自不同帳戶的多個 Azure AD Azure AD 租用戶都在同一個裝置上時，您必須更新裝置的登錄通訊 Azure 版權管理 (Azure RMS) 的每個 Azure AD 租用戶。  

1. 尋找 GUID 每個 Azure AD 租用戶。 開啟 [Azure 傳統入口網站，然後選取 [Azure AD 租用戶。 用戶 GUID 位於您的瀏覽器的網址列中的 URL。 例如︰ `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. 有 GUID 之後，您將需要新增登錄機碼**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<租用戶識別碼 GUID >**。
從 [**租用戶識別碼 GUID** ] 鍵，建立新多字串值 (登錄-多重-SZ) 為**AllowedRMSServerUrls**。 其資料，請指定其他 Azure 租用戶裝置存取授權分配點 Url。
3. 您可以找到授權分配點 Url 執行**取得 AadrmConfiguration**指令程式。 如果不同**LicensingIntranetDistributionPointUrl**及**LicensingExtranetDistributionPointUrl**的值，指定兩個值。 如果值是相同，只是一次指定其值。


## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>什麼是漫遊的 [設定] 選項為現有的 Windows 桌面應用程式？
漫遊只適用於通用的 Windows 應用程式。 有適用於啟用漫遊上現有的 Windows 桌面應用程式的兩個選項︰

- [桌面橋](http://aka.ms/desktopbridge)可協助您將通用的 Windows 平台現有 Windows 桌面應用程式。 從這裡開始，最小的程式碼變更將會才能利用漫遊的 Azure AD 應用程式的資料。 桌面橋提供您的應用程式身分，就需要啟用漫遊現有桌面應用程式的應用程式資料的應用程式。
- [使用者體驗虛擬化 (舒慧 V)](https://technet.microsoft.com/library/dn458947.aspx) ，可協助您建立現有的 Windows 桌面應用程式的自訂設定範本，並啟用漫遊 Win32 應用程式。 此選項時，不需要的應用程式開發人員若要變更的應用程式的程式碼。 舒慧 V 會限制為內部部署 Active Directory 漫遊的購買的數量 Microsoft 桌面最佳化套件的客戶。

系統管理員可以設定舒慧-V 漫遊 Windows 桌面應用程式的資料，藉由變更漫遊 Windows OS 設定和通用的應用程式中資料[舒慧 V 群組原則](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)，包括︰

- 漫遊 Windows 設定群組原則
- 無法同步處理 Windows 應用程式群組原則
- 在 [應用程式] 區段中漫遊的 Internet Explorer

在未來，Microsoft 可能調查進行舒慧 V 深層整合到 Windows 和擴充舒慧和 V 漫遊透過 Azure AD 雲端設定的方法。


## <a name="can-i-store-synced-settings-and-data-on-premises"></a>我能將儲存同步處理的設定和資料內部部署？
企業狀態漫遊 Azure 雲端儲存同步處理的所有資料。 舒慧 V 提供內部部署漫遊解決方案。

## <a name="who-owns-the-data-thats-being-roamed"></a>資料會被漫遊的擁有者是誰？
自己的企業透過企業狀態漫遊漫遊資料。 資料會儲存於 Azure 資料中心。 加密所有使用者資料在傳輸時會和使用 Azure RMS 雲端中的其餘部分。 這是比較 Microsoft 帳戶為基礎的設定同步處理，可先將其保留裝置加密特定機密資料例如使用者認證改進。

Microsoft 將致力於保護客戶資料。 企業使用者的設定資料會自動加密 Azure RMS 之前將其保留在 Windows 10 裝置，讓其他使用者可以讀取此資料。 如果貴組織的 Azure RMS 的付費的訂閱，您可以使用其他 Azure RMS 的功能，例如追蹤及撤銷文件、 自動保護機密資訊，包含電子郵件和管理您自己的金鑰 （「 將您自己的金鑰 」 解決方案，也就是 BYOK）。 如需有關這些功能和 Azure RMS 的運作方式的詳細資訊，請參閱[什麼是 Azure 版權管理](https://technet.microsoft.com/jj585026.aspx)。

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>管理設定特定應用程式的同步處理？
在 Windows 10 中沒有停用個別的應用程式的漫遊 MDM 或群組原則設定。 租用戶系統管理員可以停用的受管理的裝置上的所有應用程式的 appdata 同步處理，但有沒有更妥善地控制，每個應用程式或應用程式中的層級。

## <a name="how-can-i-enable-or-disable-roaming"></a>如何啟用或停用漫遊？
在 [**設定**應用程式中，移至**帳戶** > **同步處理您的設定**。 此頁面上，您可以看到漫遊設定]，用的帳戶，您可以啟用或停用個別的設定，可漫遊群組。

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>什麼是 Microsoft 的建議來啟用漫遊 Windows 10 中？
Microsoft 有幾個漫遊可用，包括漫遊使用者設定檔、 舒慧 V，以及企業狀態漫遊解決方案的不同設定。  Microsoft 是致力狀態漫遊未來版本的 Windows 企業中某項投資的。 如果您的組織不是好或習慣移動至雲端的資料，我們建議您為您的主要漫遊技術使用舒慧 V。 如果您組織需要漫遊現有的 Windows 桌面應用程式的支援，但想要移動至雲端，我們建議您使用企業狀態漫遊與舒慧 V。 雖然舒慧 V 和企業版狀態漫遊是非常類似技術，他們無法互斥。 它們彼此互補以確保您的組織提供您的使用者需要漫遊服務。  

使用企業狀態漫遊和舒慧 V 時，便會套用下列規則︰

- 企業狀態漫遊是在裝置上的主要漫遊代理程式。 用於舒慧 V 補充 「 Win32 間距]。
- 使用舒慧 V 群組原則時，應該停用舒慧 V 漫遊 Windows 設定和現代化的 UWP 應用程式資料。 這些是已涵蓋企業狀態漫遊。

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>如何是否企業狀態漫遊支援虛擬桌面基礎結構 (VDI)？
在 Windows 10 用戶端 Sku，而非伺服器 Sku 企業狀態漫遊支援。 如果的用戶端 VM 裝載在 hypervisor 台機器上您從遠端登入虛擬機器，將漫遊您的資料。 如果多個使用者共用相同的 OS，且使用者從遠端登入伺服器的完整桌面體驗，可能無法運作漫遊。 正式不支援後面的工作階段型案例。


## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>當我的組織購買 Azure RMS 使用漫遊之後，會發生什麼情況？
如果貴組織已在使用漫遊 Windows 10 中 Azure RMS 限制使用免費訂閱，購買付費的 Azure RMS 訂閱不會造成任何影響漫遊的功能的功能和設定變更都必須由您的 IT 系統管理員。

## <a name="known-issues"></a>已知的問題

- 如果您嘗試登入您的 Windows 裝置使用智慧卡或虛擬智慧卡時，設定同步處理會停止運作。 在 Windows 10 以後的更新可能會解決此問題。
- 您將需要適用於 Windows 10 （建立 10586.494 或更新版本） 的年 7 月積存更新同步處理工作的 Internet Explorer [我的最愛]。
- Windows 資訊保護受保護的資料，不會透過企業狀態漫遊同步處理。 此外，啟用 Windows 資訊保護電腦不會發生的佈景主題同步處理。 
- 在某些情況下企業狀態漫遊可能無法同步處理資料，如果 Azure 多重因素驗證設定。
    - 如果您的裝置設定為需要 Azure Active Directory 入口網站上的[多重因素驗證](multi-factor-authentication.md)，您可能無法同步處理時登入 Windows 10 裝置使用密碼的設定。 多重因素驗證設定此類型被要保護 Azure 系統管理員帳戶。 系統管理員使用者仍然可以同步處理到他們的 Windows 10 裝置使用其[Microsoft 密碼工時](active-directory-azureadjoin-passport.md)PIN 登入，或完成多重因素驗證時存取 Office 365 等其他 Azure 服務。
    - 如果管理員設定的 Active Directory Federation 服務多重因素驗證條件的存取原則，並在裝置上的存取權杖到期，同步處理可能會失敗。  請確定您登入與登出使用[Microsoft 密碼工時](active-directory-azureadjoin-passport.md)PIN 或完成存取其他 Azure 服務，例如 Office 365 時的多重因素驗證。

- 如果電腦的 Azure Active Directory 裝置自動註冊加入網域，它可能會遇到同步處理會失敗，如果電腦離線延伸一段時間，而無法完成網域驗證。 若要解決此問題，機器連線到公司網路，好讓可以繼續同步處理。


## <a name="related-topics"></a>相關的主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [啟用企業漫遊 Azure Active Directory 中的狀態](active-directory-windows-enterprise-state-roaming-enable.md)
- [群組原則和 MDM 設定同步處理設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [在 Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
