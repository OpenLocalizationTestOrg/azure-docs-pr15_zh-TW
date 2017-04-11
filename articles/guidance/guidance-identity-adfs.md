<properties
   pageTitle="Azure 中實作 ADFS |Microsoft Azure"
   description="Azure 中實作安全的混合式網路架構與 Active Directory Federation 服務授權的方式。"
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
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>實作 Azure Active Directory Federation Services (ADFS)

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明實作安全的混合式網路的延伸到 Azure 的內部部署網路，並使用[Active Directory Federation Services (ADFS)]的最佳作法[active-directory-federation-services]在雲端中執行的元件執行同盟的驗證和授權。 此結構延伸結構描述[至 Azure 延伸 Active Directory][implementing-active-directory]。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 此參考架構會使用資源管理員，Microsoft 建議新的部署。

ADFS 可以執行於內部部署，但在 Azure 中的應用程式的所在位置的混合式案例它可以實作這項功能在雲端更有效率。 此結構的一般的使用案例包括︰

- 混合式應用程式位置負載執行只能內部部署和只能在 Azure。

- 利用同盟的授權公開合作夥伴的 web 應用程式的解決方案。

- 支援的系統執行組織防火牆以外的網頁瀏覽器存取。

- 讓使用者從授權外部的裝置，例如遠端電腦的筆記本，與其他行動裝置連線來存取 web 應用程式的系統。 

如需有關 ADFS 的運作方式的詳細資訊，請參閱[Active Directory 同盟服務概觀][active-directory-federation-services-overview]。 此外，文件[中 Azure ADFS 部署][adfs-intro]包含的詳細逐步簡介實作 ADFS Azure 中。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示重要的元件，在這種架構 （[*按一下以拉近顯示*）。 更多與 ADFS 不相關的任何項目的詳細資訊，請參閱[實作 Azure 中的安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]，[實作安全混合的網路架構與 Azure 中的網際網路存取][implementing-a-secure-hybrid-network-architecture-with-internet-access]，及[實作與中 Azure Active Directory 身分識別的安全的混合式網路架構][implementing-active-directory]:

[![0]][0]

>[AZURE.NOTE] 此圖表描述下列情況中，使用︰
>
>- 執行合作夥伴組織內的應用程式碼存取裝載於您 Azure VNet web 應用程式。
>
>- 外部使用者已註冊 （含內新增存放的認證） 存取裝載於您 Azure VNet web 應用程式。
>
>- 連線到您 VNet 使用授權的裝置並執行裝載於您 Azure VNet web 應用程式使用者。
>
>此外，此架構解釋被動式同盟，同盟伺服器，決定如何以及何時要使用者進行驗證。 使用者必須執行的應用程式啟動時提供登入資訊。 這是最常使用的網頁瀏覽器的機制，並包含通訊協定的瀏覽器重新導向至網站使用者可以提供其認證的位置。 ADFS 也支援作用中的同盟，也就是應用程式負責提供認證，不需要進一步的使用者互動，但這種情況下超出此架構的範圍。

- **新增子網路。** 新增伺服器都包含在本身的子網路。 NSG 規則協助保護新增伺服器，並提供對非預期的來源的流量防火牆。

- **新增伺服器。** 這些是執行 Vm 雲端中的網域。 這些伺服器可以提供驗證的網域中的本機身分識別。

- **在 ADFS 子網路。** 在 ADFS 伺服器可與 NSG 規則做為防火牆位於本身的子網路。

- **在 ADFS 伺服器。** 在 ADFS 伺服器提供同盟的授權和驗證。 在這種架構，他們可以執行下列工作︰

    - 他們可以接收安全性權杖，包含代表合作夥伴使用者所做的協力廠商同盟伺服器的宣告。 在 ADFS 可以確認這些權杖有效傳遞至 Azure 中執行的 web 應用程式宣告之前。 （在 Azure) 的公司的 web 應用程式可以使用這些宣告授權要求。 在此案例中，公司的 web 應用程式*依賴廠商*，而且的合作夥伴同盟伺服器發出責任宣告的理解公司的 web 應用程式。 合作夥伴同盟伺服器稱為*帳戶合作夥伴*因為送出代表夥伴組織中的已驗證帳戶存取要求。 在 ADFS 伺服器稱為*資源合作夥伴*，因為他們提供存取的資源 （web 應用程式，在本例中）。

    - 他們可以驗證 (透過新增和[Active Directory 裝置註冊服務][ADDRS])，並授權傳入的邀請外部使用者執行的是網頁瀏覽器或需要您公司的 web 應用程式存取的裝置。 

    在 ADFS 伺服器設定為伺服器陣列，透過 Azure 負載平衡器存取。 此結構，可協助改善可用性能與延展性。 此外，請注意，在 ADFS 伺服器上公開直接到網際網路，而所有的網際網路流量透過 ADFS web 應用程式 proxy 伺服器和 DMZ 篩選。

- **在 ADFS proxy 子網路。** ADFS proxy 伺服器可以包含在自己的子網路，提供保護 NSG 規則。 子網路中的伺服器是透過網路虛擬就提供防火牆 Azure 虛擬網路和網際網路之間的一組網際網路上公開。

- **在 ADFS web 應用程式 proxy (WAP) 伺服器。** 這些電腦作為 ADFS 伺服器的內送和要求，合作夥伴組織外部的裝置。 WAP 伺服器做為篩選遮蔽直接存取公用網際網路 ADFS 伺服器。 就跟在 ADFS 伺服器部署 WAP 負載平衡與伺服器陣列中的伺服器可讓您更大的可用性和延展性大於部署獨立伺服器的集合。

    >[AZURE.NOTE] 如需詳細了解如何安裝 WAP 伺服器的詳細資訊，請參閱[安裝及設定 Web 應用程式 Proxy 伺服器][install_and_configure_the_web_application_proxy_server]

- **合作夥伴組織。** 這是範例合作夥伴組織中執行的 web 應用程式要求存取 Azure 中執行的 web 應用程式。 在合作夥伴組織同盟伺服器驗證本機，要求，並送出包含要 Azure 中執行的 ADFS 宣告的安全性權杖。 Azure 中的 ADFS 驗證的安全性權杖，並是否有效，可以將傳遞的 web 應用程式在 Azure 中執行這些授權宣告。

    >[AZURE.NOTE] 您也可以設定 VPN 通道使用 Azure 閘道器提供 ADFS 信任的協力廠商的直接存取。 從這些協力廠商收到要求不會通過 WAP 伺服器。

## <a name="recommendations"></a>建議

本節摘錄了實作 ADFS Azure 中的建議涵蓋︰

- VM 建議。

- 網路的建議。

- 可用的建議。

- 安全性的建議。

- 在 ADFS 安裝建議。

- 在 ADFS 信任的建議。

### <a name="vm-recommendations"></a>VM 建議

建立 Vm 足夠的資源來處理預期的流量音量。 使用現有的機器裝載 ADFS 內部部署的起點的大小。 監控資源使用狀況。 您可以調整大小 Vm 並縮小是否太大。

請遵循在[執行 Windows VM Azure 上]所列的建議[vm-recommendations]。

### <a name="networking-recommendations"></a>網路建議

設定網路介面的每個 Vm 主控 ADFS 和 WAP 伺服器私人的靜態 IP 位址。

不要使用 ADFS Vm 的公用 IP 位址。 如需詳細資訊，請參閱[的安全性考量][security-considerations]。

設定網路介面，每個 ADFS 和 WAP VM 參照 （這應該執行 DNS） 會將 Vm 慣用和次要 DNS 伺服器的 IP 位址。 此步驟是為了讓每個 VM 加入網域。

### <a name="availability-recommendations"></a>可用性建議

若要增加 ADFS 服務可用性至少兩個伺服器建立 ADFS 伺服器陣列。

在陣列中的每個 ADFS VM 使用不同的儲存帳戶。 這種方法可協助確保的單一儲存帳戶失敗不會讓整個伺服器陣列無法存取。

ADFS 和 WAP Vm 建立個別的 Azure 可用性集。 確定每個集合中都有兩個以上的 Vm。 每個可用性設定必須至少為兩個更新的網域和錯誤的兩個網域。

設定負載平衡器為 ADFS Vm 和 WAP Vm 如下所示︰

- 使用提供 WAP Vm 外部存取 Azure 的負載平衡器] 和 [分散在 ADFS 伺服器陣列中之 ADFS 伺服器上的載入至內部負載平衡器。

- 僅限通過出現在 ADFS/WAP 伺服器的連接埠 443 (HTTPS) 的流量。

- 授與負載平衡器的靜態 IP 位址。

- 建立使用 TCP 通訊協定，而不是 HTTPS 的狀況檢查。 您可以 ping 連接埠 443 驗證在 ADFS 伺服器的運作。

    >[AZURE.NOTE] 在 ADFS 伺服器使用的伺服器名稱的指示 (SNI) 通訊協定，因此嘗試探查使用 HTTPS 端點從負載平衡器失敗。

- 加入 ADFS 負載平衡器的網域 DNS*的*記錄。 

    指定負載平衡器的 IP 位址，並為它中該網域 （例如 adfs.contoso.com) 的名稱。 這是用戶端與 WAP 伺服器來存取 ADFS 伺服器陣列的名稱。

### <a name="security-recommendations"></a>安全性的建議

防止直接公開 ADFS 伺服器上網際網路。 在 ADFS 伺服器是擁有完整的授權，授與安全性權杖的網域的電腦。 如果在 ADFS 伺服器遭惡意使用者可以發行的完整存取權杖，所有 web 應用程式，並受到 ADFS 同盟伺服器。 如果您的系統必須處理不一定連線網站從信任的合作夥伴網站的外部使用者的要求，請使用 WAP 伺服器，來處理這些要求。 如需詳細資訊，請參閱在[何處放置同盟伺服器 Proxy][where-to-place-an-fs-proxy]。

位置 ADFS 伺服器和 WAP 伺服器中不同的子網路使用自己的防火牆。 若要定義防火牆規則，您可以使用 NSG 規則。 如果您需要更完整的保護您可以實作伺服器周圍額外的安全性外圍使用對子網路和 NVAs，來[實作安全混合的網路架構與 Azure 中的網際網路存取]文件所述[implementing-a-secure-hybrid-network-architecture-with-internet-access]。 請注意，所有防火牆應該都讓連接埠 443 (HTTPS) 的流量。

ADFS 和 WAP 伺服器限制直接登入存取。 僅限 DevOps 員工，應該能夠連線。

不加入 WAP 伺服器的網域]。

### <a name="adfs-installation-recommendations"></a>在 ADFS 安裝建議

[部署同盟伺服器陣列]的文件[Deploying_a_federation_server_farm]提供安裝及設定 ADFS 的詳細的指示。 在設定第一個在 ADFS 伺服器陣列中之前先執行下列工作︰

1. 取得執行伺服器驗證的公開信任的憑證。 *主體名稱*必須包含用戶端存取同盟服務的名稱。 這可能是針對負載平衡器，例如*adfs.contoso.com*註冊 DNS 名稱 (請避免使用萬用字元名稱，例如 **。 contoso.com*，基於安全性)。 在所有 ADFS 伺服器 Vm 使用相同的憑證。 您可以購買的憑證從信任的憑證授權單位，但如果您的組織使用 Active Directory 憑證服務您可以建立您自己。 

    DRS 會使用*主體的別名*，啟用來自外部裝置存取。 這必須是表單*enterpriseregistration.contoso.com*。

    如需詳細資訊，請參閱[取得及設定 SSL 憑證的 ADFS][adfs_certificates]。

2. 網域控制站，產生新的根目錄機碼的金鑰發佈服務。 設定目前的時間 （此設定會降低在發佈並同步處理整個網域的按鍵中可能發生的延遲） 的 10 個小時之內的實際時間。 此步驟才能支援建立用來執行 ADFS 服務群組服務帳戶。 下列 Powershell 命令顯示如何進行此動作的範例︰

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. 新增每個在 ADFS 伺服器 VM 網域。

>[AZURE.NOTE] 若要安裝 ADFS，執行肯定 FSMO 角色之網域的網域控制站必須執行，且可從 ADFS Vm 存取。

### <a name="adfs-trust-recommendations"></a>在 ADFS 信任的建議

建立同盟信任 ADFS 安裝，與同盟伺服器之間的任何合作夥伴。 設定任何篩選的宣告和所需的對應。 此程序需要︰

- DevOps 教職員**在每個合作夥伴組織**新增依賴廠商信任以存取您在 ADFS 伺服器的 web 應用程式。

- DevOps 教職員**您組織中**設定宣告提供者信任]，在 ADFS 伺服器信任合作夥伴提供的宣告。

- DevOps 教職員**您組織中**設定 ADFS 傳遞宣告哪貴組織的 web 應用程式。

    如需詳細資訊，請參閱[建立同盟信任][establishing-federation-trust]。

發佈貴組織的 web 應用程式，並且使用外部的合作夥伴，才能使用預先透過 WAP 伺服器驗證。 如需詳細資訊，請參閱[發佈應用程式使用 ADFS 預先驗證][publish_applications_using_AD_FS_preauthentication]

請注意 ADFS 支援 token 轉換及寶石。 Azure Active Directory 不提供這項功能。 使用 ADFS，當您設定的信任關係，您可以︰

- 設定宣告轉換的授權規則。 例如，您可以將對應從表示該相加可以授權貴組織中的項目非 Microsoft 合作夥伴組織所使用的群組安全性。

- 轉換到另一個格式宣告。 例如，您可以將對應 SAML 2.0 SAML 1.1 （英文） 如果您的應用程式只支援 SAML 1.1 （英文） 宣告。 

## <a name="availability-considerations"></a>可用性考量

您可以使用 SQL Server] 或 [Windows 內部資料庫 (WID) 如要保留 ADFS 設定資訊。 WID 提供基本重複。 變更會直接寫入其中一個 ADFS ADFS 叢集資料庫，而其他伺服器使用提取複寫保持在最新的資料庫。 使用 SQL Server，可提供完整的資料庫重複性和使用容錯移轉叢集或鏡像的可用性。

## <a name="security-considerations"></a>安全性考量

ADFS 利用 HTTPS 通訊協定，因此請確定包含網頁的子網路的 NSG 規則層 Vm 允許 HTTPS 要求。 這些要求可以來自內部部署網路，包含 web 層、 商務層、 資料層、 私人 DMZ、 公用 DMZ，以及包含在 ADFS 伺服器的子網路的子網路。

請考慮使用一組網路虛擬應用裝置的記錄流量通過虛擬網路的稽核用途的邊緣的詳細的資訊。

## <a name="scalability-considerations"></a>延展性考量

從文件[計劃 ADFS 部署]彙總下列考量[plan-your-adfs-deployment]，授予起點縮放 ADFS 伺服器陣列︰

- 如果您有 1000 個使用者，無法建立專用的 ADFS 伺服器，但改為在每一個雲端中的新增伺服器上安裝 ADFS （請確定您有兩個以上的新增伺服器，若要維持可用性）。 建立單一 WAP 伺服器。

- 如果您有 1000年和 15000 使用者之間，建立兩個專用的 ADFS 伺服器和兩個專用的 WAP 伺服器。

- 如果您有 15000 和 60000 使用者之間，建立之間 3 到 5 個專用的 ADFS 伺服器及至少兩個專用的 WAP 伺服器。

這些數字，假設您使用的雙四核心 Vm （標準 D4_v2 或更好） 裝載 Azure 中的伺服器。

請注意，如果您使用的 Windows 內部資料庫來儲存 ADFS 設定資料，您僅限於八個 ADFS 伺服器陣列中。 如果您將會需要更多，然後使用 SQL Server。 如需詳細資訊，請參閱[ADFS 設定資料庫的角色][adfs-configuration-database]。

## <a name="management-considerations"></a>管理考量事項

還需要準備 DevOps 員工，執行下列工作︰

- 管理同盟伺服器 （管理 ADFS 伺服器陣列、 管理同盟伺服器上的信任原則和管理同盟服務所使用的憑證）。

- 管理 WAP 伺服器 （管理 WAP 伺服器陣列，管理 WAP 憑證）。

- 管理 web 應用程式 （依賴的廠商、 驗證方法和宣告對應設定）。

- 備份 ADFS 元件。

## <a name="monitoring-considerations"></a>監控考量

[Microsoft 系統管理中心 Management Pack for Active Directory Federation Services 2012 R2] [oms-adfs-pack]主動式與被動式監視 ADFS 部署同盟伺服器。 此管理封包監視︰

- 在 ADFS 服務記錄 ADFS 事件記錄檔中的事件。

- 在 ADFS 效能計數器收集效能資料。 

- 整體狀況 ADFS 系統和 web 應用程式 （依賴廠商），並提供重大問題和警告的通知。

## <a name="solution-components"></a>解決方案元件

範例解決方案指令碼，[部署 ReferenceArchitecture.ps1][solution-script]，提供您可以使用實作架構的本文所述的建議。 這個指令碼利用 Azure 資源管理員範本。 為每個執行特定動作，例如建立 VNet 或設定 NSG 基本的建置組塊，一組可範本。 指令碼的目的，是協調範本部署。

範本會參數化，以不同的 JSON 檔案中的參數。 您可以修改設定以符合您自己的需求部署這些檔案中的參數。 您不需要修改範本本身。 請注意，您必須變更參數檔案中的物件的結構描述。

當您編輯的樣板時，建立物件追蹤[建議的命名慣例 Azure 資源]所述的命名慣例[naming-conventions]。

範例方案會建立並設定環境中包含新增子網路和伺服器 ADFS 子網路與伺服器、 ADFS proxy 子網路和伺服器 DMZ、 網頁層、 商務層及資料存取層元件、 VPN 閘道和管理層雲端。 範例方案也包含選擇性的設定，以建立模擬內部部署環境。

下列各節說明的元素的內部部署和雲端設定。

### <a name="on-premises-components"></a>內部部署元件

>[AZURE.NOTE] 這些元件無法在此文件中所描述的架構的主要工作，以及可供您只是讓您得以安全地，而不是使用實數生產環境中測試雲端環境。 因此，此區段只摘要索引鍵的參數檔案。 您可以修改設定，例如 IP 位址或 Vm 的大小，但建議您離開的許多其他參數保持不變。

這種環境中包括網域名稱為 contoso.com AD 樹系。 網域中包含兩個 192.168.0.4 和 192.168.0.5 的 IP 位址新增伺服器。 下列兩個伺服器也會執行 DNS 服務。 本機系統管理員帳戶在兩個 Vm 稱為`testuser`密碼`AweS0me@PW`。 此外，設定為需連線至雲端 VNet VPN 閘道器。 您可以修改設定，編輯下列 JSON 檔案位於[**參數/onpremise**] [on-premises-folder]資料夾︰

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**。 這個檔案定義內部部署環境的網路位址空間。

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**。 這個檔案包含內部部署 Vm 主機新增服務的設定。 根據預設，會建立兩個*標準-DS3-v2* Vm。

- ** [virtualNetworkGateway.parameters.json] [on-premises-virtualnetworkgateway-parameters]**和**[connection.parameters.json][on-premises-connection-parameters]**。 這些檔案會保留在雲端，包括共用要用來保護流量通過閘道器金鑰 Azure VPN 閘道器的 VPN 連線的設定。

在資料夾中的剩餘檔案包含用來建立使用此基礎結構的內部部署網域的設定資訊。 您用來安裝新增、 設定 DNS、 建立樹系和設定樹系複寫網站。

### <a name="cloud-components"></a>雲端元件

這些元件表單此架構的核心。 [**參數/azure**] [azure-folder]資料夾包含下列參數檔案設定下列元件︰

- ** [virtualNetwork.parameters.json][vnet-parameters]**。 這個檔案定義 VNet 的結構 Vm 和雲端中的其他元件。 其包含設定，例如名稱、 地址空間、 子網路及任何所需的 DNS 伺服器的位址。 請注意 DNS 所顯示的位址在此範例中參考的內部部署的 DNS 伺服器，以及預設 Azure DNS 伺服器 IP 位址。 修改參考您自己的 DNS 設定，如果您不使用範例內部部署環境這些地址︰

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **。 此檔案設定 Vm 雲端中執行相加。 設定包含兩個 Vm。 管理使用者名稱和密碼，您應該變更`virtualMachineSettings`] 區段中，而且您也可以修改虛擬記憶體大小以符合的網域需求︰

    如需詳細資訊，請參閱[延伸至 Azure 的 Active Directory][extending-ad-to-azure]。

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- **[新增-新增的網域-controller.parameters.json][add-adds-domain-controller-parameters]**。 此檔案會包含建立橫跨相加伺服器 CONTOSO 網域的設定。 使用自訂延伸，建立網域，並為其新增新增伺服器。 除非您建立其他新增伺服器 (在此情況下，您應該新增他們`vms`陣列)、 預設值，變更其名稱，或想要使用不同的名稱，您不需要修改此檔案建立網域。

- ** [loadBalancer adfs.parameters.json] [loadbalancer-adfs-parameters] **檔案含有兩組設定參數。 `virtualMachineSettings`區段定義 Vm 裝載在雲端 ADFS 服務。 根據預設，指令碼會建立兩個 Vm 在相同的可用性設定︰

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    `loadBalancerSettings`節提供這些負載平衡器的描述 Vm。 負載平衡器會將會出現在連接埠 443 (HTTPS) 的流量傳遞給一或其他的 Vm:

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs 伺服器陣列-網域 join.parameters.json ] [ adfs-farm-domain-join-parameters] **。 這個檔案包含用來新增 CONTOSO 網域 ADFS 伺服器的設定。 您只需要修改此檔案，如果您已建立其他 ADFS 伺服器 (更新`vms`陣列在此情況下)，或您已變更的網域名稱。

- ** [gmsa.parameters.json][gmsa-parameters]**， ** [adfs-伺服器陣列-first.parameters.json][adfs-farm-first-parameters]**，及**[adfs-伺服器陣列-rest.parameters.json][adfs-farm-rest-parameters]**。 指令碼會使用這些檔案中的設定，以建立 ADFS 伺服器陣列。 

    *Gmsa.parameters.json*檔案包含 ADFS 服務所使用的群組受管理的服務帳戶的設定。 如果您想要變更的帳戶或的網域名稱，您可以修改此檔案。

    *在 adfs-伺服器陣列-first.parameters.json*檔案保留建立 ADFS 伺服器陣列，並將第一個伺服器所需的資訊。 如果您有變更的網域或群組受管理的服務帳戶的名稱，您應該更新此檔案。

    *在 adfs-伺服器陣列-rest.parameters.json*檔案用來新增其餘的 ADFS 伺服器陣列。 同樣地，如果您有變更的網域或群組受管理的服務帳戶的名稱，您應該更新此檔案。 更新`vms`如果您已建立其他 ADFS 伺服器陣列。

- ** [loadBalancer adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**。 此檔案是類似結構與內容*loadBalancer adfs.parameters.json*檔案。 包含建置 ADFS proxy 伺服器和負載平衡器的資料。

- ** [adfsproxy-伺服器陣列-first.parameters.json][adfsproxy-farm-first-parameters]**，及**[adfsproxy-伺服器陣列-rest.parameters.json][adfsproxy-farm-rest-parameters]**。 指令碼會使用這些檔案中的設定，以建立 ADFS proxy 伺服器陣列。 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**。 這個檔案包含用來建立 Azure VPN 閘道器用來連線到內部部署網路雲端中的設定。 您應該修改`sharedKey`中的值`connectionsSettings`以符合的內部部署 VPN 裝置的區段。 如需詳細資訊，請參閱[實作混合的網路架構與 Azure 及內部部署 VPN][hybrid-azure-on-prem-vpn]。

- ** [dmz private.parameters.json] [dmz-private-parameters]**和**[dmz public.parameters.json ] [ dmz-public-parameters] **。 這些檔案設定輸入 （公開） 輸出 （私人） 兩側 Vm 包含 DMZ 保護雲端中的伺服器。 如需有關這些項目] 和 [其設定的詳細資訊，請參閱[實作 Azure 與網際網路之間 DMZ][implementing-a-secure-hybrid-network-architecture-with-internet-access]。

- ** [loadBalancer-web.parameters-json][loadBalancer-web-parameters]**， ** [loadBalancer-biz.parameters-json][loadBalancer-biz-parameters]**，及**[loadBalancer-data.parameters-json][loadBalancer-data-parameters]**。 這些參數檔案包含網頁、 商業及資料存取層級的 VM 規格，並設定的每一層的負載平衡器。 這些是 Vm 主控 web 應用程式，並資料庫，並執行商務工作負載的組織。 您可以根據您的需求修改大小和 Vm 的每一層的數字。

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**。 這個檔案包含跳轉] 方塊/管理 Vm 的設定。 您才可以取得登入與管理存取權網頁、 企業版和資料層 Vm 跳轉] 方塊。 根據預設，指令碼會建立單一*Standard_DS1_v2* VM，但您可以修改這個檔案來建立更大或其他 Vm 時可能會嚴重管理工作量。

## <a name="solution-deployment"></a>解決方案部署

解決方案假設下列先決條件︰

- 您有現有的 Azure 訂閱，您可以在其中建立資源群組。

- 您必須下載並安裝最新的 Powershell 的 Azure 建置。 請參閱[以下][azure-powershell-download]如需相關指示。

若要執行部署解決方案的指令碼︰

1. 在您的本機電腦上移動最方便的資料夾，並建立下列子資料夾︰

    - 指令碼

    - 指令碼/參數

    - 指令碼/參數/Onpremise

    - 指令碼/參數/Azure

2. 下載[部署 ReferenceArchitecture.ps1] [solution-script]的指令碼資料夾的檔案

3. 下載內容的[參數/onpremise] [on-premises-folder]指令碼/參數/Onpremise 資料夾的資料夾︰

4. 下載內容的[參數/azure] [azure-folder]指令碼/參數/Azure 資料夾的資料夾。

5. 部署 ReferenceArchitecture.ps1 中編輯檔案的指令碼資料夾，並變更下列行指定的資源群組，必須建立，或用來儲存建立的指令碼的資源︰

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. 參數中編輯檔案的指令碼/參數/Onpremise 和指令碼/參數/Azure 資料夾。 更新資源] 群組中的參考這些檔案，以符合部署 ReferenceArchitecture.ps1 檔案中的變數分派的資源群組的名稱。 下表顯示哪些參數檔案參照的資源群組。 注意*ra adfs-工作量路由*、 *ra adfs-安全性路由*、 *ra-adfs-新增-路由*、 *ra adfs-adfs 路由*及*ra adfs-proxy 路由*群組只能用於 PowerShell 指令碼，並不會發生參數檔案中。

  	|資源群組|參數檔案|
  	|--------------|--------------|
  	|ra adfs-onpremise 路由|parameters\onpremise\connection.parameters.json<br /> parameters\onpremise\virtualMachines-adds.parameters.json<br />parameters\onpremise\virtualNetwork-adds-dns.parameters.json<br />parameters\onpremise\virtualNetwork.parameters.json<br />parameters\onpremise\virtualNetworkGateway.parameters.json<br />parameters\azure\virtualNetworkGateway.parameters.json
  	|ra adfs-網路路由|parameters\onpremise\connection.parameters.json<br />parameters\azure\dmz-private.parameters.json<br />parameters\azure\dmz-public.parameters.json<br />parameters\azure\loadBalancer-adfs.parameters.json<br />parameters\azure\loadBalancer-adfsproxy.parameters.json<br />parameters\azure\loadBalancer-biz.parameters.json<br />parameters\azure\loadBalancer-data.parameters.json<br />parameters\azure\loadBalancer-web.parameters.json<br />parameters\azure\virtualMachines-adds.parameters.json<br />parameters\azure\virtualMachines-mgmt.parameters.json<br />parameters\azure\virtualNetwork-with-onpremise-and-azure-dns.parameters.json<br />parameters\azure\virtualNetwork.parameters.json<br />parameters\azure\virtualNetworkGateway.parameters.json （*兩個*）

    此外，設定的內部部署和雲端中的 [方案元件] 和 [方案元件] 一節所述的元件]。

7. 開啟 PowerShell 的 Azure 視窗，移至 [指令碼] 資料夾，然後執行下列命令︰

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    取代`<subscription id>`您 Azure 訂閱 id。

    針對`<location>`，指定 Azure 的區域，例如`eastus`或`westus`。

    `<mode>`參數會擁有下列的值︰

    - `Onpremise`來建立模擬內部部署環境。

    - `Infrastructure`建立 VNet 基礎結構，並跳雲端中的方塊。

    - `CreateVpn`若要建立 Azure 虛擬網路閘道器，並將其連線至內部部署網路。

    - `AzureADDS`以建構 Vm 做為新增伺服器，這些 Vm 部署 Active Directory 和在雲端建立網域。

    - `AdfsVm`若要建立 ADFS Vm 並加入雲端中的網域。

    - `ProxyVm`若要建立 ADFS proxy Vm 並加入雲端中的網域。

    - `Prepare`會執行上述的所有工作。 **如果您要建立全新的部署，且您未現有的內部部署基礎結構，這是建議的選項。**

    >[AZURE.NOTE] 您也可以執行的指令碼`<mode>`的參數`Workload`來建立網站、 商務和資料層 Vm 和網路。 此設定不包含`Prepare`模式。

    如果您是使用`Prepare`選項，指令碼會完成，數個小時，而訊息*準備完成已完成。請安裝憑證所有 ADFS 和 proxy Vm。*

8.  重新啟動 (*ra adfs-管理 vm1* *ra adfs-安全性路由*] 群組中) 上的 [跳轉] 方塊，以允許其 DNS 設定，才會生效。

9.  [取得 SSL 憑證 ADFS] [adfs_certificates]並在 ADFS Vm 安裝這個憑證。 請注意，您可以連線至 ADFS Vm 透過跳轉] 方塊。 IP 位址是*10.0.5.4*和*10.0.5.5*。 預設使用者名稱與密碼*contoso\testuser* *AweSome@PW*。

    >[AZURE.NOTE] 此時部署 ReferenceArchitecture.ps1 指令碼中的註解提供的詳細的指示，以建立自我簽署的測試憑證和授權使用`makecert`] 命令。 不過，不要使用 makecert 生產環境中所產生的憑證。

10. 執行下列 Powershell 命令以設定 ADFS 伺服器陣列︰

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. 在 [跳轉] 方塊中，瀏覽至*https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm*測試 （您可能會收到憑證警告，您可以略過這項測試） ADFS 安裝。 確認 [Contoso Corporation 登入] 頁面隨即出現。 登入密碼*contoso\testuser* *AweS0me@PW*。

12. 在 ADFS proxy Vm 安裝 SSL 憑證。 IP 位址是*10.0.6.4*和*10.0.6.5*。

13. 執行下列 Powershell 命令，將第一個 ADFS proxy 伺服器設定︰

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. 依照顯示的指令碼測試第一個 ADFS proxy 伺服器的安裝指示。

15. 執行下列 Powershell 命令，將第二個的第一個 ADFS proxy 伺服器設定︰

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. 依照指示顯示由指令碼來測試完成 ADFS proxy 設定。

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory][aad]。

- [Azure Active Directory B2C][aadb2c]。

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "安全的混合式網路架構與 Active Directory"
