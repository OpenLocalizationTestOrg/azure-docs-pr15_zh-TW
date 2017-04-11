<properties
   pageTitle="實作 Azure Active Directory |Microsoft Azure"
   description="實作安全的混合式網路架構使用 Azure Active Directory 的方式。"
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>實作 Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明與 Azure Active Directory 提供雲端身分驗證整合內部部署 Active Directory (AD) 網域及樹系的最佳作法。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 此參考架構會使用資源管理員，Microsoft 建議新的部署。

您可以使用目錄及身分識別的服務，例如 AD 目錄服務 (AD DS) 來驗證身分識別提供。 這些識別可以隸屬於使用者、 電腦、 應用程式] 或其他資源安全性界限部分。 您也可以裝載目錄及身分識別服務於內部部署，但在應用程式的項目 Azure 中的所在位置的混合式案例可以是更有效率延伸到雲端這項功能。 這種方法可協助降低傳送驗證所造成的延遲和雲端的本機的授權要求回執行內部部署目錄及身分識別的服務。

Azure 提供兩種解決方案在雲端實作目錄及身分識別的服務︰

1. 您可以使用[Azure Active Directory (AAD)] [azure-active-directory]在雲端建立 AD 網域，並將其連結至內部部署 AD 網域。 AAD 可讓您設定單一登入 (SSO) 執行透過雲端存取的應用程式的使用者。 AAD 利用[Azure AD Connect] [azure-ad-connect]執行內部部署複製物件及身分識別保存在內部部署存放庫中至雲端。

    您也可以實作 AAD，而不使用內部部署目錄。 在此情況下，AAD 作為主要的所有身分識別資訊而不是從內部部署目錄複製包含資料來源。

    請注意，在雲端的目錄****內部部署目錄的副檔名。 而是包含相同的物件及身分識別的複本。 這些項目內部部署所做的變更會複製到雲端，但在中進行變更**不是**雲端複寫回內部部署的網域。

    >[AZURE.NOTE] Azure Active Directory 進階版支援回寫功能的使用者的密碼，讓內部部署使用者重新執行在雲端的自助密碼重設。 這是唯一的 AAD 同步處理至內部部署目錄的資訊。 更多 AAD 和其功能的不同版本的相關資訊，請參閱[Azure Active Directory 版本][aad-editions]。

2. 您可以從您的內部部署資料中心部署 VM 執行 AD DS 中網域控制站 Azure 延伸現有 AD 基礎結構 （包括 AD DS 和 AD FS）。 這個方法最常見的案例的內部部署網路和 Azure 虛擬網路位置連線 VPN 及/或 ExpressRoute 連線。 此方案也支援雙向複寫讓您在中進行變更的雲端及內部部署，是最適當的地方。

此結構解釋解決方案 1。 如需有關第二個方案的詳細資訊，請參閱[延伸至 Azure 的 Active Directory][guidance-adds]。

此結構的一般的使用案例包括︰

- 提供使用者執行 SaaS 及其他應用程式在雲端，使用相同的認證他們所指定的內部部署的應用程式 SSO。

- 發佈內部部署到雲端提供給遠端屬於您組織的使用者存取的 web 應用程式。

- 實作自助功能，使用者，重設密碼，等委派管理群組。 

    >[AZURE.NOTE] 這些功能可以控制由系統管理員透過群組原則。

- 內部網路和 Azure 虛擬網路裝載雲端應用程式的情況下直接不會使用 VPN 通道或 ExpressRoute 電路連結。

您應該注意 AAD 不提供 AD 的所有功能。 例如，AAD 目前只會處理使用者驗證，而不是電腦驗證。 某些應用程式和服務，例如 SQL Server，可能需要電腦驗證在此情況下不適用此方案。 此外，AAD 可能無法適用於系統元件可能移轉跨上部署/雲端邊界的位置，而不是只複製。

> [AZURE.NOTE] Azure Active Directory 的運作方式的詳細說明，觀看[Microsoft [Active Directory 說明][aad-explained]。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示重要的元件，在這種架構。 更多 Azure 中的工作量元素的詳細資訊，請參閱[執行 Vm N 層架構在 Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] 為求，此圖表中只顯示 AAD，直接關聯的連接，並不會說明網頁瀏覽器要求重新導向或其他通訊協定驗證與身分識別同盟程序時，可能會發生的相關的流量。 例如，通常是透過瀏覽器中，存取 web 應用程式的使用者 （內部部署或遠端），且 web 應用程式可能會無障礙重新導向驗證透過 AAD 要求的網頁瀏覽器。 驗證之後，可以傳遞要求回 web 應用程式搭配使用的適當的身分識別的資訊。

[![0]][0]

- **Azure Active Directory 租用戶**。 這是您的組織所建立的 AAD 的執行個體。 將其做為雲端應用程式的簡單的目錄服務 (它會保留在部署從複製的物件 AD)，並提供服務的身分識別。

- **網頁層子網路**。 子網路保留實作自訂雲端應用程式開發由您的組織和其 AAD 可以做為身分識別代理人的 Vm。

- **內部部署 AD DS 伺服器**。 您的組織可能已有現有的目錄服務，例如 AD DS。 您可以同步的項目 （例如使用者和群組的資訊） 與 AAD，才能使用這項資訊來驗證身分識別 AAD AD DS 目錄中。

- **Azure AD Connect 同步處理伺服器**。 這是在內部部署電腦所執行的 Azure AD Connect 同步處理服務。 您可以使用 Azure AD Connect 軟體，以安裝此服務。 Azure AD Connect 同步處理服務同步處理資訊 （使用者、 群組、 連絡人等） 保存在內部部署 AD 以 AAD 雲端中。 例如，您可以佈建或 deprovision 內部部署的群組和使用者，這些變更會傳播到 AAD。 Azure AD Connect 同步處理服務會傳送資訊至 AAD 租用戶。

    >[AZURE.NOTE] 基於安全性理由，不會直接在 AAD 儲存使用者的密碼。 而 AAD 保留雜湊每個密碼。 這是足以驗證使用者的密碼。 如果使用者需要重設密碼，這必須是執行內部部署和新的雜湊傳送至 AAD。 AAD 進階版包含可以自動化，讓使用者可以重設自己的密碼這項工作的功能。

## <a name="recommendations"></a>建議

本節摘要列出實作 AAD，如下所示的建議。

- AD 連線
- 安全性

### <a name="azure-ad-connect-sync-service-recommendations"></a>Azure AD Connect 同步處理服務建議

同步處理被為了確保雲端中的使用者身分識別資訊會與內部部署保留一致。 Azure AD Connect 同步處理服務的目的是保持一致性。 下列點摘要實作 Azure AD Connect 同步處理服務的建議︰

- 您應該之前實作 Azure AD Connect 同步處理，決定您組織的同步處理需求 (要從哪兩個網域，同步處理和頻率。 [判斷目錄同步處理需求]的文件[aad-sync-requirements]說明您應該考慮的點。

- 您可以執行 VM Azure AD Connect 同步處理服務，或在電腦裝載於內部部署。 根據 AD 目錄中的資訊可後執行初始同步處理 AAD, Azure AD Connect 同步處理服務載入不高。 使用 VM，可讓您更輕鬆地縮放伺服器 （來決定是否需要監視器活動的[監控考量](#monitoring-considerations)一節所述）。

- 如果您有多個內部部署網域樹系中時，您可以儲存並同步處理至單一 AAD 承租人整個樹系的資訊 （這是建議的方式）。 篩選出現在多個網域，使每個身分識別一次出現在 AAD，而不是被複製為這可能會導致不一致的資料會在同步處理的身分識別的資訊。 這個方法需要執行多個 Azure AD Connect 同步處理伺服器。 如需詳細資訊，請參閱在[拓撲考量](#topology-considerations)] 區段中的多個 AAD 案例。 

- 使用篩選來限制中 AAD 儲存只，這是必要的資料。 例如您的組織可能不想儲存 AAD 非作用中或非個人帳戶的相關資訊。 篩選群組為基礎、 網域為基礎、 OU 型或屬性為基礎，而您可以結合篩選，以產生更複雜的規則。 例如，您可能會選取同步處理的物件保留在網域中選取的屬性中有特定的值。 詳細資訊，請參閱[Azure AD Connect 同步處理︰ 設定篩選][aad-filtering]。

- 若要實作可用性 AD Connect 同步處理服務，請執行次要暫存伺服器。 如需詳細資訊，請參閱[拓撲考量](#topology-considerations)一節。

### <a name="security-recommendations"></a>安全性的建議

下列項目摘要實作 AAD，視貴組織的需求而定的主要安全性建議︰

- 管理使用者的密碼。

    如果您使用的 AAD premium 版本，您可以執行自訂安裝的 Azure AD Connect 啟用密碼回寫功能 AAD 從您的內部部署目錄︰

    [![9]][9]

    此功能可讓使用者重設自己從 Azure] 入口網站中的密碼，但檢閱貴組織的密碼的安全性原則之後，才應該啟用。 例如，您可以限制哪些使用者可以變更他們的密碼，您可以依需求自訂密碼管理體驗。 如需詳細資訊，請參閱[自訂密碼管理]，以符合您組織的需求][aad-password-management]。

- 維護保護內部部署的應用程式，可以從外部存取。

    使用 Azure AD 應用程式 Proxy 透過 AAD 的外部使用者從內部部署的 web 應用程式提供的控制的存取。 這種方法可以不將其設為公開直接到網際網路保護您的應用程式。 Azure 目錄中有有效的認證的使用者就可以連絡您的應用程式。 如需詳細資訊，請參閱文章[Azure 入口網站中啟用應用程式 Proxy][aad-application-proxy]。

- 可疑的活動的註冊、 監視 AAD。

    請考慮使用 AAD 進階版 P2 版本，其中包括 AAD 身分識別保護。 身分識別保護使用自動調整電腦學習演算法和 heuristics 偵測異常行為及風險可能表示身分識別已洩露的事件。 例如，就可以偵測到潛在異常的活動，例如不規則登入活動登增益集，從未知的來源或可疑的活動，IP 位址或登增益集可能感染的裝置。 使用此資料，身分識別保護會產生報告及可讓您調查這類風險事件，以及要花適當補救或降低動作的警示。 如需詳細資訊，請參閱[Azure Active Directory 身分識別保護][aad-identity-protection]。

    您也可以使用 AAD Azure 入口網站中的報告功能來監控可疑郵件及其他安全性相關活動系統中發生的。 AAD 可以產生一系列的摘要報表︰

    [![17]][17]

    如需有關如何使用這些報表的詳細資訊，請參閱[Azure Active Directory 報告指南][aad-reporting-guide]。

## <a name="topology-considerations"></a>拓撲考量

如果您使用 AAD 整合內部部署目錄，設定 Azure AD Connect 實作拓撲最符合貴組織的需求。 Azure AD Connect 支援拓撲包括下列各項︰

- **單一樹系、 單一 AAD 目錄**。 在此拓撲中，使用 Azure AD Connect 同步處理物件，在單一的一或多個網域中的身分識別資訊內部部署與單一 AAD 租用戶樹系。 這是預設拓撲實作快速 Azure AD Connect 的安裝。

    [![1]][1]

    沒有建立多個 Azure AD Connect 同步處理伺服器連線至相同的 AAD 租用戶的相同的內部部署樹系不同的網域，除非您正在執行 Azure AD Connect 同步處理伺服器中臨時模式 （請參閱下方的 [執行伺服器）。

- **多個樹系、 單一 AAD 目錄**。 如果您有一個以上的內部部署樹系，請使用此拓撲。 您可以合併彙算身分識別的資訊，讓每個唯一的使用者會顯示一次在 AAD 目錄中，即使一個以上的樹系中有相同的使用者。 所有樹系使用相同的 Azure AD Connect 同步處理伺服器。 Azure AD Connect 同步處理伺服器沒有任何網域，但必須使用可從所有樹系︰

    [![2]][2]

    在此拓撲，不要使用不同的 Azure AD Connect 同步處理連線至單一 AAD 承租人的每個內部部署樹系的伺服器。 如果使用者有一個以上的樹系中，這可能導致 AAD 中的重複的身分識別資訊。

- **多個樹系、 個別拓撲**。 這種方法可讓您從不同的樹系的身分識別資訊合併到單一 AAD 租用戶。 如果您結合樹系不同的組織 （合併或收購，例如） 後，每位使用者的身分識別資訊保存在一個樹系，此策略很有用︰

    如果在 Gal 同步處理，一個樹系中的使用者可能會出現在另一個為連絡人。 如果您的組織有，例如 Forefront 識別管理員 2010年或 Microsoft 身分識別管理員 2016年實作 GALSync，會發生這項目。 您可以在這個案例中，指定使用者應由其*郵件*屬性。 您也可以符合使用*ObjectSID*和*msExchMasterAccountSID*屬性的身分識別。 這是如果您有一個或多個資源樹系停用的帳戶。

- **執行伺服器**。 在此組態中，您的第一個同時執行 Azure AD Connect 同步處理伺服器的第二個執行個體。 此結構例如支援案例︰

    - 高的可用性。

    - 測試和部署新 Azure AD Connect 同步處理伺服器的設定。

    - 介紹新的伺服器，然後解除委任舊的設定]。 

    在下列情況下，第二個執行個體中*臨時模式*執行。 伺服器在其資料庫中，記錄匯入的物件及同步處理資料，但不是將資料傳遞到 AAD。 只有當您停用暫存模式時，才會伺服器開始資料寫入 AAD，以及開始執行密碼寫入-備份到內部部署目錄適當的位置︰

    [![4]][4]

    如需詳細資訊，請參閱[Azure AD Connect 同步處理︰ 操作工作及考量][aad-connect-sync-operational-tasks]。

- **多個 AAD 目錄**。 建議您建立的組織中的單一 AAD 目錄，但可能會需要的磁碟分割資訊在個別的 AAD 目錄的情況。 在此情況下，您應該確保的每個物件從內部部署樹系只會出現在一個 AAD 目錄，以避免同步處理和密碼回寫功能的問題。

    若要執行這種情況下，設定個別 Azure AD Connect 同步處理伺服器的每個 AAD 目錄，並使用篩選，每個 Azure AD Connect 同步處理伺服器一組互斥物件的操作︰ 

    [![5]][5]

如需有關這些拓撲的詳細資訊，請參閱[Azure AD Connect 的拓撲][aad-topologies]。

## <a name="user-sign-in-considerations"></a>使用者登入考量

根據預設，AAD 服務會假設提供相同的密碼，使用內部部署，以及 Azure AD Connect 同步處理伺服器設定密碼同步處理內部部署的網域和 AAD 之間的使用者登入。 許多組織，這是適當，但您應該考慮貴組織的現有的原則和基礎結構。 例如︰

- 貴組織的安全性原則可能會禁止同步處理至雲端的密碼雜湊。

- 從網域存取雲端資源聯結公司網路的電腦時，可能會要求使用者在 （不會提示其他密碼），會遇到順暢 SSO。

- 您的組織可能已經有 ADFS 或協力廠商同盟提供者部署。 您可以設定 AAD 使用此基礎結構實作驗證及 SSO 而不是使用密碼保留在雲端中的資訊。

如需詳細資訊，請參閱[Azure AD 連線使用者的登入選項][aad-user-sign-in]。

## <a name="azure-ad-application-proxy-considerations"></a>Azure AD 應用程式 proxy 考量

使用 Azure AD 提供存取內部部署的應用程式。

- 使用應用程式 proxy 連接器管理 Azure AD 應用程式 proxy 元件的內部部署 web 應用程式的方式公開。 應用程式 proxy 連接器開啟 Azure AD 應用程式 proxy 輸出網路連線。 遠端使用者要求傳閱從 AAD 透過此連線到 web 應用程式。 這個機制，就不必在內部部署的防火牆，減少貴組織所公開攻擊點開啟連入連接埠。

如需詳細資訊，請參閱[發佈應用程式使用 Azure AD 應用程式 proxy][aad-application-proxy]。

## <a name="object-synchronization-considerations"></a>物件同步處理考量

Azure AD Connect 的預設設定同步處理您的本機 AD 目錄根據一組規則，請參閱指定的物件[Azure AD Connect 同步處理︰ 了解的預設設定][aad-connect-sync-default-rules]。 只有符合這些規則會同步處理，其他人會被略過的物件。 例如，使用者物件必須唯一*sourceAnchor*屬性，而且必須填入*accounEnabled*屬性。 未同步處理使用者物件沒有*sAMAccountName*屬性，或*AAD_*或*MSOL_*以該文字的開頭。 Azure AD Connect 套用至多個規則使用者、 連絡人、 群組、 ForeignSecurityPrincipal，與電腦的物件。 如果您要修改預設一組規則，請使用同步處理規則編輯器安裝 Azure AD Connect (也記錄在[Azure AD Connect 同步處理︰ 了解的預設設定][aad-connect-sync-default-rules])。

您可以定義自己的篩選，以限制供網域或 OU 進行同步處理的物件。 實作更複雜的自訂篩選，如下所述或者[Azure AD Connect 同步處理︰ 設定篩選][aad-filtering]。

## <a name="security-considerations"></a>安全性考量

若要從非預期的來源拒絕驗證要求使用條件存取控制︰

- 觸發[Azure 多重因素驗證 (MFA)] [azure-multifactor-authentication]使用者嘗試連線不受信任的位置 (例如在網際網路上) 而不是受信任的網路。

- 使用 [裝置平台類型 (iOS Android 中，在 Windows Mobile Windows) 的使用者，來判斷存取原則應用程式和功能。

- 錄製的使用者的裝置，啟用或停用狀態，並將這項資訊合併到存取原則檢查項目。 例如，遺失或竊取使用者的電話時，應該錄製已停用，防止用來存取。

- 控制項的層級的存取權的使用者，以群組成員資格。 使用[AAD 動態成員資格規則][aad-dynamic-membership-rules]以簡化群組管理。 如簡短的運作方式概觀，請參閱[簡介動態群組的成員資格][aad-dynamic-memberships]。

- 將條件存取風險原則搭配 AAD 身分識別保護提供進階的保護根據異常的登入活動或其他事件。

如需詳細資訊，請參閱[Azure Active Directory 條件存取][aad-conditional-access]。

## <a name="scalability-considerations"></a>延展性考量

延展性解決 AAD 服務和 Azure AD Connect 同步處理伺服器的設定︰

- AAD 服務，您沒有設定任何選項，來實作延展性。 AAD 服務支援延展性根據複本。 AAD 實作單一的主要複本，寫入的控點，以及多個唯讀次要複本。 AAD 無障礙會重新導向嘗試的寫入對次要複本至主要的複本。 AAD 提供一致性。 主要複本所做的變更會傳播到第二個的複本。 大部分作業 AAD 和讀取，而不是寫入，此架構佳。

    如需詳細資訊，請參閱[Azure AD︰ 我們地理重複、 可用性、 分散式雲端目錄的顯示進階設定][aad-scalability]。

- Azure AD Connect 同步處理伺服器，您必須決定您是從您的本機目錄同步處理可能多少物件。 如果您有小於 100000 物件時，您可以使用預設的 SQL Server Express LocalDB 軟體提供 Azure AD Connect。 如果您有較大的數字的物件，您應該安裝生產版本的 SQL Server，並執行 Azure AD Connect 指定應該使用現有的 SQL Server 執行個體的自訂安裝。

## <a name="availability-considerations"></a>可用性考量

與延展性疑慮 AAD 服務，以及 Azure AD Connect 的設定，範圍包括顯示狀態︰

- 在設計 AAD 服務可提供高可用性。 沒有使用者設定的可用選項。 地理分散式並執行多個資料中心與自動化容錯移轉世界各地的模式。 如果無法使用的資料中心，AAD 確定目錄資料可以在兩個以上的更多地域分散的資料中心的執行個體存取。

    >[AZURE.NOTE] AAD 基本和進階版服務的保證至少 99.9%可用性 SLA。 有可用的 AAD 層沒有 SLA。 如需詳細資訊，請參閱[Azure Active Directory SLA][sla-aad]。

- 若要增加 Azure AD Connect 同步處理伺服器的可用性您可以在臨時模式中，執行第二個執行個體的[拓撲考量](#topology-considerations)一節所述。 

    此外，如果您不使用隨附 Azure AD Connect 的 SQL Server Express LocalDB 執行個體，則您應該考慮可用性的 SQL Server。 請注意，只有可用性方案支援 SQL 叢集。 Azure AD Connect 不支援等鏡像永遠上的解決方案。

    其他考量維護 Azure AD Connect 同步處理伺服器，以及如何復原失敗後的可用性的詳細資訊，請參閱[Azure AD Connect 同步處理︰ 操作工作和考量-損毀修復][aad-sync-disaster-recovery]。

## <a name="management-considerations"></a>管理考量事項

有兩個方面管理 AAD:

1. 管理 AAD 雲端中。

2. 維護 Azure AD Connect 同步處理伺服器。

AAD 提供管理網域和雲端中的目錄下列選項︰

- [Azure Active Directory PowerShell 模組][aad-powershell]。 如果您需要指令碼一般 Azure AD 系統管理工作等使用者管理、 網域管理以及設定單一登入，請使用本單元。

- Azure AD 管理刀 Azure 入口網站中。 此刀提供互動式管理檢視的目錄，並可讓您控制及設定的 AAD 多數方面。

    [![10]][10]

Azure AD Connect 安裝下列的工具，若要維持從您的內部部署電腦 Azure AD Connect 同步處理服務︰

- Microsoft Azure Active Directory 連線主控台。 這項工具可讓您修改 Azure AD Sync 伺服器的設定，自訂如何發生同步處理、 啟用或停用暫存模式，並切換使用者登入模式 （您可以啟用 AD FS 登入使用您的內部部署基礎結構）。

- 同步處理服務管理員。 使用此工具中的 [*作業*] 索引標籤，管理同步處理程序和偵測程序的任何部分是否有失敗。 您可以使用這項工具，以手動方式同步處理觸發程序。 

    [![12]][12]

    [*連接器*] 索引標籤可讓您控制網域連線 (內部部署和雲端中) 的同步處理引擎附加至︰

    [![13]][13]

-  同步處理規則編輯器]。 您可以使用這項工具，來自訂時的內部部署目錄和雲端中的 AAD 之間複製物件轉換的方式。 這項工具可讓您指定其他屬性及物件的同步處理，並篩選，以判斷哪一個執行個體應該或不會同步。

    如需詳細資訊，請參閱在文件中的 [同步處理規則編輯器] 區段[Azure AD Connect 同步處理︰ 了解的預設設定][aad-connect-sync-default-rules]。

頁面[Azure AD Connect 同步處理︰ 變更預設設定的最佳作法][aad-sync-best-practices]包含的其他資訊及管理 Azure AD Connect 的秘訣。

## <a name="monitoring-considerations"></a>監控考量

健康監視使用代理程式安裝於內部部署的數列來執行︰

- Azure AD Connect 安裝代理程式擷取同步處理的相關資訊。 使用 Azure 入口網站中的 Azure Active Directory 連線狀況刀監控健康狀況和 Azure AD Connect 的效能。 如需詳細資訊，請參閱[使用 Azure AD 連線狀況的同步處理][aad-health]。

- 若要監控 AD DS 網域及從 Azure 目錄的狀況，請在內部部署網域中的電腦上安裝 Azure AD 連線狀況 AD DS 代理程式。 Azure AD DS 監視器入口網站中使用 Azure Active Directory 連線狀況刀。 如需詳細資訊，請參閱[使用 Azure AD 連線健康狀況與 AD DS][aad-health-adds]

- 安裝 Azure AD 連線健全狀況監控 AD FS 於內部部署上, 執行的健康狀況和 Azure 入口網站中的 Azure Active Directory 連線狀況刀使用監控 AD DS AD FS 代理程式。 如需詳細資訊，請參閱[使用 Azure AD 連線健康狀況與 AD FS][aad-health-adfs]

如需有關如何安裝 AD 連線健康狀況代理程式和其需求的詳細資訊，請參閱[Azure AD 連線健康狀況代理程式安裝][aad-agent-installation]。

## <a name="sample-solution"></a>範例方案

本節文件建置範例解決方案，您可以使用以測試 AAD 設定的步驟。 方案包含下列元素︰

- N 層 web 應用程式中，下列結構描述[執行 Vm N 層架構在 Azure][implementing-a-multi-tier-architecture-on-Azure]。

- 測試用戶端電腦。 我們建議用這台電腦上的另一個 VM。 只要您具備管理員權限，以及可以連線至 n 層 web 應用程式並不重要，此 VM 的設定。

- 模擬內部部署環境包含使用 AD DS 建立自己的網域。

這些步驟將告訴您的情況如下︰

- 啟用存取執行雲端中的外部使用者，提供密碼驗證 AAD n 層 web 應用程式。

- 啟用存取 n 層 web 應用程式在雲端執行 AAD 提供密碼驗證的組織內部的使用者。

### <a name="prerequisites"></a>必要條件

請依照下列步驟假設下列先決條件︰

- 您有現有的 Azure 訂閱，您可以在其中建立資源群組。

- 您已安裝[Azure 命令列介面][azure-cli]。

- 您必須下載並安裝最新的建立。 請參閱[以下][azure-powershell-download]如需相關指示。

- 您已安裝的[makecert]複本[makecert]公用程式測試用戶端電腦上。

- 您可以在已安裝的 Visual Studio 開發電腦存取。

### <a name="create-the-n-tier-web-application-architecture"></a>建立 n 層 web 應用程式結構

1. 建立一個名為資料夾`Scripts`包含一個名為子`Parameters`。

2. 下載[部署 ReferenceArchitecture.ps1] [solution-script]的指令碼資料夾的 PowerShell 指令碼。

3. 在 [參數] 資料夾中，建立名為 Windows 的另一個子資料夾。

4. 下列檔案下載到 Windows 參數/資料夾︰

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. **部署 ReferenceArchitecture.ps**1 中編輯檔案的指令碼資料夾，並變更指定應該建立，或用來保留 VM 和建立指令碼資源的資源群組的下列行︰

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. 編輯下列**參數/視窗**的資料夾中檔案，並設定`resourceGroup`中的值`virtualNetworkSettings`] 區段中每個檔案，相同的**部署 ReferenceArchitecture.ps1**指令碼檔中指定。

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. 開啟 PowerShell 的 Azure 視窗，移至 [指令碼] 資料夾，然後執行下列命令︰

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    取代**<subscription id>**您 Azure 訂閱 id。

    針對**<location>**，指定 Azure 的區域，例如*eastus*或*westus*。

8. 完成指令碼，請使用 Azure 入口網站以取得網站層負載平衡器 (*ra-aad-ntier-web-lb*) 的公用 IP 位址︰

    [![18]][18]

9. 登入您測試用戶端電腦 （或 VM），並確認您可以存取網站層使用 Internet Explorer 瀏覽至網站層負載平衡器的公用 IP 位址。 預設 IIS 頁面應該會出現。

### <a name="simulate-configuration-of-a-public-web-site"></a>模擬的公用網站的設定

>[AZURE.NOTE] 下列步驟以模擬網站層關聯修改 hosts 檔案測試用戶端電腦上的以 DNS 名稱 www.contoso.com。 此外，網站層 Vm 設定自我簽署的憑證與 fake 主控授權。 這是測試僅供和**不應使用生產環境中的技巧**。

1. 測試用戶端電腦上，使用 [記事本] 來編輯檔案**C:\Windows\System32\drivers\etc\hosts**並新增名稱 www.contoso.com 關聯網站層負載平衡器的公用 IP 位址的項目︰

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. 確認您可以立即瀏覽至 www.contoso.com 測試用戶端電腦從。 相同的預設 IIS 頁面應該會顯示為之前。

3. 測試用戶端電腦上，開啟命令提示字元以系統管理員身分並使用 c makecert 公用程式
4. 建立假根憑證授權單位憑證︰

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    確認會建立下列檔案︰

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. 執行下列命令以安裝測試根憑證授權單位︰

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. 您可以使用的測試憑證授權單位，產生 www.contoso.com 的憑證︰

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. 執行 [ **mmc** ] 命令，並新增憑證嵌入式管理單元的本機電腦的電腦帳戶。

7. 在 [ */Certificates （本機電腦）/個人/憑證/*儲存、 匯出為檔案命名 www.contoso.com.pfx 其私密金鑰 www.contoso.com 憑證︰

    [![20]][20]

8. 返回 [Azure 入口網站，並連接到管理層 VM (*ra-aad-ntier-管理-vm1*)。 預設的使用者名稱會以密碼*testuser* *AweS0me@PW*:

    [![21]][21]
    
9. 從管理層 VM，連線到每一個在使用*testuser*的使用者名稱和密碼的 web 層 Vm *AweS0me@PW*，並執行下列工作。 請注意，Vm 私人位址 IP 10.0.1.4 10.0.1.5，與 10.0.1.6:

    >[AZURE.NOTE] Web 層 Vm 只有私用的 IP 位址。 您只可以利用的管理層 VM 連線至它們。

    1. 複製檔案*www.contoso.com.pfx* *MyFakeRootCertificateAuthority.cer*測試用戶端電腦從。
    
    2. 開啟命令提示字元以系統管理員身分，移至按住您要複製，檔案的資料夾，請執行下列命令︰
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. 執行`mmc`命令、 新增憑證嵌入式管理單元的本機電腦，電腦帳戶，請確認已安裝下列憑證︰

        - \Certificates （本機電腦） \Personal\Certificates\ www.contoso.com

        - \Certificates （本機電腦） \Trusted 根憑證 Authorities\Certificates\MyFakeRootCertificateAuthority

    4. 啟動網際網路服務 (IIS) 管理員主控台並瀏覽至*Sites\Default 網站*在電腦上。

    5. 在 [*動作*] 窗格中，按一下*連結*，並新增使用 www.contoso.com SSL 憑證 https 繫結︰

        [![22]][22]

10. 返回 [測試用戶端電腦，並確認您可以立即瀏覽至 https://www.contoso.com。

### <a name="create-an-azure-active-directory-tenant"></a>建立 Azure Active Directory 租用戶

1. 使用 Azure 入口網站中，建立的 Azure Active Directory 租用戶，例如*myaadname*。 onmicrosoft.com， *myaadname*是由您所選取的目錄名稱的位置。

2. 新增名為*管理員*與目錄 GlobalAdmin 角色的使用者。 記下新產生的密碼。

3. 使用 Internet Explorer，瀏覽至 https://account.activedirectory.windowsazure.com/ 和身分登入admin@ *myaadname*。 onmicrosoft.com。 變更您的密碼，出現提示時。

### <a name="create-and-deploy-a-test-web-application"></a>建立並部署測試 web 應用程式

1. 使用 Visual Studio 開發電腦上，建立名為 ContosoWebApp1 ASP.NET Web 應用程式 （使用.NET Framework 4.5.2）︰

    [![23]][23]

2. 選取*MVC*範本*公司與學校帳戶*，變更驗證，指定您 AAD 租用戶的名稱。 不在雲端建立應用程式服務。

    [![24]][24]

3. 建立並執行測試驗證應用程式。 在登入頁面上輸入 [帳戶名稱admin@ *myaadname*。 onmicrosoft.com，提供的密碼，然後按一下 [*登入*︰

    [![25]][25]

4. 驗證 AAD 要求登入並讀取您的設定檔的權限，便會開始執行管理員的身分識別的 web 應用程式

5. 關閉 Internet Explorer，並返回 [Visual Studio。

6. 開啟 web.config 檔案，以及在`<appSettings>`區段中，變更的*ida: PostLogoutRedirectUri*的關鍵值*https://www.contoso.com:443 /*。 儲存檔案。

7. 在*方案總管*] 視窗中，以滑鼠右鍵按一下 ContosoWebApp1 專案，按一下 [*發佈*]。

8. 在 [*發佈網站*] 視窗中，按一下 [*自訂*]。 建立自訂設定檔名為*ContosoWebApp1*。

9. 在 [*連線*] 頁面中，將*檔案系統*的*發佈方法*並指定名為*ContosoWebApp*，位於 [開發電腦適當的位置。

10. 在 [*設定*] 頁面中，設定*設定**發行版本*。

11. 在*預覽*頁面上，按一下 [*發佈]*。

12. 連線至每個網頁伺服器，依序 （透過管理層 VM），然後執行下列工作︰

    1. 將從開發電腦*ContosoWebApp*資料夾和其內容複製到 [ *C:\inetpub* ] 資料夾。

    2. 使用網際網路服務 (IIS) 管理員主控台，瀏覽至*Sites\Default 網站*在電腦上。

    3. 在 [*動作*] 窗格中，按一下*基礎設定*]，並變更網站的實體路徑為*%SystemDrive%\inetpub\ContosoWebApp*:

        [![26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>發佈到 AAD 測試 web 應用程式

1. 登入 Azure 入口網站，並瀏覽至您的 AAD 目錄。

2. 在 [*應用程式*] 索引標籤上按一下 ContosoWebApp1 應用程式。

3. 驗證目錄、 成功新增的應用程式，然後按一下 [*設定*] 索引標籤。

4. 變更*登開啟 URL* https://www.contoso.com:443，並將*回覆 URL* https://www.contoso.com:443 (相同 URL)。

5. 儲存設定。

6. 測試用戶端電腦上，瀏覽至 https://www.contoso.com。 驗證的 AAD 會提示您輸入您的認證，然後登入。

>[AZURE.NOTE] 這是第一種情況的端點︰ 啟用存取執行雲端中的外部使用者，提供密碼驗證 AAD n 層 web 應用程式。

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>使用 Active Directory 中建立模擬內部部署環境

內部部署環境包含兩個網域控制站的`contoso.com`網域和代管 Azure AD Connect 兩個伺服器同步處理服務。 裝載 Azure AD Connect 伺服器不是網域。

1. 在檔案總管] 中，傳回包含用來建立 N 層 web 應用程式的指令碼的指令碼資料夾。

2. 在 [參數] 資料夾中，新增另一個名為 「 的子資料夾`Onpremise`。

3. 下列檔案下載到參數/Onpremise 資料夾︰

    - [新增-新增的網域-controller.parameters.json][add-adds-domain-controller-parameters]

    - [建立-新增-樹系-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines adc.parameters.json][virtualMachines-adc-parameters]

    - [virtualMachines-adc-joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [virtualMachines adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [virtualNetwork 新增 dns.parameters.json][virtualNetwork-adds-dns-parameters]

5. 部署 ReferenceArchitecture.ps1 中編輯檔案的指令碼資料夾，並變更指定應該建立，或用來保留 VM 和建立指令碼資源的資源群組的下列行︰

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. 編輯下列參數/Onpremise 資料夾中的檔案，並設定`resourceGroup`中的值`virtualNetworkSettings`] 區段中每個檔案，相同的部署 ReferenceArchitecture.ps1 指令碼檔中指定。

    - virtualMachines adds.parameters.json

    - virtualMachines adc.parameters.json

    - virtualNetwork.parameters.json

    - virtualNetwork 新增 dns.parameters.json

7. 開啟 PowerShell 的 Azure 視窗，移至 [指令碼] 資料夾，然後執行下列命令︰

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    取代`<subscription id>`您 Azure 訂閱 id。

    針對`<location>`，指定 Azure 的區域，例如`eastus`或`westus`。

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>安裝並設定 Azure AD Connect 同步處理服務

Azure AD Connect 同步處理服務的兩個執行個體包含以下步驟所示的設定。 第二個設定以提供快速容錯移轉及高可用性如果第一個伺服器失敗的暫存模式時，使用中第一個。

1. 使用 [Azure 入口網站，瀏覽至 [資源群組拿著 Vm Azure AD Connect 同步處理服務 (預設的*ra aad-onpremise 路由*，)，並連線到*ra-aad-onpremise-adc-vm1* VM。 *Testuser*密碼以登入*AweS0me@PW*。

2. 從 [[在這裡]下載 Azure AD Connect[aad-connect-download]。

3. 執行 Azure AD Connect 安裝程式]，並執行安裝使用 [*自訂*] 選項。

    >[AZURE.NOTE] 您無法使用 [*快速設定*選項，因為 VM 裝載 Azure AD Connect 同步處理服務無法網域。

4. 在*安裝所需的元件*] 頁面上，將選擇性組態設定保留空白，接受預設的選項。

5. 在 [*使用者登入*] 頁面中，選取 [*密碼同步處理*]。

6. 在 [*連線至 Azure AD* ] 頁面上輸入admin@ *myaadname*。 onmicrosoft.com，其中*myaadname*是您 AAD 租用戶的名稱。 輸入管理員帳戶的密碼。

7. 在*您的目錄的連線*] 頁面中，指定樹系 contoso.com （輸入中的值因為它不會出現在下拉式清單中），CONTOSO\testuser 輸入使用者名稱，指定AweS0me@PW密碼，然後按一下 [*新增目錄*。

8. 在 [ *Azure AD 登入設定*] 頁面接受預設值的使用者主要名稱。 核取 [*繼續] 沒有任何驗證網域*]。

    >[AZURE.NOTE] Contoso.com 目錄列出*未驗證*。 若要驗證的網域名稱，您必須建立網域名稱註冊機構的 TXT 記錄。 在此範例中，內部部署網域未註冊外部。 如需詳細資訊，請參閱[新增 Azure Active Directory 的自訂網域名稱][aad-custom-directory]。

9. 在*網域和 OU 篩選*頁面上，選取 [*同步處理所有的網域和 Ou* （預設）]。

10. 在 [*可唯一識別您的使用者*] 頁面接受預設值。

11. 在*使用者和裝置篩選*的頁面，選取 [*同步處理所有的使用者和裝置*（預設）]。

12. 在 [*選擇性功能*] 頁面中，選取 [*密碼回寫*]。

13. 在*準備好設定*頁面上，選取 [*開始同步處理程序設定完成後*，，但保留*啟用暫存模式*取消選取。

14. 驗證的設定程序完成不具有錯誤，然後結束安裝程式。

15. 從 Azure] 入口網站連線至*ra-aad-onpremise-adc-vm2* VM。 *Testuser*密碼以登入*AweS0me@PW*。

16. 下載 Azure AD Connect，並執行安裝程式。

17. 精靈的步驟，並回應步驟 3 到 12 步驟所述。

18. 在*準備設定*頁面上，選取 [*開始同步處理程序設定完成後*，和**也選取 [** *啟用暫存的模式*。 這會使 Azure AD Connect 同步處理服務擷取 contoso.com 網域的帳戶和物件的詳細資訊，並將它們儲存在資料庫中，但它不會傳送至您的 AAD 租用戶這些詳細資料。

14. 驗證的設定程序完成不具有錯誤，然後結束安裝程式。

### <a name="test-the-aad-configuration"></a>測試 AAD 設定

1. 使用 Azure 入口網站，請切換至 AAD 目錄、 開啟 Azure Active Directory 刀、 按一下 [*使用者和群組*]，然後按一下 [*所有使用者*，以顯示使用者和群組與目錄同步處理的清單。 您應該會看到下列帳戶的使用者︰

    - 管理員(admin@ *myaadname*。 onmicrosoft.com)

    - 內部部署目錄同步處理服務帳戶 (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*。 onmicrosoft.com)

    - 內部部署目錄同步處理服務帳戶 (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*。 onmicrosoft.com)

2. 在 Azure 入口網站，瀏覽至 [資源群組拿著 AD DS 網域控制站 (預設的*ra aad-onpremise 路由*，) Vm 並連線到*ra-aad-onpremise-ad-vm1* VM。 *Testuser*密碼以登入*AweS0me@PW*。

3. 使用*Active Directory 使用者和電腦*主控台，新增網域使用者登入名稱命名 Diane Tibbot dianet@contoso.com。 指定您所選擇的密碼。 讓使用者的本機管理員群組的成員 （這是只讓您可以在本機使用者身分登入此稍後-網域中的唯一機器是 Dc）。

4. 切換至*ra-aad-onpremise-adc-vm1* VM、 開啟 PowerShell 視窗中，執行下列命令︰

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    驗證*成功*傳回命令。

    >[AZURE.NOTE] 此步驟，因為預設同步處理排程執行在 30 分鐘的時間間隔。 這些命令強制立即同步處理。

5. 返回 Azure 入口網站切換到您 AAD 目錄中，開啟 Azure Active Directory 刀、 按一下 [*使用者和群組*]，再按一下 [*所有使用者*。 您現在應該會看到 Diane Tibbot (dianet@ *myaadname*。 onmicrosoft.com) 的使用者清單中顯示。

6. 在 Azure Active Directory 刀中，按一下*企業應用程式*，，然後按一下 [*所有應用程式*。 按一下 [ *ContosoWebApp1*應用程式，然後按一下 [*使用者和群組]*。 在工具列上，按一下 [*新增*]。 按一下 [*使用者和群組*]，然後選取*Diane Tibbot*。 按一下 [*指派*]。

7. 使用 Internet Explorer，瀏覽至網站 https://account.activedirectory.windowsazure.com。 登入為dianet@ *myaadname*。 onmicrosoft.com 您先前指定的密碼。

    >[AZURE.NOTE] 請勿按的應用程式清單中的 [ContosoWebApp] 圖示。 若要尋找的 web 應用程式公開列出的 DNS 地址的 www.contoso.com，這是您的網站層負載平衡器的地址不同嘗試 AAD。

8. 按一下 [*設定檔*] 索引標籤。 應該顯示使用者 （如果有指定任何建立時） 的詳細資料。

    >[AZURE.NOTE] 按一下 [*變更密碼*時，如果您不允許執行這項工作，因為這項作業必須啟用由系統管理員第一次。 如需詳細資訊，請參閱[快速入門密碼管理][aad-password-management]。

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>讓內部部署使用者可以使用驗證透過 AAD 執行應用程式

1. 返回*ra-aad-onpremise-ad-vm1*網域控制站 VM。

2. 開啟 [ *DNS 管理員*] 主控台並加入 www.contoso.com 的新主機記錄。 指定網站層負載平衡器的公用 IP 位址。

3. 複製檔案*MyFakeRootCertificateAuthority.cer*測試用戶端電腦 （您建立了此檔案中[的公用網站的模擬設定](#simulate-configuration-of-a-public-web-site)的程序
    
4. 開啟命令提示字元以系統管理員身分，移至拿著您剛才複製檔案的資料夾，並執行下列命令︰

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. 使用 Internet Explorer，瀏覽至 https://www.contoso.com。 請確認 ContosoWebApp1 web 應用程式的登入頁面 AAD 出現。

6. 登入為dianet@ *myaadname*。 onmicrosoft.com。 應用程式應該執行，並登入正確。

## <a name="next-steps"></a>後續步驟

- [延伸內部部署新增網域至 Azure]最佳作法[adds-extend-domain]

- 瞭解[建立新增資源樹系]的最佳作法[adds-resource-forest]Azure 中

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "使用 Azure Active Directory 雲端身分識別架構"
[1]: ./media/guidance-identity-aad/figure2.png "單一樹系，單一 AAD 目錄拓撲"
[2]: ./media/guidance-identity-aad/figure3.png "多個樹系單一 AAD 目錄拓撲"
[4]: ./media/guidance-identity-aad/figure5.png "執行伺服器拓撲"
[5]: ./media/guidance-identity-aad/figure6.png "多個 AAD 目錄拓撲"
[6]: ./media/guidance-identity-aad/figure7.png "選取自訂 Azure AD 連線同步處理的特定的執行個體的 SQL Server 安裝"
[7]: ./media/guidance-identity-aad/figure8.png "指定使用者登入的 SSO 方法"
[8]: ./media/guidance-identity-aad/figure9.png "指定網域和 OU 篩選選項"
[9]: ./media/guidance-identity-aad/figure10.png "啟用密碼回寫功能"
[10]: ./media/guidance-identity-aad/figure11.png "Azure AD 管理刀在入口網站"
[11]: ./media/guidance-identity-aad/figure12.png "Azure AD Connect 主控台"
[12]: ./media/guidance-identity-aad/figure13.png "在同步處理服務管理員] 中的 [作業] 索引標籤"
[13]: ./media/guidance-identity-aad/figure14.png "在同步處理服務管理員] 中的 [連接器] 索引標籤"
[14]: ./media/guidance-identity-aad/figure15.png "同步處理規則編輯器"
[15]: ./media/guidance-identity-aad/figure16.png "在顯示同步處理狀況 Azure 入口網站中的 Azure Active Directory 連線狀況刀"
[16]: ./media/guidance-identity-aad/figure17.png "顯示 AD DS 狀況 Azure 入口網站中的 Azure Active Directory 連線狀況刀"
[17]: ./media/guidance-identity-aad/figure18.png "Azure 入口網站中可用的安全性報表"
[18]: ./media/guidance-identity-aad/figure19.png "醒目提示網站層負載平衡器的公用 IP 位址 Azure 入口網站"
[19]: ./media/guidance-identity-aad/figure20.png "使用 Internet Explorer 瀏覽至網站層負載平衡器的公用 IP 位址"
[20]: ./media/guidance-identity-aad/figure21.png "憑證嵌入式管理單元顯示 www.contoso.com 憑證"
[21]: ./media/guidance-identity-aad/figure22.png "連接到管理層 VM"
[22]: ./media/guidance-identity-aad/figure23.png "建立 HTTPS 繫結的預設網站"
[23]: ./media/guidance-identity-aad/figure24.png "建立 ContosoWebApp1 web 應用程式"
[24]: ./media/guidance-identity-aad/figure25.png "設定 ContosoWebApp1 web 應用程式的驗證屬性"
[25]: ./media/guidance-identity-aad/figure26.png "登入 Azure AAD 從 ContosoWebApp1 web 應用程式"
[26]: ./media/guidance-identity-aad/figure27.png "變更預設的網站的資料夾"