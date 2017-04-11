<properties
    pageTitle="延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能 |Microsoft Azure"
    description="提供詳細的說明如何在 Windows 10 裝置可以利用 Azure AD 加入至取得登錄的 Azure Active Directory。"
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
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能

## <a name="what-is-azure-active-directory-join"></a>什麼是 Azure [Active Directory 加入？
Azure Active Directory 加入 （Azure AD 加入） 是註冊公司所擁有的裝置，若要啟用集中的管理裝置的 Azure Active Directory 中的功能。 其可讓使用者，例如員工與學生以連線至企業雲端透過 Azure Active Directory。 同時這可讓簡化的 Windows 部署與組織的應用程式與資源的存取權，從任何 Windows 裝置，公司所擁有與個人擁有 (BYOD)。

Azure AD 加入適用於企業的雲端第一個/雲端--通常是小型及中型大小的企業沒有內部部署的 Windows Server Active Directory 基礎結構。 在大型組織，卻無法執行傳統網域聯結 （的行動裝置，例如），或針對主要需要存取 Office 365 或其他 Azure AD SaaS 應用程式的使用者的裝置上使用該、 Azure AD 加入可以，也會。

雖然傳統的網域加入仍會提供最佳內部部署體驗也能加入網域的裝置上，就有一個 Azure AD 加入適合的裝置，無法加入網域。 Azure AD 加入也適用於管理雲端中的使用者。 它會使用行動裝置管理功能，而不是使用傳統的網域管理工具，例如群組原則] 和 [系統管理中心設定管理員 (SCCM)。

![公司的裝置與內部部署的 Active Directory 與 Azure AD 的個人裝置的概觀](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## <a name="why-should-enterprises-adopt-azure-ad-join"></a>為什麼應該企業採用 Azure AD 加入？

* **主要是在雲端的企業**︰ 如果您已移動或移動的方法，您可以縮減您所需的內部部署，並且想要執行更多雲端中的模型，Azure AD 加入可能優點。 或許您已建立 Azure AD 帳戶，以手動方式，或透過同步處理您的內部部署 Active Directory 中。 兩種方式，您帳戶中有 Azure AD，並用您可以登入 Windows 10。 您的使用者可以將自己的電腦加入 Azure AD 透過其中一個現成的體驗 (OOBE) 或 [設定] 功能表。 加入之後，使用者會喜歡單一登入 (SSO) 存取雲端之類的資源 Office 365]，在瀏覽器中或 Office 應用程式中。

* **教育機構**︰ 我們通常聆聽相關的案例的內容之一是教育機構有兩個使用者類型︰ 學生與教職員。 教職員成員被視為長期成員的組織，因此建議，建立內部部署帳戶。 但學生 shorter-term 成員的組織，因此可以在 [Azure AD 管理。 這表示可在雲端，而不是儲存於內部部署推入目錄小數位數。 這也表示學生可以使用其 Azure AD 帳戶登入 Windows，並存取 Office 365 的資源，在瀏覽器中或 Office 應用程式中。

* **零售企業**︰ 我們的客戶聽到另一種情況是他們想要更輕鬆地管理季節性的同事。  同樣地，長期、 全職員工的帳戶是通常建立內部網域的電腦上的帳戶。 但季節性工作者 shorter-term 成員的組織，因此希望管理其位置使用者的授權可以更輕鬆地四處移動。 在 Office 365 授權的雲端建立這些使用者帳戶，可讓使用者取得 Windows 和 Office 應用程式 Azure AD 帳戶登入的優點。 同時，您有更多的彈性，其授權後離開。
* **其他企業**︰ 雖然您維護您內部部署的 Active Directory 中的使用者，您可能仍的好處是 Azure AD 加入的使用者。 這是因為 Azure AD 提供簡化聯結的體驗，有效裝置管理、 自動的行動裝置管理註冊和單一登入功能 Azure ad 和內部部署資源。  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>沒有 Azure AD 加入提供哪些功能？
使用 Azure AD 加入，您可以實現下列︰

* **自我佈建的公司所擁有的裝置**︰ 搭配 Windows 10，使用者可以在現成的體驗，而不 IT 參與設定全新，這些裝置。


* **新式表單因素支援**︰ Azure AD 加入沒有傳統的網域加入功能的裝置上的運作。  


* **現有的組織帳戶支援**︰ 建立和維護不再需要使用者個人 Microsoft 帳戶公司發出的裝置上取得最佳體驗，與 Windows 8 所顯示的一樣。 他們可以改為使用其現有的公司帳戶中 Azure AD。 大多數組織會基本上代表使用者可以設定和使用相同的認證，他們使用存取 Office 365 登入 Windows。


* **自動在行動裝置管理註冊**︰ 裝置自動註冊 Azure AD 連線時，行動裝置管理。 此程序適用於 Microsoft Intune 與合作夥伴的行動裝置管理解決方案。 裝置管理完成時使用 Intune，IT 系統管理員可以監視器/管理 Azure AD 加入裝置同時 SCCM 管理主控台中的網域的裝置。


* **單一登入公司資源**︰ 使用者享受單一登入從 Windows 桌面應用程式並在雲端，例如 Office 365 和千分位 Azure AD 透過[Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md)驗證所依賴的商務應用程式的資源。 Azure AD 加入公司所擁有的裝置也很享受在 SSO 內部部署資源裝置已在公司網路，且無論在何處時這些資源透過[Azure AD 應用程式 Proxy](https://msdn.microsoft.com/library/azure/Dn768219.aspx)公開。


* **OS 狀態漫遊**︰ 協助工具設定、 網站、 Wi-fi 密碼及其他設定之間已同步處理公司所擁有的裝置而不需要個人 Microsoft 帳戶。


* **可供企業的 Windows 市集**︰ Windows 市集支援應用程式和 Azure AD 帳戶授權。 組織可以大量授權應用程式，並使其提供給組織中的使用者。

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>不同的裝置如何與 Azure AD 加入運作？

| 公司的裝置 （加入至內部部署的網域）                                                                                                                                                                                         | 公司的裝置 （加入至雲端）                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 個人裝置                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 使用者可以使用工作認證帳戶登入 Windows （如 [立即執行）。                                                                                                                                                                        | 使用者可以使用登入 windows Azure AD 中管理工作認證。 這是相關的公司中的裝置三種情況︰ 1) 的組織沒有 Active Directory 內部部署 （例如，小型企業版）。 2) 組織不會建立在 Active Directory 中的所有使用者帳戶 （例如，學生、 顧問，或季節性同事的帳戶不 Active Directory 中建立）。 3) 的組織無法加入 （內部部署） 的網域，例如電話或平板電腦執行 Mobile SKU （例如，次要裝置移至工廠/零售 floor） 的公司裝置。 Azure AD 加入支援加入公司的裝置的受管理與同盟的組織。 | 使用者登入 Windows 以其個人的 Microsoft 帳戶認證 （沒有變更）。                                                |
| 使用者可存取漫遊設定和企業版 Windows 市集。 這些服務與公司帳戶搭配使用，而且不需要個人 Microsoft 帳戶。 需要 Azure ad 連線其內部部署的 Active Directory 的組織。                                        | 使用者可以執行自助設定。 他們可以瀏覽首次執行體驗 (FRX) 透過其公司帳戶有 IT 佈建另一種裝置，不過這兩種方法都支援。                                                                                                                                                                                                                                                                                                                                                                             | 使用者在 Active Directory 或 Azure AD，可以輕鬆地新增管理公司帳戶。                                                      |
| 使用者會有 SSO 能夠從桌面應用程式、 網站及資源，包括內部部署資源和 Azure AD 用於驗證的雲端應用程式。                                                                                                            | 裝置已自動登錄企業目錄 (Azure AD)，並自動註冊在行動裝置管理]。 （azure AD 進階功能）。                                                                                                                                                                                                                                                                                                                                                                                                                                                  | 使用者可以 SSO 跨應用程式和網站/資源此的公司帳戶使用。                                              |
| 使用者可以將其個人的 Microsoft 帳戶，而不會影響企業資料存取其個人的圖片和檔案。 （漫遊設定繼續使用其公司帳戶）。Microsoft 帳戶啟用 SSO，以及不再磁碟機漫遊的設定。  | 使用者可以執行自助密碼重設 (SSPR) 上 winlogon，表示它們可以重設忘記的密碼。 （azure AD 進階功能）。                                                                                                                                                                                                                                                                                                                                                                                                                                   | 使用者可以存取企業 Windows 市集，使他們可以擷取並其個人的裝置上使用線條的商務應用程式。 |                                                               |


## <a name="additional-information"></a>其他資訊
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [不需要密碼透過 Microsoft 密碼驗證身分識別](active-directory-azureadjoin-passport.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
