<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-定義資料保護策略 |Microsoft Azure"
    description="您可以定義資料保護策略混合式身分識別解決方案以符合您所定義的業務需求。"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>定義資料保護策略混合式身分識別解決方案

在此工作中，您會定義您混合式身分識別的方案符合企業需求在中所定義的資料保護策略︰

- [決定資料保護需求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [決定內容管理需求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [決定存取控制需求](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [決定應計需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>定義資料保護選項
所述，是[判斷目錄同步處理的需求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)，Microsoft Azure AD 可以與您 Active Directory 網域服務 (AD DS) 同步處理會位於內部部署。 此整合可以讓組織運用 Azure AD 驗證使用者的認證時，嘗試存取公司的資源。 這可以完成這兩種方法︰ 在其餘內部部署和雲端中的資料。  Azure AD 中資料的存取權需要透過安全性 token 服務 (STS) 的使用者驗證。

驗證，使用者主要名稱 (UPN) 閱讀的 「 驗證 」 token 和複寫的磁碟分割，並決定容器對應到使用者的網域。 使用者存在、 啟用的狀態，以及角色資訊是由授權系統來決定是否以您在這個工作階段中輸入此使用者的授權要求的存取權的目標租用戶。 特定授權的動作 （特別是，建立使用者、 重設密碼） 建立租用戶系統管理員可以使用管理法規或調查的稽核記錄。

透過網際網路連線到 Azure 儲存體您內部部署資料中心的移動資料不一定永遠合適資料量、 頻寬或其他考量。 [Azure 儲存匯入/匯出服務](../storage/storage-import-export-service.md)提供的硬體選項放在/擷取大量的 blob 儲存體中的資料。 其可讓您直接傳送到雲端運算子將內容上傳您儲存的帳戶，或其可以下載 Azure 資料至您的磁碟機，若要返回您 Azure 資料中心[BitLocker 加密](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2)硬碟機。 只有加密的磁碟會接受此程序 （使用服務本身工作安裝期間所產生的 BitLocker 鍵）。 BitLocker 鍵可 Azure 分開，因此提供登出頻金鑰共用。

由於資料在傳輸時會可能需要不同的案例中的位置，也會會知道的 Microsoft Azure[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)隔離租用戶的流量從另一個，採用量值，例如主機和來賓層級防火牆、 IP 封包篩選、 連接埠封鎖和 HTTPS 結束點與相關項目。 不過，大部分的 Azure 的內部通訊，包括基礎結構的基礎結構和基礎結構客戶 （內部部署），也會加密。 另一個重要的案例是內 Azure 資料中心; 通訊Microsoft 管理網路，以確保沒有 VM 可以模擬或竊聽在另一個的 IP 位址。 存取 Azure 儲存體或 SQL 資料庫]，或是連線至雲端服務時，會使用 TLS/SSL。 如此一來，客戶系統管理員負責取得 TLS/SSL 憑證，並將其部署到他們的租用戶基礎結構。 透過加密的通訊協定，例如 HTTPS、 SSL/TLS，或其他人可以保護資料流量虛擬機器中的相同的部署之間或之間移動單一部署透過 Microsoft Azure 虛擬網路的租用戶。

根據回答的問題，[判斷資料保護](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)需求，您應該可以決定要如何保護您的資料，以及混合式部署識別方案幫助您在上的。 下表顯示支援 Azure 所使用的每個資料保護案例的選項。


| 資料保護選項         | 在雲端中的其餘部分 | 在將內部部署 | 在傳輸時會 |
|---------------------------------|----------------------|---------------------|------------|
| BitLocker 磁碟機加密      | X                    | X                   |            |
| 若要將加密的資料庫的 SQL Server | X                    | X                   |            |
| VM-VM 加密             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
閱讀，若要深入瞭解每個 Azure 服務的符合認證的[Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)的[功能的規範](https://azure.microsoft.com/support/trust-center/services/)。
資料保護的選項使用多層級的方法，因為這些選項之間的比較不適用此工作。 請確定您要使用的資料將會每省/市可用的所有選項。

## <a name="define-content-management-options"></a>定義內容管理選項
使用 Azure AD 管理混合式部署識別基礎結構的優點之一是從使用者的觀點來看完全透明程序。 使用者會嘗試存取共用的資源、 資源需要驗證，使用者必須以取得權杖和存取資源的 Azure ad 傳送驗證要求。 整個程序的情況背景，不需要使用者互動。 您也可授予的權限[] 群組中](active-directory-manage-groups.md#getting-started-with-access-management)的使用者，才可用於執行某些常見的動作。

通常是資料的隱私權需要考慮的組織要求資料分類方案。 如果目前的內部部署基礎結構已使用的資料分類，可能是利用使用者的身分識別的主要存放庫為 Azure AD。 常見的工具是使用內部部署資料分類的 Windows Server 2012 R2 稱為[資料分類工具組](https://msdn.microsoft.com/library/Hh204743.aspx)。 這項工具可協助識別、 分類及保護您私人的雲端中的檔案伺服器上的資料。 您也可運用在 Windows Server 2012，完成這項工作中[自動檔案分類](https://technet.microsoft.com/library/hh831672.aspx)。

如果貴組織沒有任何位置的資料分類，但必須保護機密的檔案，但不新增新伺服器內部部署，他們可以使用 Microsoft [Azure 版權管理服務](https://technet.microsoft.com/library/JJ585026.aspx)。  Azure RMS 使用的是加密，身分識別，並且授權原則，以協助保護您的檔案及電子郵件，且其運作方式與在多個裝置上 — 手機、 平板電腦及電腦。 Azure RMS 是雲端服務，因為沒有明確設定與其他組織的信任，您可以與他們共用受保護的內容之前，先需要。 如果已有 Office 365 或 Azure AD 目錄，並自動支援跨組織共同作業。 您也可以只目錄屬性 Azure RMS 需要使用 Azure Active Directory 同步處理服務 (AAD Sync) 或 Azure AD Connect 您內部部署的 Active Directory 帳戶，支援常見的身分識別的同步處理。

內容管理很重要的一部分瞭解誰正在存取的資源，因此強大的記錄功能是很重要的身分識別管理解決方案。 Azure AD 提供記錄超過 30 天包括︰

- 變更角色的成員資格 (例如︰ 新增至全域管理員角色的使用者)
- 認證的更新 (例如︰ 變更密碼)
- 管理網域 (例如︰ 驗證自訂網域，請將網域移除)
- 新增或移除應用程式
- 管理使用者 (例如︰ 新增、 移除、 更新使用者)
- 新增或移除授權

>[AZURE.NOTE]
請閱讀[Microsoft Azure 安全性和稽核記錄管理](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)来進一步瞭解 Azure 中的記錄功能。
根據回答的問題，[判斷內容管理](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)需求，您應該可以決定要如何管理混合式身分識別方案中的內容。 簡訊的整合與 Azure AD 公開表 6] 中的所有選項時，請務必以定義的更多適用於您的業務需求。

| 內容管理選項                                                               | 優點                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 缺點                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 集中式內部部署 （Active Directory 版權管理伺服器）                      | 完全控制負責分類資料伺服器基礎結構 <br> 在 Windows Server，不需要額外授權的訂閱中的內建功能 <br> 可以在混合式部署的情況下 Azure AD 與整合 <br> Microsoft Online services，例如 Exchange Online 和 SharePoint Online，以及 Office 365 的支援資訊版權管理 (IRM) 的功能 <br> 支援內部部署 Microsoft 伺服器產品，例如 Exchange Server 與 SharePoint Server 的執行 Windows Server 和檔案分類基礎結構 (FCI) 檔案伺服器。 | 更高，進行的維修作業 （保留設定更新、 設定與升級），因為 IT 擁有伺服器 <br> 需要伺服器基礎結構內部部署<br> Doesn'tleverage Azure 功能原本                                     |
| 集中放在雲端 (Azure RMS)                                                     | 更容易管理相較於內部部署解決方案 <br> 可以整合與 AD DS 混合式情境中操作 <br>  Azure AD 與完全整合 <br> 不需要在伺服器上內部部署服務才能 <br> 支援內部部署 Microsoft 伺服器產品，例如 Exchange Server、 SharePoint、 伺服器，以及執行 Windows Server 和檔案分類，基礎結構 (FCI) 的檔案伺服器 <br> IT，可以讓其租用戶的金鑰 BYOK 功能完整的控制權。                                                                                    | 您的組織必須有雲端訂閱支援 RMS 的 <br> 您的組織必須有 Azure AD 目錄，支援 RMS 使用者驗證                                                                                  |
| 混合式 （Azure RMS 與整合，內部部署 Active Directory 權限管理伺服器） | 這種情況累積兩個，集中於內部部署和雲端中的優點。                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | 您的組織必須有雲端訂閱支援 RMS 的 <br> 您的組織必須有 Azure AD 目錄，支援 RMS，使用者驗證 <br> 需要 Azure 雲端服務之間的連線，並在內部部署基礎結構 |

## <a name="define-access-control-options"></a>定義存取控制選項
利用驗證授權及存取控制 Azure AD，您可以讓您的公司使用單一登入 (SSO)，如下圖所示，同時能讓使用者與合作夥伴使用管理中心的身分識別存放庫中提供的功能︰

![](./media/hybrid-id-design-considerations/centralized-management.png)

集中式的管理和完整與另一個目錄整合

單一登入數以千計的 SaaS 應用程式提供的 azure Active Directory 與內部部署 web 應用程式。 請閱讀[Azure Active Directory federation 相容性清單︰ 可用來實作單一登入的協力廠商身分識別提供者](https://msdn.microsoft.com/library/azure/jj679342.aspx)如需詳細資訊 SSO 協力廠商的測試 microsoft 的文章。 此功能可讓組織時保留的身分識別與存取權的管理控制實作各種 B2B 案例。 不過，期間 B2B 設計程序瞭解是很重要，將使用的合作夥伴，驗證如果這個方法 Azure 支援的驗證方法。 目前，以下是支援 Azure AD 的方法︰

- 安全性判斷提示標記語言 (SAML)
- OAuth
- Kerberos
- 權杖
- 憑證


>[AZURE.NOTE] 閱讀[Azure Active Directory 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn151124.aspx)知道每個通訊協定和 Azure 其功能的相關的更多詳細資料。

使用 Azure AD 支援，企業版行動應用程式可以使用相同的簡單行動服務驗證體驗，讓員工登入他們使用其公司的 Active Directory 認證的行動應用程式。 使用此功能，Azure AD 支援為身分識別提供者在行動電話服務同時與其他身分識別提供者已支援 （包括 Microsoft 帳戶、 Facebook 識別碼、 Google 識別碼和 Twitter 的識別碼）。 如果內部部署應用程式使用位於公司的 AD DS 的使用者認證，則應該透明從協力廠商和來自雲端的使用者存取。 您可以管理使用者的條件存取控制 （雲端） web 應用程式、 web API、 Microsoft 雲端服務，第 3 的廠商 SaaS 應用程式及原生 （行動） 用戶端應用程式]，並優點安全性、 稽核、 一個位置集中報告。 不過，建議您驗證此非生產環境中或一段有限的使用者。


>[AZURE.TIP] 請務必提及 Azure AD 起來不太群組原則為 AD DS 具有。 若要強制執行原則的裝置，您必須在行動裝置管理解決方案，例如[Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)。

一旦使用 Azure AD 驗證使用者時，就必須評估，使用者必須將其存取層級。 使用者必須在資源的存取層級而有所不同，雖然 Azure AD 可以新增額外的安全性層來控制存取權一些資源，您必須同時請注意，資源本身也可以讓自己存取控制清單分開，例如檔案位於 [在檔案伺服器的存取控制。 下圖摘要列出存取控制，您可以在混合式部署的案例中的層級︰

![](./media/hybrid-id-design-considerations/accesscontrol.png)


圖 X 中有顯示圖表中的每個互動代表一個可以覆蓋 Azure AD 的存取控制案例。 以下有每個案例的說明︰

1.條件的應用程式存取裝載於內部部署︰ 您可以使用註冊的裝置存取原則設定為搭配 Windows Server 2012 R2 使用 AD FS 的應用程式。 如需有關的內部部署的條件存取設定的詳細資訊，請參閱[使用 Azure Active Directory 裝置登錄的內部部署條件存取的設定](active-directory-conditional-access-on-premises-setup.md)。
2.存取控制 Azure 管理入口網站︰ Azure 也有使用 RBAC （角色以存取控制項） 來控制存取 [管理] 入口網站的功能。 這個方法可讓您以限制個別他有權存取 Azure 管理入口網站後，可以執行的作業的公司。 藉由使用 RBAC 來控制入口網站的存取權，IT 管理員 ca 委派存取權限使用下列存取管理方法︰

 - 群組為基礎的角色指派︰ 您可以從您的本機 Active Directory 可以同步處理的 Azure AD 群組指派存取。 這可讓您運用現有的投資的組織所做的工具和程序來管理群組。 您也可以使用 Azure AD 進階版的委派的群組管理功能。
 - 使用內建於 Azure 中的角色︰ 您可以使用下列三種角色，擁有人、 參與者及讀取程式，以確保使用者和群組有權限只需要完成工作的工作。
 - 微調資源的存取權︰ 您可以指派角色給使用者和群組用於特定訂閱、 資源] 群組中或個別的 Azure 資源，例如網站或資料庫。 如此一來，您可以確保使用者擁有其所需的所有資源的存取權和管理不需要的資源沒有存取。

>[AZURE.NOTE] 閱讀[Azure 中的角色型存取控制](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/)知道更多有關此功能的詳細資料。 適用於開發人員建立應用程式，並想要的自訂 access 控制項，則也使用 Azure AD 應用程式角色的授權。 檢閱此[WebApp-RoleClaims-DotNet 範例](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet)如何建立您的應用程式使用此功能。

3.條件存取 Microsoft Intune 與 Office 365 應用程式︰ IT 管理員可以佈建企業資源時同時讓資訊工作者存取服務相容的裝置上的安全性的條件的存取裝置原則。 如需詳細資訊，請參閱[Office 365 服務的條件的存取裝置原則](active-directory-conditional-access-device-policies.md)。

4.條件存取 Saas 應用程式︰[此功能](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx)可讓您設定每個應用程式多重因素驗證存取規則及封鎖的不是在受信任的網路上的使用者存取權的能力。 您可以套用多重因素驗證規則會指派到應用程式，或僅適用於使用者指定的安全性群組內的所有使用者。 如果他們從 IP 位址，在公司內部網路存取應用程式，可能會排除使用者從多重因素驗證需求。

存取控制的選項使用多層級的方法，因為這些選項之間的比較不適用此工作。 請確定您要使用適用於您來控制存取您的資源需要每個案例的所有選項。

## <a name="define-incident-response-options"></a>定義事件回應選項
Azure AD 可協助 IT 潛在安全性風險監視使用者的活動，IT 可以環境中運用 Azure AD 存取和使用狀況報表取得完善的整合與您組織的目錄安全性功能的身分識別。 此資訊，IT 管理員可以更有效地決定，好讓他們可以累積規劃來降低風險可能位於可能的安全性風險的位置。  [Azure AD 進階版的訂閱](active-directory-get-started-premium.md)有一組安全性報告，可讓 IT 來取得這項資訊。 [Azure AD 報表](active-directory-view-access-usage-reports.md)分類，如下所示︰

- **異常報表**︰ 包含登入我們發現異常的事件。 我們的目標是讓您知道這類活動，並可讓您能夠進行判斷是否可疑事件。
- **整合式應用程式的報表**︰ 提供獲得深入見解到雲端應用程式在您的組織中的使用方式。 Azure Active Directory 提供整合數以千計的雲端應用程式。
- **錯誤報告**︰ 指出佈建外部應用程式的帳戶時，可能會發生錯誤。
- **特定使用者的報表**︰ 特定使用者的活動資料中顯示裝置/符號。
- **活動記錄**︰ 過去 24 小時、 過去 7 天，或過去 30 天，以及群組活動的變更及密碼重設和註冊活動中包含的所有稽核的事件記錄。

>[AZURE.TIP]
另一個報表，也可協助您處理案例事件回應小組是[使用者遺漏的認證與](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx)報表。  此報表會呈現這些遺漏的認證] 清單和您的租用戶之間的任何符合項目。

其他重要中可用的事件回應調查 Azure AD 內建的報表，且︰

- **重設密碼的活動**︰ 提供給管理員重設密碼如何主動組織中所使用的細節。
- **密碼重設登錄活動**︰ 提供獲得深入見解的使用者已註冊的重設密碼，其方法與所選的方法。
- **群組活動**︰ 提供群組的變更的歷程記錄 (365&ex︰ 新增或移除的使用者)，啟動 [Access] 面板中。

除了用於運用在事件回應調查過程中，IT 可以也 Azure AD 進階版的核心報表功能運用稽核報告，以取得資訊，例如︰

- 變更角色的成員資格 (例如︰ 新增至全域管理員角色的使用者)
- 認證的更新 (例如︰ 變更密碼)
- 管理網域 (例如︰ 驗證自訂網域，請將網域移除)
- 新增或移除應用程式
- 管理使用者 (例如︰ 新增、 移除、 更新使用者)
- 新增或移除授權

事件回應選項使用多層級的方法，因為這些選項之間的比較不適用此工作。 請確定您要使用適用於要求您做為貴公司的事件回應程序的一部分使用 Azure AD 報告功能每個案例的所有選項。

## <a name="next-steps"></a>後續步驟
[決定混合式身分識別管理工作](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
