<Properties
    pageTitle="Azure Active Directory 條件存取 |Microsoft Azure"  
    description="使用 Azure Active Directory 中的條件存取控制時，檢查特定條件的 access 應用程式進行驗證。"  
    services="active-directory"
    keywords="條件的 access 應用程式、 條件存取 Azure AD、 安全存取公司的資源，條件存取原則"
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
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的條件存取   

Azure Active Directory (Azure AD) 條件存取控制功能提供簡單的方法來協助保護內部部署和雲端中的資源。 多重因素驗證可以防範風險等的條件存取原則竊取和 phished 認證。 其他條件存取原則可讓您組織的資料安全。 例如，除了需要認證，您可能必須原則等 Microsoft Intune 可以存取您組織的機密服務註冊在行動裝置管理系統的唯一裝置。


## <a name="prerequisites"></a>必要條件

Azure AD 條件存取是[Azure Active Directory 進階版](http://www.microsoft.com/identity)的功能。 每個使用者存取應用程式已套用的條件存取原則，必須要有 Azure AD 進階版授權。 您可以進一步瞭解[未授權的使用者報表](https://aka.ms/utc5ix)中的授權要求。


## <a name="how-is-conditional-access-control-enforced"></a>如何強制條件存取控制？  

使用就地條件存取控制項，Azure AD 檢查特定條件您設定使用者存取應用程式。 符合存取要求之後，使用者進行驗證，並可以存取應用程式。  

![條件存取概觀](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>條件

以下是您可以將加入的條件存取原則的條件︰

- **群組成員資格**。 控制使用者存取權的群組中的成員資格。

- **位置**。 使用引動程序多重因素驗證，使用者的位置，當使用者不信任網路上時使用組塊控制項。

- **裝置平台**。 使用的裝置平台，例如 iOS、 Android、 Windows Mobile 或 Windows 中，做為套用原則的條件。

- **啟用裝置的**。 裝置狀態中啟用或停用，會驗證裝置評估期間。 如果您停用在遺失或竊取裝置在目錄中的，就可以不符合原則需求。

- **登入與使用者的風險**。 您可以使用條件存取風險原則[Azure AD 身分識別保護](active-directory-identityprotection.md)。 條件存取風險原則協助讓您根據風險事件和異常的登入活動的組織換頁保護。


## <a name="controls"></a>控制項

以下是您可以使用強制執行的條件存取原則的控制項︰

- **多重因素驗證**。 您可以要求到多重因素驗證強式驗證。 Azure 多重因素驗證或使用內部部署多重因素驗證提供者，加上 Active Directory Federation Services (AD FS)，您可以使用多重因素驗證。 使用多重因素驗證，可協助避免未經授權的使用者，可能會得到的存取權的有效的使用者認證以存取資源。

- **封鎖**。 您可以套用條件，例如封鎖使用者存取權的使用者位置。 例如，您可以封鎖存取使用者不信任網路上時。

- **相容的裝置**。 您可以在裝置層級設定條件存取原則。 您可能會設定原則，以便只加入網域電腦或行動裝置的註冊在行動裝置管理應用程式，可以存取您組織的資源。 例如，您可以檢查裝置符合規範，請使用 Intune，然後回報強制 Azure ad 當使用者嘗試存取應用程式。 詳細瞭解如何使用 Intune 保護應用程式和資料的詳細資訊，請參閱[保護應用程式與 Microsoft Intune 的資料](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune)。 您也可以使用 Intune 強制執行保護資料遺失或竊取裝置。 如需詳細資訊，請參閱[協助保護您的完整或選擇性抹除使用 Microsoft Intune 的資料](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)。

## <a name="applications"></a>應用程式

您可以強制應用程式層級的條件的存取原則。 設定內部部署或雲端中的應用程式與服務的存取層級。 原則會直接套用至網站或服務。 在瀏覽器，並存取服務應用程式的存取，原則會強制執行。


## <a name="device-based-conditional-access"></a>裝置型條件的存取

您可以從裝置的註冊的 Azure AD，且其符合特定條件的應用程式限制存取。 裝置型條件存取防止使用者嘗試存取資源的組織的資源︰

- 未知或未受管理的裝置。
- 不符合您的組織設定的安全性原則的裝置。

您可以設定原則根據這些需求︰

- **加入網域的裝置**。 設定原則，以限制加入至內部部署的 Active Directory 網域，且，也包含已註冊 Azure AD 裝置存取。 此原則適用於 Windows 桌面、 膝上型電腦及企業平板電腦。
如需有關如何設定網域的裝置 Azure AD 自動註冊的詳細資訊，請參閱[設定自動註冊的 Windows Azure Active Directory 網域的裝置](active-directory-conditional-access-automatic-device-registration-setup.md)。

- **相容的裝置**。 設定原則，以限制存取標示**相容**管理系統目錄中的裝置。 此原則可確保符合安全性原則，例如強制裝置上的檔案加密的裝置，允許存取。 若要從下列裝置限制存取，您可以使用此原則︰

    - **Windows 網域的裝置**。 受管理的系統管理中心組態管理員 （在目前的分支） 部署的混合式設定。
    - **Windows 10 行動裝置的公司或個人的裝置**。 Intune，或支援的協力廠商的行動裝置管理系統管理。
    - **iOS 和 Android 裝置**。 管理 Intune。


存取受裝置為基礎的應用程式的使用者，憑證授權單位原則必須符合此原則需求將裝置存取應用程式。 如果嘗試拒絕存取不符合原則需求在裝置上。

如何設定 Azure AD 裝置為基礎的憑證授權單位原則的相關資訊，請參閱[設定裝置條件存取原則的 Azure Active Directory 連線應用程式](active-directory-conditional-access-policy-connected-applications.md)。

## <a name="resources"></a>資源

請參閱下列資源類別和文件若要深入瞭解設定貴組織的條件的存取權。


### <a name="multi-factor-authentication-and-location-policies"></a>多重因素驗證和位置原則

- [Azure AD 連線應用程式根據群組、 位置及多重因素驗證原則條件 access 快速入門](active-directory-conditional-access-azuread-connected-apps.md)
- [支援的應用程式](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>裝置型條件的存取

- [Azure Active Directory 連線應用程式設定裝置條件存取原則存取控制](active-directory-conditional-access-policy-connected-applications.md)
- [設定自動註冊的 Windows Azure Active Directory 網域的裝置](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Azure Active Directory 存取方面的問題的疑難排解](active-directory-conditional-access-device-remediation.md)
- [使用 Microsoft Intune 保護遺失或竊取裝置上的資料](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>保護根據 [登入風險的資源

-   [Azure AD 身分識別保護](active-directory-identityprotection.md)

### <a name="next-steps"></a>後續步驟

- [條件的存取權的常見問題集](active-directory-conditional-faqs.md)
- [技術參照](active-directory-conditional-access-technical-reference.md)
