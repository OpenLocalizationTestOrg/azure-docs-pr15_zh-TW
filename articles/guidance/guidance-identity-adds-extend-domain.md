<properties
   pageTitle="Azure 架構參考 IaaS︰ 將 Active Directory 延伸至 Azure |Microsoft Azure"
   description="Azure 中實作安全的混合式網路架構與 Active Directory 授權的方式。"
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>延伸至 Azure 的 Active Directory 目錄服務 （新增）

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明延伸至 Azure，以使用[Active Directory 網域服務 (AD DS)]提供分散式的驗證服務的 Active Directory (AD) 環境的最佳作法[active-directory-domain-services]。 此結構延伸[實作安全混合的網路架構 Azure 中]的文件中所述[implementing-a-secure-hybrid-network-architecture]和[實作安全混合的網路架構與 Azure 中的網際網路存取][implementing-a-secure-hybrid-network-architecture-with-internet-access]。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 此參考架構會使用資源管理員，Microsoft 建議新的部署。

您可以使用 AD DS 驗證身分識別。 這些識別可以隸屬於使用者、 電腦、 應用程式] 或其他資源的安全性網域部分。 您也可以裝載 AD DS 內部部署，但在應用程式的項目 Azure 中的所在位置的混合式案例它可以複製至雲端的這項功能和 AD 存放庫更有效率。 這種方法可協助降低傳送驗證所造成的延遲和雲端的本機的授權要求回至 AD DS 執行內部部署。 

有兩種方式來管理您的目錄服務 Azure 中︰

1. 您可以使用[Azure Active Directory (AAD)] [azure-active-directory]在雲端建立新的 AD 網域，並將其連結至內部部署 AD 網域。 然後設定[Azure AD Connect] [azure-ad-connect]上承諾複寫身分識別保留在雲端的內部部署存放庫。 請注意，在雲端的目錄****內部部署系統的副檔名，而是包含相同的身分識別的複本。 回到內部部署網域複寫內部部署將會複製到雲端，但在雲端，**則不會**變更這些身分識別所做的變更。 例如，密碼重設必須執行內部部署並使用 Azure AD Connect，將變更複製到雲端。 此外，請注意 AAD 的同一個執行個體，可以連結至 AD ds; 多個執行個體AAD 會包含連結的每個 AD 存放庫的身分識別。

    AAD 非常適合位置的內部部署網路及裝載雲端資源 Azure 虛擬網路不會直接連結使用 VPN 通道或 ExpressRoute 電路的情況。 雖然這個方案簡單，它可能無法適用於系統讓元件可能移轉跨上部署/雲端界限這可能需要 AAD 重新設定。 此外，AAD 只會處理使用者驗證，而不是電腦驗證。 某些應用程式和服務，例如 SQL Server，可能需要電腦驗證在此情況下不適用此方案。

2. 您可以部署 VM 執行 AD DS 網域控制站 Azure 中，從您的內部部署資料中心延伸現有 AD 基礎結構。 這個方法最常見的案例的內部部署網路和 Azure 虛擬網路位置連線 VPN 及/或 ExpressRoute 連線。 此方案也支援雙向複寫讓您在中進行變更的雲端及內部部署，是最適當的地方。 根據您的安全性需求，可在雲端的 AD 安裝︰
    - 相同的網域的保留內部部署的一部分
    - 共用樹系中新的網域
    - 個別的樹系

<!-- we might want to add info on how to choose from the three options above -->

此結構著重於使用相同的 AD DS 網域 Azure 及內部部署中的方案 2。

此結構的一般的使用案例包括︰

- 混合式應用程式位置負載執行只能內部部署和只能在 Azure。

- 應用程式與服務的使用 Active Directory 執行驗證。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示重要的元件，在這種架構 （[*按一下以拉近顯示*）。 更多關於灰色元素的詳細資訊，請參閱[實作 Azure 中的安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]和[實作安全混合的網路架構與 Azure 中的網際網路存取][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[![0]][0]

- **內部部署網路。** 內部部署網路包含可以執行驗證與授權元件位於內部部署的本機 AD 伺服器。

- **AD 伺服器。** 這些是網域控制站實作執行 Vm 雲端中的目錄服務 (AD DS)。 這些伺服器可以提供元件執行 Azure 虛擬網路中的驗證。

- **Active Directory 的子網路。** AD DS 伺服器都裝載於不同的子網路。 NSG 規則保護 AD DS 伺服器，並提供對非預期的來源的流量防火牆。

- **Azure 閘道器與 AD 同步處理。**。 Azure 閘道器提供內部部署網路與 Azure VNet 之間的連線。 這可能是[VPN 連線][azure-vpn-gateway]或[Azure ExpressRoute][azure-expressroute]。 在雲端的 AD 伺服器與內部部署之間的所有同步處理要求通過閘道器。 使用者定義的路徑 (UDRs) 控點的直接傳送至 AD 伺服器在雲端，並不會將透過現有網路虛擬應用裝置 (NVAs) 在此情況下使用同步處理流量路由。

    如需有關如何設定 UDRs 及 NVAs 的詳細資訊，請參閱[實作 Azure 中的安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]。

## <a name="recommendations"></a>建議

本節摘錄了實作 Azure AD DS 的建議涵蓋︰

- VM 建議。

- 網路的建議。

- 安全性的建議。 

- Active Directory 網站建議。

- Active Directory FSMO 位置的建議。

>[AZURE.NOTE] 文件[的指導方針部署 Windows Server Active Directory 上 Azure 虛擬機器][ad-azure-guidelines]包含許多這些點的詳細的資訊。

### <a name="vm-recommendations"></a>VM 建議

建立 Vm 足夠的資源來處理預期的流量音量。 使用主控開始算起的 AD DS 內部部署電腦的大小。 監視資源使用狀況。您可以調整大小 Vm 並縮小是否太大。 如需有關縮放 AD DS 網域控制站的詳細資訊，請參閱[Active Directory 網域服務的容量計劃][capacity-planning-for-adds]。

建立個別的虛擬資料片 ad 儲存資料庫、 記錄和 SYSVOL。 不要作業系統為相同的磁碟上儲存這些項目。 請注意，根據預設，附加至 VM 的資料磁碟使用寫入到快取。 不過，這種快取發生衝突 AD ds 的需求。 因此，將 [*主機快取喜好設定*] 設定為 [*無*資料磁碟上。 如需詳細資訊，請參閱[Windows Server AD DS 資料庫及 SYSVOL 位置][adds-data-disks]。

部署至[可用性](#Availability-considerations)基於 Azure 虛擬網路，為網域控制站執行 AD DS 至少兩個 Vm。

### <a name="networking-recommendations"></a>網路建議

設定網路介面的每個 Vm 裝載 AD DS 私人的靜態 IP 位址。 更佳，此設定會在每一 AD Vm 支援 DNS。 如需詳細資訊，請參閱[如何設定 Azure 入口網站的靜態私用的 IP 位址][set-a-static-ip-address]。

> [AZURE.NOTE] 不提供 AD DS Vm 的公用 IP 位址。 請參閱[的安全性考量][security-considerations]如需詳細資訊。

您必須確定的流量自由之間 AD 伺服器在雲端和內部部署︰

- 允許來自內部部署的內送的資料傳輸 AD 子網路新增 NSG 規則。 如使用 AD DS 的連接埠的詳細資訊，請參閱[Active Directory 和 Active Directory 網域服務連接埠需求][ad-ds-ports]。

- 請確定 UDR 表格執行不 AD DS 將流量路由傳送到此案例中使用 NVAs。 針對您的部署，根據您使用時，哪些 NVAs 可能會想要的流量重新導向。

### <a name="security-recommendations"></a>安全性的建議

AD DS 伺服器處理驗證，因此很重要的項目。 防止直接公開 AD DS 伺服器至網際網路。 將 AD DS 伺服器放在不同的子網路，使用自己的防火牆。 請確定所需使用 AD DS 的驗證及授權，以及 synchronzing 伺服器的連接埠保持開啟，但關閉所有其他的連接埠。 如需詳細資訊，請參閱[Active Directory 和 Active Directory 網域服務連接埠需求][ad-ds-ports]。 [解決方案元件][solution-components]稍後的這份文件] 區段會顯示 NSG 規則範例方案使用開啟連接埠。

若要建立簡單的防火牆，您可以使用 NSG 規則。 如果您需要更完整的保護您可以實作伺服器周圍額外的安全性外圍使用對子網路和 NVAs，來[實作安全混合的網路架構與 Azure 中的網際網路存取]文件所述[implementing-a-secure-hybrid-network-architecture-with-internet-access]。

使用 BitLocker 或 Azure 磁碟加密加密裝載 AD DS 資料庫的磁碟。

### <a name="active-directory-site-recommendations"></a>Active Directory 網站建議

您可以使用連接的快速連結的群組在一起的網域控制站 AD DS 中的網站。 在同一 AD DS 網站的網域控制站自動複寫目錄所做的變更，小型的設定，則需要處理複寫。

若要控制 Azure 及內部部署資料中心之間的複寫流量，建議新增另一個 AD DS 網站來代表 Azure 所使用的位址空間。 您可以設定的網站連結 AD DS 網站以及更有效地控制跨網站複寫之間您的內部部署。

您也可以使用網站分隔套用不同的群組原則物件 (Gpo 若要加入 Azure 中的電腦，並利用 「 網站注意 」，例如 「 系統管理中心設定管理員的應用程式)。

### <a name="active-directory-fsmo-placement-recommendations"></a>Active Directory FSMO 位置的建議

彈性的單一母片作業 (FSMO) 伺服器是特殊的網域控制站，reposnsible 資料的一致性跨 AD DS 下方所列中的不同設定。

- **結構描述母片**。 這是樹系角色維護使用 AD DS 的結構描述的結構。

- **網域命名母片**。 這是樹系角色管理樹系的網域名稱的相關資訊。

- **主要網域控制站 (PDC)**。 這是全網域的角色。 PDC 處理密碼更新及帳戶鎖定。 服務驗證要求有不相符的密碼時，它被諮詢其他 dc。 PDC 也控點的群組原則更新，而是目標 DC 的舊版應用程式和一些管理員工具執行某些可寫入的作業。

- **相對識別碼 (RID) 母片**。 Rid 用來協助唯一識別目錄內的物件。 此伺服器負責網域中的所有 AD 伺服器都產生的 Rid 用於整個網域集區。

- **基礎結構角色**。 一個網域中的物件可以參照另一個網域中的物件。 此全網域角色負責更新物件的 SID 與跨網域物件參照中的辨別的名稱。 請注意，執行此角色的伺服器也無法作為全域目錄 (GC) 伺服器。

如需詳細資訊，請參閱[Active Directory 故障 Windows 中的][AD-FSMO-roles-in-windows]。

為這個案例中，我們建議您避免 Azure 中的網域控制站部署故障。 

## <a name="availability-considerations"></a>可用性考量

建立設定 AD 伺服器的可用性。 請確定在設定有兩個以上的伺服器。 在雲端的 AD 伺服器應該相同的網域中的網域。 這樣會啟用自動複寫伺服器之間。

此外，請考慮為[備用操作母片]指定伺服器[standby-operations-masters]以防做為 FSMO 角色的伺服器的連線失敗。

## <a name="security-considerations"></a>安全性考量

如果所有的網域管理員工作可能會使用內部部署 Dc 執行，請考慮在雲端中的 Dc 變成唯讀。 唯讀 DC 只會維護 （才能執行本機驗證） 的使用者認證的子集，並可設定為僅適用於特定使用者的快取資訊。 因此，如果遭唯讀 DC 快取使用者的資訊會影響，而不是網域中的每個帳戶的詳細資料。 如需詳細資訊，請參閱[唯讀網域控制站][read-only-dc]。

若要最小化的個別使用者帳戶，並防止嘗試入侵，請遵循設定和維護 AD 中的使用者的密碼的最佳作法。 如需詳細資訊，請參閱[最佳作法強制密碼原則][best_practices_ad_password_policy]。 此外，請小心指派使用者的群組。 例如，請勿選取 [一般使用者的企業系統管理員群組、 結構描述管理員] 群組中，Domain Admins 群組的成員。

## <a name="scalability-considerations"></a>延展性考量

AD 是網域的屬於相同的網域控制站的自動調整。 要求分佈網域中的所有控制站。 您可以新增另一個網域控制站，它會自動同步處理的網域。 未設定不同的負載平衡器，將網域中的 [控制站流量導向。 確定所有網域控制站都有足夠的記憶體和儲存資源可處理網域資料庫，例如︰理想的情況下，讓所有的網域控制站 Vm 相同的大小。

## <a name="management-considerations"></a>管理考量事項

請勿複製，而不是執行定期備份，因為還原可能會導致之間網域控制站的狀態不一致的網域控制站的 VHD 檔案。

關閉並重新啟動 VM 來賓作業系統，而不是 Azure 入口網站中使用關機] 選項中，執行 Azure 中的網域控制站的。 若要關閉 VM 使用 Azure 入口網站，會使解除配置 VM。 這個動作，重設 VM GenerationID，這是不需要的網域控制站。 重設 VM GenerationID 時, AD 存放庫呼叫識別碼也重設，捨棄 RID 集區與 SYSVOL 已標示為非代表性。

## <a name="monitoring-considerations"></a>監控考量

監視及維護作業 AD 伺服器的網路失敗可能會導致問題例如︰

- **登入失敗**。 登入失敗就會發生整個網域或樹系的信任關係] 或 [名稱解析失敗時，或如果通用類別目錄伺服器無法判斷通用群組成員資格。

- **帳戶鎖定**。 如果主要網域控制站無法使用，或多個網域控制站之間的複寫失敗，可能會變成鎖定使用者與服務帳戶。

- **網域控制站失敗**。 如果包含 Ntds.dit 檔案的磁碟空間，網域控制站可以停止運作。

- **應用程式錯誤**。 如果將目錄的地址活頁簿查詢會失敗，可能會失敗的關鍵 Microsoft Exchange 或另一個電子郵件應用程式，例如企業應用程式。

- **不一致的目錄的資料**。 如果複寫失敗的一段時間，請重複的物件就會可以建立在目錄中，且可能需要廣泛的診斷和以利消除的時間。

- **帳戶建立失敗**。 網域控制站無法建立使用者或電腦帳戶，如果其耗盡其提供的相對的識別碼，無法使用 RID 母片。

- **安全性原則失敗**。 如果不正確的複寫 SYSVOL 共用的資料夾，群組原則物件和安全性原則會不正確套用至用戶端。

監視 AD 伺服器謹慎，並準備快速採取修正動作。 建立監視適當的間隔中執行的工作檢查清單。 例如您可能會每日排程下列的要徑任務︰

- 檢查並解決報告的網域控制站的通知 

- 確認所有網域控制站能以都無聲複寫正常運作。

- 確保 SYSVOL 保持共用。

- 修正時間同步處理問題。

- 檢查 AD 所使用的實體磁碟機上的磁碟空間。

您可能會經常執行其他例行工作較少。 例如，您可以檢閱信任資料庫關聯圖和每週] 核取過時或損毀的信任，確認所有網域控制站正在都執行每月使用相同的 service pack 及修正修補程式。

如需詳細資訊，請參閱[監控 Active Directory][monitoring_ad]。 您可以安裝工具，例如[Microsoft 系統管理中心][microsoft_systems_center]監控伺服器上 (請參閱[架構圖表][architecture]) 可協助執行這些工作。 

## <a name="solution-components"></a>解決方案元件

提供此架構建立安全的混合式網路，來[實作安全混合的網路架構 Azure 中]的文件所述的解決方案[implementing-a-secure-hybrid-network-architecture]和[實作安全混合的網路架構與 Azure 中的網際網路存取][implementing-a-secure-hybrid-network-architecture-with-internet-access]，但加上的下列項目︰

- Azure 資源群組名稱為*basename*-dns-路由， *basename*哪裡首碼您指定部署解決方案。

- 兩個 Azure Vm 稱為*basename*-ad1-vm 和*basename*-ad2-vm，建立*basename*-dns 路由資源群組。 這些 Vm 會設定為 AD 伺服器與目錄服務和安裝並設定 DNS。

- NSG 名為*basename*-ad-nsg *basename*-ntwk 路由 Azure 資源群組中的。 此資源群組會構成安全的混合式網路基礎結構的組件，但新 NSG 定義輸入的安全性規則 AD 伺服器下表所示的元件︰


    優先順序|名稱|來源|目的地|服務|巨集指令|
    --------|----|------|-----------|-------|------|
    170|vnet-port53|10.0.0.0/16|任何|Custom(ANY/53)|允許|
    180|vnet-port88|10.0.0.0/16|任何|Custom(ANY/88)|允許|
    190|vnet-port135|10.0.0.0/16|任何|Custom(ANY/135)|允許|
    200|vnet 到 port137 9|10.0.0.0/16|任何|Custom(ANY/137-139)|允許|
    210|vnet-port389|10.0.0.0/16|任何|Custom(ANY/389)|允許|
    220|vnet-port464|10.0.0.0/16|任何|Custom(ANY/464)|允許|
    230|動態至 rpc vnet|10.0.0.0/16|任何|Custom(ANY/49152-65535)|允許|
    240|onprem-ad-至-port53|192.168.0.0/24|任何|Custom(ANY/53)|允許|
    250|onprem-ad-至-port88|192.168.0.0/24|任何|Custom(ANY/88)|允許|
    260|onprem-ad-至-port135|192.168.0.0/24|任何|Custom(ANY/135)|允許|
    270|onprem-ad-至-port389|192.168.0.0/24|任何|Custom(ANY/389)|允許|
    280|onprem-ad-至-port464|192.168.0.0/24|任何|Custom(ANY/464)|允許|
    290|管理 rdp 允許|10.0.0.128/25|任何|Custom(ANY/3389)|允許|
    300|閘道器允許|10.0.255.224/27|任何|Custom(ANY/Any)|允許|
    310|自我允許|10.0.255.192/27|任何|Custom(ANY/Any)|允許|
    320|vnet-拒絕|任何|任何|Custom(ANY/Any)|允許|

    AD DS 使用連接埠 53，89、 135、 389 和 464 接受傳入的複寫和驗證流量。 在這個資料表中，內部部署網域控制站位於位址空間 192.168.0.0/24 (可能會有所不同位址空間-您做為參數指定這項資訊部署解決方案的範本。

    NSG 也會定義下列的啟用同步處理和授權的流量回內部網路的輸出安全性規則︰

    優先順序|名稱|來源|目的地|服務|巨集指令|
    --------|----|------|-----------|-------|------|
    100|出 port53|任何|192.168.0.0/24|Custom(ANY/53)|允許|
    110|出 port88|任何|192.168.0.0/24|Custom(ANY/88)|允許|
    120|出 port135|任何|192.168.0.0/24|Custom(ANY/135)|允許|
    130|出 port389|任何|192.168.0.0/24|Custom(ANY/389)|允許|
    140|出 port445|任何|192.168.0.0/24|Custom(ANY/445)|允許|
    150|出 port464|任何|192.168.0.0/24|Custom(ANY/464)|允許|
    160|出 rpc 動態|任何|192.168.0.0/24|Custom(ANY/49152-65535)|允許|

提供解決方案的指令碼也會執行下列工作︰

- 它會加入*basename*-ad1-vm 和*basename*-ad2-vm 伺服器，為網域控制站的網域。 您可以在內部部署網域控制站*Active Directory 使用者和電腦*主控台中檢視這些伺服器︰

![[1]][1]

- 它 AD 網站的網域名稱 Azure VNet-Ad 網站建立新的子網路 (10.0.0.0/16)。 這個網站包含*basename*-ad1-vm 和*basename*-ad2-vm 伺服器。 

- 它會將雲端中設定複寫之間的間隔的內部部署網站與網域控制站的 IP 站台間傳輸設定。 您可以看見的子網路、 網站和內部部署網域控制站在*Active Directory 站台和伺服器*主控台中的傳輸設定的設定︰

![[2]][2]

## <a name="deployment"></a>部署

範例方案具有下列 prerequsites:

- 在您已設定的內部部署網域，請與您設定 DNS，並安裝路由及遠端存取服務，以支援 VPN 連線至 Azure VPN 閘道器。


- 您已經安裝最新版 Azure CLI。 [請遵循以下指示以取得詳細資料][cli-install]。

- 如果您部署從 Windows 解決方案，您必須安裝的工具，提供艦隊命令介面，例如[GitHub 桌面][github-desktop]。

>[AZURE.NOTE] 如果您沒有安裝 access 至現有的內部部署網域，您可以建立使用[onpremdeploy.sh]測試環境[onpremdeploy]被指令碼。 這個指令碼會建立網路名稱及 VM 在雲端的模擬基本內部部署安裝。 編輯後再繼續執行這個指令碼，並設定下列變數定義開頭的檔案︰
>
> - **BASE_NAME**。 使用者定義的前置字元的資源群組和指令碼建立 VM。 此項目應該**不超過 5 個字元**否則指令碼會產生不正確的名稱與 VM 和失敗。
> 
> - **訂閱**。 您的 Azure 訂閱 id。 資源群組將會建立在此 suscription。
> 
> - **位置**。 在其中建立 [資源] 群組中的，例如*eastus*或*westus*Azure 的位置。
> 
> - **ADMIN_USER_NAME**。 要使用 VM 中的系統管理員帳戶的名稱。
> 
> - **ADMIN_PASSWORD**。 系統管理員帳戶的密碼。

執行下列步驟，以建立範例方案︰

1. 下載並編輯[azuredeploy.sh] [azuredeploy]指令碼並設定下列參數開頭的檔案︰

    - **BASE_NAME**。 使用者定義的前置字元的資源群組和指令碼建立 Vm。 為之前，此項目應該**不超過 5 個字元**。

    - **訂閱**。 您的 Azure 訂閱 id。

    - **OS_TYPE**。 作業系統 （*Windows*或*Linux*） 使用 web、 商業及資料存取層 Vm。 請注意，指令碼所建立的所有 AD 伺服器就會都執行 Windows Server 2012，無論這項設定。

    - **網域名稱**。 內部部署網域的名稱。 請注意，是否您使用的 onpremdeploy.sh 指令碼建立的環境，這必須是*contoso.com*。

    - **位置**。 在其中建立資源群組 Azure 的位置。

    - **ADMIN_USER_NAME**。 要使用的各種 Vm 的管理員帳戶的名稱。

    - **ADMIN_PASSWORD**。 系統管理員帳戶的密碼。

    - **ON_PREMISES_PUBLIC_IP**。 內部部署 VPN 電腦的公用 IP 位址。

    - **ON_PREMISES_ADDRESS_SPACE**。 內部部署網路內部的地址空間。 如果您使用 onpremdeploy.sh 指令碼建立的環境，這必須是 192.168.0.0/16。

    - **VPN_IPSEC_SHARED_KEY**。 用來建立 VPN 連線，內部部署網路和 Azure VPN 閘道器之間 IPSec 共用索引鍵。

    - **ON_PREMISES_DNS_SERVER_ADDRESS**。 內部部署的 DNS 伺服器 IP 位址。 如果您使用 onpremdeploy.sh 指令碼建立的環境，這必須是 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX**內部部署子網路的地址前置字元。 如果您使用 onpremdeploy.sh 指令碼建立的環境，這必須是 192.168.0.0/24。

    >[AZURE.NOTE] 若要將資源與時間，指令碼並不會建立商務或資料的存取層級。 如果您需要這些項目，您可以取消註解下的一節中的 azuredeploy.sh 指令碼︰
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. 開啟艦隊命令介面提示，然後移至包含 azuredeploy.sh 指令碼的資料夾。

3. Azure 帳戶登入。 在艦隊命令介面輸入下列命令︰

    ```
    azure login
    ```

    請遵循指示連線至 Azure。

4. 執行命令`./azuredeploy.sh`，然後等候指令碼會建立網路基礎結構。

5. 出現提示時，*請確認 [VNet 建立之後*，請檢查資源群組名稱*basename*-ntwk 路由已經建立的以及包含類似下圖所示的項目使用 Azure 入口網站︰

    ![[3]][3]

    >[AZURE.NOTE] 在顯示的範例， *basename*設為*雲端*時執行指令碼。

    按一下*basename*vnet VNet、 按一下*子網路*，並確認已建立以下所示的子網路︰

    ![[4]][4]

6. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待時建立 web 層和負載平衡器。

7. 出現提示時，*請確認 [已正確地建立 Web 層*，請檢查已建立一個稱為*basename*網頁層路由的資源群組，並包含類似如下所示的項目使用 Azure 入口網站︰

    ![[5]][5]

8. 出現提示時，在艦隊命令介面視窗中，按下按鍵，然後建立 NVAs 等候。

9. 出現提示時，*請確認 [已正確地建立 NVA*，使用 Azure 入口網站檢查 [資源群組稱為*basename*-管理路由已經建立的下列內容︰

    ![[6]][6]

10. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待 jumpbox 建立時。

11. 出現提示時，*請確認 [已正確地建立 jumpbox*，請檢查下列項目已經新增*basename*-管理路由資源群組使用 Azure 入口網站︰

    - 可用性設定稱為*basename*jb-為。

    - 名為*basename*-jb vm VM。

    - 網路介面一部分*basename*-jb nic

12. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待時 Azure VPN 閘道器與連線的建立。 請注意此步驟可能需要 30 分鐘才能完成。

13. 出現提示時，*請確認 [已正確地建立 VPN 閘道器*，請檢查下列項目已經新增*basename*-ntwk 路由資源群組使用 Azure 入口網站︰

    - 區域網路的閘道器稱為上部署 lgw。
    
    - 虛擬網路閘道器稱為*basename*vpngw。

    - 名為*basename*-vnet vpnconn 閘道器連線。 請注意，是否尚未設定連線，內部部署結尾，此連線狀態可能會是*未連線*您將地址此更新版本。

14. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待時 Vm 與 DMZ 其他資源的建立。

15. 出現提示時，*請確認 [已正確地建立 DMZ*，使用 Azure 入口網站檢查 [資源群組稱為*basename*-dmz 路由已經建立的下列內容︰

    ![[7]][7]

16. 出現提示時，在艦隊命令介面視窗中，按下按鍵。 應該會出現下列提示︰

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    登入您的內部部署電腦連線到 Azure 閘道器的並適當地設定連線。 新增靜態路由至內部部署閘道器的裝置的指示 requestsfor 透過閘道器至 VNet 10.0.0.0/16 位址範圍。 執行此動作的步驟會根據您連線的方式而有所不同。 請參閱[實作混合的網路架構與 Azure 及內部部署 VPN] [implementing-a-hybrid-network-architecture-with-vpn]如需詳細資訊。

    請注意，您可以找到 Azure VPN 閘道器的公用 IP 位址，請使用 [Azure 入口網站檢查*basename*-ntwk 路由資源群組的*basename*-vpngw 閘道器︰

    ![[8]][8]

    您可以判斷是否已建立連線正確查看*basename*-vnet vpnconn 連線狀態。 它應該設定為 [*已連線*。

    若要測試連線，請開啟遠端桌面連線至 jumpbox (10.0.0.254) 從位於您的內部網路的電腦。

17. 出現提示時，在艦隊命令介面視窗中，按下按鍵。 在下一步提示，*請按任一按鍵將更新為指向內部部署的 DNS VNet VNet 設定*、 按下按鍵，等待時都會更新 VNet 的 DNS 設定，以您指定為**ON_PREMISES_DNS_SERVER_ADDRESS**參數 azuredeploy.sh 指令碼中的值。

18. 出現提示時，*請確認已更新 VNet 的 DNS 伺服器設定值*，請使用 Azure 入口網站，可以檢查*basename*-vnet VNet *basename*-ntwk 路由資源群組中的*DNS 伺服器*設定。 *自訂 DNS*，則應設定和*主要 DNS 伺服器*應該是內部 DNS 伺服器的位址︰

    ![[9]][9]

19. 在*按任一按鍵將建立資源群組 AD 伺服器的*提示時艦隊命令介面視窗中，按下按鍵，然後等候建立保留 AD 伺服器雲端中的資源群組]。

20. 在*按任一按鍵將建立的 AD 伺服器 Vm*提示艦隊命令介面視窗中按下按鍵並等待 Vm 會建立並新增至 [資源] 群組。

21. *請按任一按鍵加入至內部部署網域 Vm*出現時，移至 Azure 入口網站，並確認已建立名為*basename*-dns 路由的群組，並包含兩個 VM (*basename*-ad1-vm 和*basename*-ad2-vm):

    ![[10]][10]

22. 在*basename*-ntwk 路由的 [資源] 群組中，核取已經建立的 NSG 稱為 [ *basename*-ad-nsg。 檢查此 NSG 傳入和傳出安全性規則。 他們應該相符列出的資料表中[解決方案元件][solution-components]一節。

23. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待時 Vm 會新增至內部部署的網域。

24. 在*請移至內部部署 AD 伺服器，以驗證您的電腦已新增網域至*提示、 連線至您的內部部署電腦和使用*Active Directory 使用者和電腦*主控台來檢查兩個 Vm 有已新增網域︰

    ![[11]][11]

25. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待時 AD 複寫網站會建立在網域]。

26. 在*請移至內部部署 AD 伺服器驗證複寫網站建立之後*提示，請核取的複寫名為*Azure Vnet-Ad 網站*具有已成功建立網站，與稱為*AzureToOnpremLink*及參照 VNet 子網路 IP 站台間傳輸連結至使用*Active Directory 網站和服務*主控台︰

    ![[12]][12]

27. 出現提示時，在艦隊命令介面視窗中，按下按鍵，等待時指令碼會在每一 AD Vm 安裝目錄服務及 DNS。

28. 出現提示時*，請登入每個 Azure AD 伺服器驗證目錄服務已成功設定*時，開啟 jumpbox (*basename*-jb-vm)，從內部部署電腦遠端桌面連線，然後再開啟另一個遠端桌面連線從 jumpbox 的第一個 AD 伺服器 (*basename*-ad1-vm)。 使用**DOMAIN_NAME** **ADMIN_USER_NAME**，與**ADMIN_PASSWORD** azuredeploy.sh 指令碼中指定登入。 使用伺服器管理員，請確認的 AD DS 和 DNS 角色兩者都已新增。 第二個 AD 伺服器重複此程序 (*basename*-ad2-vm)。

29. 出現提示時，在艦隊命令介面視窗中，按下按鍵。 出現提示時，*請按任一按鍵將 Azure VNet DNS 設定指向 Azure 中的 DNS*時，請按下按鍵，並允許指令碼來更新 VNet 的 DNS 設定。

30. 當提示*請確認已設定 VNet DNS 更新參照 Azure VM DNS 伺服器*出現，請使用 Azure 入口網站的核取*basename*-vnet VNet *basename*-ntwk 路由資源群組中的*DNS 伺服器*設定。 主要和次要 DNS 伺服器現在應該參照兩個 AD Vm:

    ![[13]][13]

31. 重新啟動 AD Vm 再繼續。 此步驟是為了確保他們每個從 Azure 選擇正確的 DNS 設定。 在繼續之前執行兩個 Vm 等到。

32. 出現提示時，在艦隊命令介面視窗中，按下按鍵。 在下一個提示時，*請按任一按鍵，若要套用的閘道器 UDR 閘道器子網路 （它可能已經移除）*，按下按鍵，然後允許重新整理 UDR 閘道器的指令碼]。

33. 確認已順利完成指令碼。

## <a name="next-steps"></a>後續步驟

- 瞭解[建立新增資源樹系]的最佳作法[adds-resource-forest]Azure 中。

- 瞭解[建立 ADFS 基礎結構]的最佳作法[adfs]Azure 中。

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "安全的混合式網路架構與 Active Directory"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "列出兩個 Azure Vm 與伺服器的 Active Directory 使用者和電腦主控台"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "顯示網站的複寫設定在雲端的 Active Directory 網站和服務主控台"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "Basename-ntwk-路由資源群組的內容"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "在 [basename vnet VNet 子網路"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "在網頁層中的項目"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "在 basename-管理-路由資源群組 NVAs"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "[Basename-dmz-路由資源] 群組中的資源"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "尋找 Azure VPN 閘道器的公用 IP 位址"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "DNS 伺服器設定為 * basename *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "* Basename *-dns-包含 AD 伺服器 Vm 路由資源群組"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "為成員的網域列出 AD 伺服器 Vm Active Directory 使用者和電腦主控台"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "顯示複寫網站 Azure AD 伺服器的 Active Directory 網站和服務主控台"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "參照在雲端的 AD 伺服器 basename vnet VNet DNS 伺服器設定"