<properties
    pageTitle="在您工作地點中使用 Windows 10 裝置 |Microsoft Azure"
    description="為使用者提供的功能快照和 IT、 對比的不同方式佈建和企業使用 Windows 10 中所用的裝置。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>使用 Windows 10 裝置在您工作地點

適用於︰ Windows 10 的電腦

在 Windows 10 提供三種模式，讓使用者以安全且方便的方式存取工時資源的組織。

- **Azure Active Directory 加入**（azure AD 加入） 的工作人員存取資源，例如主要是在雲端中的 Office 365。 Azure AD 加入是自助工作佈建在 Windows 10 中的新的體驗。
- **網域加入**的內部部署的應用程式與資源的投資的組織。 網域加入可提供較佳的體驗，在 Windows 10 中 Azure AD 連線時。
- **新的簡化的 BYOD 體驗**，使用者想要新增的公司帳戶或學校 Windows 和輕鬆存取個人裝置上的資源。

下表將呈現快照的使用者和 IT 系統管理員，對比的不同方式佈建和企業使用 Windows 10 中所用的裝置的功能︰

|                                                                                                                                                                 | 網域加入     | Azure AD 加入 | 個人裝置 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| 在 Windows 裝置登入公司或學校帳戶。                                                                                                                      | [是]             | [是]           | 無              |
| 使用者單一登入 (SSO) 至 Office 365 和 Azure AD 應用程式。 SSO 是一次登入存取組織的資源的能力。 | [是]             | [是]           | [是]             |
| 使用者 Kerberos/NTLM 應用程式 SSO。                                                                                                                                  | [是]             | 限制       | 可以，透過 VPN         |
| 加強的授權，好方便您用於登入 Microsoft 密碼和 Windows Hello 的公司或學校帳戶。                                                                   | [是]             | [是]           | [是]             |
| 企業 Windows 市集 （沒有 Microsoft 帳戶） 以公司或學校帳戶存取。                                                                                    | [是]             | [是]           | [是]             |
| 企業相容漫遊的使用者設定與公司或學校帳戶的裝置上。                                                                                 | [是]             | [是]           | [是]             |
| 若要限制存取組織的應用程式與組織裝置原則相容的裝置能力。                                                      | [是]             | [是]           | [是]             |
| 使用者的自助佈建裝置的 「 工作從任何位置。 」                                                                                                | 無              | [是]           | [是]             |
| 管理裝置的功能。                                                                                                                                       | 可以，透過 GP/SCCM | [是]           | [是]             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>在 Windows 10 中加入 Azure AD 加入與網域的 [使用工作擁有裝置

在 Windows 10 提供工時所擁有的裝置存取工時資源的兩種的方法︰

- Azure AD 加入
- 網域加入

 兩者都可以是有效的選項，根據組織的需求與需求。 在某些情況下，組織可能會因啟用的部署這兩種方法。

## <a name="when-to-use-azure-active-directory-join"></a>何時使用 Azure [Active Directory 加入

Azure AD 加入是新自助公司佈建在 Windows 10 中的體驗。  它被專為工作人員存取工時資源，例如主要是在雲端中的 Office 365。 精簡的方式來設定的電腦、 平板電腦和手機企業版則。 透過行動裝置管理管理裝置在 Windows 平台之間使用一致的控制項。

**使用 Azure AD 加入的可能原因如下**︰

- 您想要啟用自助佈建的裝置的出門在外工作的人員。
- 您為使用者提供平板電腦和手機等工作擁有行動裝置。
- 您想要管理 Azure AD 而不是在 Active Directory 中的一組使用者的例如季節性工作者、 承包商或學生。
- 您想要在遠端分公司工作者的結合功能提供有限的內部部署基礎結構。
- 您沒有內部部署 Active Directory。

在某些組織會使用 Azure AD 加入作為主要方式部署工作所擁有的裝置，特別是他們移轉至雲端的大部分或所有的資源。 Active Directory 和 Azure AD 的混合式組織，也可以選擇根據使用者或部門部署其中一個方法或另一個。

學與大學，例如，可能會管理 Active Directory 中的人員和 Azure AD 中的學生。 有些公司可能會想要管理遠端辦公室 」 或 「 銷售部門 Azure AD 中。 Azure AD 加入和網域聯結方法可供混合式部署組織中。 Azure AD 加入可以部署裝置在公司環境中的網域加入一個好補充。

**如果的最常用的存取權的企業資源是透過雲端，如果您的組織可能享受額外的福利**︰

- 您可以移除內部部署識別基礎結構的相依性。
- 您可以簡化您裝置的部署模型，藉由使用自助設定快速離開影像解決方案。
- 您可以使用行動裝置管理來管理在不同的平台上的所有裝置。

如需有關 Azure AD 加入的詳細資訊，請參閱[Windows 10 裝置，透過 Azure [Active Directory 加入延伸雲端功能](active-directory-azureadjoin-overview.md)。

## <a name="when-to-use-domain-join-or-keep-using-it"></a>使用網域加入 （或使用的保留） 的時機

過去 15 年，許多組織使用網域加入連線工作裝置。 其可讓使用者登入他們的裝置 Active Directory 工作或學校帳戶。 加入網域也可讓 IT 集中和完整管理這些裝置。 組織通常依賴影像方法佈建裝置和經常使用系統管理中心設定管理員 (SCCM) 或群組原則 (GP) 加以管理。

**企業應用於網域加入 （或使用的保留） 的可能原因如下**︰

- 您必須使用 NTLM/Kerberos 這些裝置部署 Win32 應用程式。
- 您需要 GP 或 SCCM/DCM 管理裝置。
- 您要繼續使用您的員工設定裝置的影像解決方案。

**在 Windows 10 中的網域加入也可讓您連線至 Azure AD 時下列優點**︰

- 強大的裝置繫結驗證與方便登入 Microsoft 密碼和 Windows Hello 的公司或學校帳戶。
- 使用工作或學校帳戶 （不需要 Microsoft 帳戶） 的裝置，企業版的 Windows 市集存取。
- 企業相容漫遊使用者設定的跨裝置可使用公司或學校帳戶 （不需要 Microsoft 帳戶）。
- 若要限制存取組織的應用程式與組織裝置原則相容的裝置能力。

如需有關 Azure AD 加入的詳細資訊，請參閱[Windows 10 裝置，透過 Azure [Active Directory 加入延伸雲端功能](active-directory-azureadjoin-overview.md)。

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>啟用加入的工作或學校的個人所擁有的裝置

若要在企業中支援 BYOD，Windows 10 會讓使用者能夠將公司或學校帳戶新增至他們的電腦、 平板電腦或手機。 使用者新增公司或學校帳戶後，裝置註冊 Azure AD，而您也可以在行動裝置管理系統的組織已設定註冊。 目錄會顯示這些裝置為 「 已註冊 」 與 「 Azure AD 加入 」。 IT administraors 可以套用不同的原則，根據這項資訊，如有需要，提供更亮觸控式個人所擁有的裝置上於工作所擁有的裝置上。

使用者可以新增工作或學校帳戶至其個人的裝置很方便。 他們可以這麼做時存取工作的應用程式第一次，或者他們可以執行手動透過 [設定] 功能表。 此帳戶會提供 SSO 組織的資源。

如需有關 Azure AD 加入的詳細資訊，請參閱[連線至 Windows 10 版的 Azure AD 網域裝置體驗](active-directory-azureadjoin-devices-group-policy.md)。

## <a name="enable-domain-join-or-azure-ad-join"></a>啟用網域聯結或 Azure AD 聯結

稍早所述的所有方法 （網域加入 Azure AD 加入與新增公司或學校帳戶） 中的 Windows 10 使用者體驗有進入點。 但是，所有需要 IT 系統管理員啟用基礎結構中的功能，才能執行體驗。

## <a name="requirements-for-deploying-azure-ad-join"></a>部署 Azure AD 加入的需求

若要部署 Azure AD 加入的任何一組使用者必須下列動作︰

- Azure AD 訂閱。
- Azure AD 進階版訂閱，例如行動裝置管理自動註冊，如果您需要更多的功能。
- 行動裝置管理，例如 Microsoft Intune 訂閱、 Office 365 的行動裝置管理或任何整合 Azure AD 與合作夥伴行動裝置管理廠商。 （請參閱本文，如需詳細資訊的結尾處的[常見問題集一節](#frequently-asked-questions)）。

如果您的設施混合式部署，我們強烈建議您部署 Azure AD Connect Azure ad 延伸內部部署目錄。

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Azure AD 加入使用網域的需求

網域加入繼續永遠具有的運作。 不過，若要取得 Azure AD 優點，您將需要下列動作︰

- Azure AD 訂閱。
- 若要將內部部署目錄延伸到 Azure AD Azure AD Connect 部署。
- 可讓網域裝置 Azure AD 條件存取原則。
- 如果您想要用來限制存取針對某些裝置可讓 「 網域 」 裝置存取原則。
- 系統管理中心設定管理員版本 1509 技術預覽，請啟用規則要求相容的裝置。 （請參閱 TechNet 文件和部落格文章。）

更多在 Windows 10 中的網域聯結的詳細資訊，請參閱[連線至 Windows 10 版的 Azure AD 網域裝置體驗](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>使用 BYOD 和 「 新增公司或學校帳戶 」 的需求

若要啟用 「 將您自己的裝置 」 (BYOD) 使用公司或學校帳戶，您需要下列動作︰

- Azure AD 訂閱。
- Azure AD 進階版訂閱，例如行動裝置管理自動註冊，如果您需要更多的功能。

## <a name="requirements-for-using-microsoft-passport"></a>使用 Microsoft 密碼的需求

若要啟用 Microsoft 密碼，您將需要下列項目︰

- 公開金鑰基礎結構 (PKI) 使用 Microsoft 密碼的憑證驗證支援。
- 使用 Microsoft 密碼的 Azure AD 加入與公司或學校帳戶的憑證驗證支援 Intune 訂閱。
- 系統管理中心設定管理員 Technical Preview 版的版本 1509年 （請參閱 TechNet 文件和部落格文章） 憑證驗證支援使用網域加入 Microsoft 的密碼。
- 在組織中啟用 Microsoft 密碼原則。

使用 PKI 或者，您可以啟用為基礎的 Microsoft 密碼來執行下列動作︰

- 部署 Windows Server 2016 「 生產預覽 1 」 Dc （不需要的網域或樹系功能等級; 每個 Active Directory 網站會滿足重複性服務的多個 Dc）。
- 設定原則，以在組織中啟用 Microsoft 密碼。

如需瞭解 Microsoft 密碼和 Windows Hello Windows 10 中的詳細資訊，請參閱 < link-to-MS-Passport-and-Windows-Hello-document >。

## <a name="frequently-asked-questions"></a>常見問題集
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Azure AD 整合的協力廠商的行動裝置管理產品？

下列廠商的產品整合與 Azure AD 整合的註冊和 Windows 10 中的條件存取︰

- 以 VMware AirWatch
- Citrix Xenmobile
- Lightspeed 行動管理員
- SOTI 內部部署行動裝置管理
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>工作地點怎麼加入 Windows 10 中？
工作地點加入已啟用 BYOD Windows 8.1 中使用。 在 Windows 10 中 BYOD 已啟用透過 「 新增公司或學校帳戶 」，這份文件之前所述。 不要將其行動裝置管理整合 Azure AD 的公司，使用者可以將透過**設定**手動管理註冊裝置 > **帳戶** > **工作的存取權**。


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>使用者可以在 Windows 10 中其網域帳戶連線 Microsoft 帳戶？
不是在 Windows 10。 Windows 8.1 網域裝置的使用者無法 「 連接 「 Microsoft 帳戶 （例如，Hotmail、 Live、 Outlook、 Xbox 等） 其啟用 SSO 等特定體驗即時服務，請使用 Windows 市集，並在裝置上漫遊的使用者設定的網域帳戶。 在 Windows 10 中已經退休 」 連線 」 功能的 Microsoft 帳戶。 使用者可以新增一或多個 Microsoft 帳戶以其他帳戶以 SSO 等 Windows 市集的消費者服務。 這是在 [**設定** > **帳戶** > **您的帳戶**。

誰從 Windows 8.1 網域裝置升級，以及誰有 Microsoft 帳戶的使用者連線，會自動連線的 Microsoft 帳戶新增至其他帳戶使用的清單。


## <a name="additional-information"></a>其他資訊
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
