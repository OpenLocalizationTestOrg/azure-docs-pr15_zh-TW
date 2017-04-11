<properties
    pageTitle="Office 365 服務的條件的存取裝置原則 |Microsoft Azure"
    description="如何以裝置為基礎的狀況的詳細資料控制 Office 365 服務的存取權。 而資訊工作者 (IWs) 要從其個人裝置的工作或學校 Exchange 和 SharePoint Online 等的存取 Office 365 服務，請他們的 IT 管理員會想要的存取權會 secure.IT 管理員可以佈建企業資源時同時讓 IWs 存取服務相容的裝置上的安全性的條件的存取裝置原則。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Office 365 服務的條件的存取裝置原則

字詞，「 條件存取 」 有與其相關聯，例如多重因素驗證的使用者，驗證的多個條件裝置、 相容的裝置等。本主題主要 focusses 裝置為基礎的條件來控制 Office 365 服務的存取權。 而資訊工作者 (IWs) 要從其個人裝置的工作或學校 Exchange 和 SharePoint Online 等的存取 Office 365 服務，請他們的 IT 管理員會希望安全地存取。 IT 管理員可以佈建企業資源時同時讓 IWs 存取服務相容的裝置上的安全性的條件的存取裝置原則。 Office 365 的條件存取原則可從 Microsoft Intune 條件存取入口網站設定。

Azure Active Directory 強制執行存取 Office 365 服務的安全性的條件存取原則。 租用戶管理員可以建立條件存取原則會封鎖使用者存取 O365 服務不相容的裝置上。 使用者必須符合公司的裝置原則之前服務授與存取權。 或者，管理員也可以建立的原則，要求使用者只要註冊其裝置來存取 O365 服務。 可能會套用到組織中的所有使用者或限制為幾個目標群組和增強型一段時間，包括其他目標群組原則。

強制裝置原則的必要條件適用於使用者註冊其裝置與 Azure Active Directory 裝置註冊服務。 您可以選擇啟用裝置註冊 Azure Active Directory 裝置註冊服務的多重因素驗證 (MFA)。 MFA 建議的 Azure Active Directory 裝置註冊服務。 啟用 MFA 時，使用者註冊其裝置與 Azure Active Directory 裝置註冊服務的挑戰第二個因素驗證。

##<a name="how-does-conditional-access-policy-work"></a>條件存取原則如何運作？

當使用者要求存取至 O365 服務從支援的裝置平台時，Azure Active Directory 驗證使用者與裝置的使用者啟動的要求。及使用者符合原則設定服務時，只授與存取服務。 沒有註冊其裝置的使用者如何註冊和符合存取公司的 office 365 服務可以改善的指示進行。 IOS 和 Android 裝置上的使用者都必須註冊其裝置使用公司入口網站應用程式。 當使用者註冊其裝置時，裝置註冊的 Azure Active Directory，而註冊裝置管理和法規遵循。 客戶必須搭配 Microsoft Intune 使用 Azure Active Directory 裝置註冊服務來啟用 Office 365 服務的行動裝置管理。 裝置註冊裝置原則會強制執行時的必要條件，來存取 Office 365 服務的使用者。

當使用者成功註冊其裝置時，裝置會變成信任。 Azure Active Directory 提供單一登入存取公司的應用程式，並授與存取權的使用者要求存取權，但每次使用者要求更新存取的服務不只第一個時間的條件存取原則。 登入認證都會變更、 裝置會遺失/竊取或更新的邀請時不符合原則時，使用者將拒絕存取服務。

## <a name="deployment-considerations"></a>部署考量︰
必須使用裝置註冊 Azure Active Directory 裝置註冊的服務。

當使用者進行驗證內部部署時，是必要的 Active Directory Federation Services (AD FS) （1.0 和上面）。 多重因素驗證地點加入會失敗時身分識別提供者不支援的多重因素驗證。 例如，AD FS 2.0 不是多重因素驗證能力。 租用戶管理員必須確認內部部署 AD FS MFA 簡訊，並啟用有效的 MFA 方法時，才能啟用 MFA Azure Active Directory 裝置註冊服務。 例如，在 Windows Server 2012 R2 AD FS 有 MFA 功能。 您也必須啟用其他有效驗證 (MFA) 上的方法 AD FS server Azure Active Directory 裝置註冊服務啟用 MFA 之前。 如需有關 AD FS 中支援的 MFA 方法的詳細資訊，請參閱 AD FS 設定其他的驗證方法。

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

問︰ 什麼是不受支援的裝置平台的預設排除原則？

答︰ 在目前的時間，條件存取原則選擇性地上強制執行 iOS 和 Android 裝置上的使用者。 其他裝置平台上的應用程式是，根據預設，受 iOS 和 Android 裝置的條件存取原則。 租用戶管理員可以不過，選擇覆寫全域原則不允許使用者不受支援的平台上的存取權。
為 [開啟指南延伸條件存取原則，包括 Windows 其他平台版的使用者。

問︰ Office 365 服務的條件存取原則會延伸至瀏覽器為基礎的應用程式 （例如，OWA、 瀏覽器為基礎的 SharePoint） 的當。

答︰ 在目前的時間，條件存取 office 365 服務只限豐富型的應用程式在裝置上。 它是從瀏覽器存取服務的使用者延伸條件存取原則藍圖。
