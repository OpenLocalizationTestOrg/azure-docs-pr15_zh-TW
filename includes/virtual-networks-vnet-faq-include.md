## <a name="virtual-network-basics"></a>虛擬網路的基本概念

### <a name="what-is-an-azure-virtual-network-vnet"></a>什麼是 Azure 虛擬網路 (VNet)？

您可以使用 VNets 佈建和管理 Azure 虛擬私人網路 (Vpn) 並，或者，您可以連結 VNets Azure 中的其他 VNets 或您的內部部署 IT 基礎架構，來建立混合式或跨內部部署的解決方案。 您建立的每個 VNet 有其專屬 CIDR 區塊，，而且可以連結至其他 VNets 和內部部署網路中，只要不衝突 CIDR 區塊，請執行。 您也可以控制項的 DNS 伺服器設定為 VNets，並將子網路的 VNet 分割。

若要使用 VNets:

- 建立專屬私人雲端專用虛擬網路

    有時候您不需要跨內部部署設定解決方案。 當您建立 VNet 時，您的服務與您 VNet 內 Vm 可以彼此直接和安全地在雲端。 這會保留安全地內 VNet，流量，但仍可讓您設定為 Vm 和服務的要求屬於您的方案的網際網路通訊的端點連線。

- 安全地延長您的資料中心

    使用 VNets，您可以建立傳統網站為網站 (S2S) Vpn 安全地調整您的資料中心的容量。 S2S Vpn 使用 IPSEC 提供您的公司 VPN 閘道和 Azure 之間的安全連線。

- 啟用混合式雲端案例

    VNets 提供支援範圍的混合式雲端案例的彈性。 您可以安全地連接雲端任何類型的內部部署系統，例如大型主機和 Unix 系統應用程式。

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>如何知道我是否需要虛擬網路？

請造訪[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)以查看可協助您決定最佳的網路設計選項，讓您決定資料表。

### <a name="how-do-i-get-started"></a>如何開始？

請造訪[虛擬網路文件](https://azure.microsoft.com/documentation/services/virtual-network/)，即可開始使用。 此頁面包含連結至通用設定步驟，以及資訊可協助您瞭解您將需要設計虛擬網路時，請考慮的事項。

### <a name="what-services-can-i-use-with-vnets"></a>我可以使用哪些服務搭配 VNets？

VNets 可以使用各種不同的 Azure 服務，例如雲端服務 (PaaS)、 虛擬機器和 Web 應用程式。 然而，有幾個 VNet 不支援的服務。 請檢查您想要使用，並確認其為相容的特定服務。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>可以使用 VNets 沒有跨內部部署連線嗎？

[是]。 您可以使用 VNet，而不使用網站的連線。 這是您想要執行 Azure 中的網域控制站與 SharePoint 伺服器陣列的特別有用。

## <a name="virtual-network-configuration"></a>虛擬網路設定

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>若要建立 VNet 使用什麼工具？

若要建立或設定虛擬網路，您可以使用下列工具︰

- Azure 入口網站 (如傳統和資源管理員 VNets)。

- 網路設定檔 (netcfg-的標準 VNets 只)。 請參閱[設定使用網路的設定檔的虛擬網路](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)。

- PowerShell (如傳統和資源管理員 VNets)。

- Azure CLI (如傳統和資源管理員 VNets)。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>在 [我的 VNets 中可以使用哪些位址範圍？

您可以使用公用 IP 位址範圍及[RFC 1918](http://tools.ietf.org/html/rfc1918)中定義的任何 IP 位址範圍。

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>可以在我 VNets 有的公用 IP 位址嗎？

[是]。 如需有關公用 IP 位址範圍的詳細資訊，請參閱[在虛擬網路 (VNet) 中的公用 IP 位址空間](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md)。 請記住，不會直接從網際網路存取您的公用 Ip。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>是否有我虛擬網路中的子網路數限制？

沒有任何限制，您使用 VNet 內的子網路數目。 所有的子網路必須完全包含在虛擬網路位址空間，並不應該彼此重疊。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>是否有使用這些子網路中的 IP 位址的任何限制？

Azure 會保留在每一個子網路內一些 IP 位址。 在子網路的第一個和最後一個 IP 位址被保留通訊協定符合，以及用於 Azure 服務的 3 個更多地址。

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>小型及如何大型可以方式就是 VNets 和子網路嗎？

我們支援的最小子網路 /29，而最大會 （使用 CIDR 子網路定義） / 8。

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>匯我虛擬至 Azure 使用 VNets？

[否]。 VNets 是 Layer-3 重疊。 Azure 不支援任何第 2 層語意。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>可以在我的 VNets 和子網路上指定自訂路由原則？

[是]。 您可以使用使用者定義路由 (UDR)。 如需有關 UDR 的詳細資訊，請造訪[使用者定義的路徑及 IP 轉寄功能](../articles/virtual-network/virtual-networks-udr-overview.md)。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNets 是否支援多點或廣播？

[否]。 我們不支援點傳送或廣播。

### <a name="what-protocols-can-i-use-within-vnets"></a>VNets 中可以使用哪些通訊協定？

您可以使用 VNets 內的標準通訊協定。 不過，多點、 廣播、 IP 中 IP 封裝封包和一般路由封裝 (GRE) 封包封鎖 VNets 內。 標準通訊協定工作，包括︰

- TCP
- UDP
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>可以 ping 內 VNet 我預設路由器嗎？

[否]。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>可以使用 tracert 診斷連線嗎？

[否]。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>建立 VNet 後可以新增子網路嗎？

[是]。 隨時都可以子網路新增至 VNets，只要子網路位址不是在 VNet 中的另一個子網路的一部分。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>我在建立之後可以修改我子網路的大小嗎？

您可以新增、 移除、 擴展或縮小子網路，如果沒有 Vm 或部署內使用 PowerShell cmdlet 或 NETCFG 檔案的服務。 您也可以新增、 移除、 展開或縮小任何前置詞，只要變更不會影響包含 Vm 或服務的子網路。

### <a name="can-i-modify-subnets-after-i-created-them"></a>我在建立之後可以修改子網路嗎？

[是]。 您可以新增、 移除及修改使用 VNet CIDR 區塊。

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>我可以連線到網際網路若 VNet 中執行我的服務？

[是]。 部署 VNet 內的所有服務可以都連線至網際網路。 部署 Azure 中每個雲端服務已指派給它公開定址 VIP。 您必須定義輸入的結束點的 PaaS 角色和虛擬機器啟用這些服務，接受來自網際網路連線的端點。

### <a name="do-vnets-support-ipv6"></a>VNets 支援 IPv6？

[否]。 您無法使用 IPv6 VNets 這一次。

### <a name="can-a-vnet-span-regions"></a>可以 VNet 跨越區域？

[否]。 VNet 僅限於單一的區域。

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>連線至 Azure 中的另一個 VNet VNet 可以？

[是]。 您可以使用 REST Api 或 Windows PowerShell VNet 通訊的檔案，以建立 VNet。 您也可以連線 VNets 透過 VNet 對等。 請參閱對等的詳細[以下。](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>名稱解析 (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>什麼是 VNets 我 DNS 的選項？

使用在[Vm 和角色執行個體名稱解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)] 頁面上的決策表格可引導您完成所有 DNS 可用的選項。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>我可以指定 VNet DNS 伺服器？

[是]。 您可以在 VNet 設定中指定的 DNS 伺服器 IP 位址。 這將會套用為預設 DNS 伺服器 VNet 中的所有 Vm。

### <a name="how-many-dns-servers-can-i-specify"></a>可以指定多少 DNS 伺服器？

您可以指定最多 12 DNS 伺服器。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>我有建立網路後可以修改我的 DNS 伺服器嗎？

[是]。 您可以隨時變更您 VNet DNS 伺服器清單。 如果您變更您的 DNS 伺服器清單，您必須重新啟動每個 Vm 您 VNet 中，挑選 [新的 DNS 伺服器。


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>什麼是 Azure 所提供的 DNS，它可以搭配 VNets？

Azure 所提供的 DNS 是由 Microsoft 提供多租用戶 DNS 服務。 Azure 註冊您的所有 Vm 和角色執行個體在此服務。 這項服務提供名稱解析 Vm 和角色執行個體包含在相同的雲端服務，主機名稱及 FQDN Vm 和相同 VNet 中的角色執行個體。

> [AZURE.NOTE] 沒有限制，此時虛擬網路中的第一個 100 雲端服務進行跨租用戶名稱解析使用 Azure 所提供的 DNS。 如果您使用您自己的 DNS 伺服器，這項限制不適用。

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>我可以在每個 VM 我 DNS 設定會覆寫/服務為基礎嗎？

[是]。 您可以設定覆寫預設網路設定針對每個雲端服務的 DNS 伺服器。 不過，我們建議您使用網路整個 DNS 盡可能。

### <a name="can-i-bring-my-own-dns-suffix"></a>我可以將自己的 DNS 尾碼嗎？

[否]。 您無法將 VNets 指定自訂的尾碼。

## <a name="vnets-and-vms"></a>VNets 和 Vm

### <a name="can-i-deploy-vms-to-a-vnet"></a>我可以將 Vm 部署到 VNet 中？

[是]。

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>我可以對 VNet 部署 Linux Vm 嗎？

[是]。 您可以部署 Linux Azure 所支援的任何 distro。

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>什麼是公用 VIP 和內部 IP 位址之間的差異？

- 內部 IP 位址是由 DHCP 分派給每個 VM VNet 內的 IP 位址。 沒有公用公開。 如果您已建立 VNet，在您 VNet 的子網路設定所指定的範圍中指派的內部 IP 位址。 如果您沒有 VNet，還是被指派內部 IP 位址。 內部的 IP 位址會保持與 VM 存留，除非的 VM 的配置。

- 公用 VIP 是分派給您的雲端服務或負載平衡器的公用 IP 位址。 未指派給您 VM NIC 直接 VIP 保持雲端服務會被指派到直到所有 Vm 的雲端服務會取消配置，或刪除。 此時，已發行。

### <a name="what-ip-address-will-my-vm-receive"></a>我 VM 會收到哪些 IP 位址？

- **內部的 IP 位址-**如果您要 VNet 部署 VM，VM 會從您指定的內部 IP 位址的集區接收內部 IP 位址。 Vm 通訊 VNets 內使用內部 IP 位址。 雖然 Azure 指派動態內部 IP 位址，您可以要求您 VM 靜態地址。 若要進一步瞭解內部的靜態 IP 位址，請造訪[如何設定靜態內部 ip 位址](../articles/virtual-network/virtual-networks-reserved-private-ip.md)。

- **-VIP**雖然 VIP 不直接指派給 VM，您 VM 也是 VIP，與相關聯。 VIP 是可以指派給您的雲端服務的公用 IP 位址。 您也可以可以保留 VIP 雲端服務。

- **-ILPIP**您也可以設定執行個體層級的公用 IP 位址 (ILPIP)。 ILPIPs 是直接 VM，而不是雲端服務相關聯。 若要進一步瞭解 ILPIPs，請造訪[執行個體層級的公用 IP 概觀](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)。

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>可以稍後會建立的 vm 保留內部 IP 位址嗎？

[否]。 您無法保留內部 IP 位址。 如果內部 IP 位址是可用會被指派到 VM 或角色的執行個體，DHCP 伺服器。 該 VM 可能，或可能不是您想要指派給的內部 IP 位址的項目。 您不過，可以變更已建立的 VM 內部 IP 位址到任何可用的內部 IP 位址。

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>執行 Vm 中 VNet 內部的 IP 位址變更？

[是]。 內部 IP 位址會維持與 VM 存留除非 VM 的配置。 當指標的配置 VM 時，除非您為您 VM 定義內部的靜態 IP 位址放開內部的 IP 位址。 如果 VM 只要停止 （並不會置於狀態**已停止 (Deallocated)**） 的 IP 位址仍會維持指派的 vm。

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>可以 Vm 在 Nic 以手動指派 IP 位址嗎？

[否]。 您不能變更 Vm 任何介面屬性。 任何變更可能會導致潛在的 vm 失去連線。

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>萬一我的 IP 位址關機 VM 怎麼辦？

執行任何動作。 您的雲端服務或 VM 會保留 （公用 VIP 和內部 IP 位址） 的 IP 位址。

> [AZURE.NOTE] 如果您想要直接關閉 VM，不使用 [管理] 入口網站執行此作業。 目前的 [關閉] 按鈕會解除虛擬機器。

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>我可以移動 Vm 從一個子網路到另一個子網路中 VNet 而不重新部署嗎？

[是]。 您可以找到更多資訊[以下](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md)。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>可以針對我 VM 設定靜態 MAC 位址嗎？

[否]。 無法靜態設定 MAC 地址。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>將 MAC 地址保持相同的我 VM，只要建立之後嗎？

是的 MAC 地址會維持不變 vm 即使 VM 已停止 （解除配置） 並重新啟動。

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>我可以連線至網際網路從中 VNet VM 嗎？

[是]。 部署 VNet 內的所有服務可以都連線至網際網路。 此外，部署 Azure 中每個雲端服務已指派給它公開定址 VIP。 您必須定義輸入的結束點的 PaaS 角色和端點 Vm 啟用這些服務，接受來自網際網路連線。

## <a name="vnets-and-services"></a>VNets 及服務

### <a name="what-services-can-i-use-with-vnets"></a>我可以使用哪些服務搭配 VNets？

您可以只使用 VNets 中的計算服務。 計算服務限於雲端服務 （web 及工作者角色） 和 Vm。

### <a name="can-i-use-web-apps-with-virtual-network"></a>可以使用 Web 應用程式與虛擬網路嗎？

[是]。 您可以部署 VNet，使用 ASE （應用程式服務環境） 內的 Web 應用程式。 新增至 Web 應用程式，可以安全地連線，或如果您有點-網站設定您的 VNet 存取您的 Azure VNet 中的資源。 如需詳細資訊，請參閱下列各項︰


- [在 [應用程式服務環境中建立 Web 應用程式](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Web 應用程式虛擬網路的整合](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Web 應用程式中使用 VNet 整合及混合式連線](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [在 web 應用程式整合 Azure 虛擬網路](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>可以部署與 web 及背景工作中的角色 (PaaS) VNet 雲端服務嗎？

[是]。 您可以部署 VNets PaaS 服務。

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>如何將 PaaS 角色部署到 VNet？

您可以指定 VNet 名稱] 和 [角色 /subnet 對應網路組態] 區段中的 [您的服務設定，以完成。 您不需要任何您的二進位檔案的更新。

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>我可以移動我的服務與 VNets 登出嗎？

[否]。 您無法移動與 VNets 登出服務。 您必須刪除並重新部署將其移到另一個 VNet 服務。

## <a name="vnets-and-security"></a>VNets 與安全性

### <a name="what-is-the-security-model-for-vnets"></a>什麼是 VNets 的安全性模型？

VNets 是完全獨立於彼此之上，並裝載於 Azure 基礎結構的其他服務。 VNet 是信任邊界。

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>可以在我 VNets 定義 Acl 或 NSGs 嗎？

[否]。 您必須 Acl 或 NSGs 至 VNets 無法建立關聯。 不過，可以為 Vm 已部署至 VNets 中，輸入的端點上定義 Acl，NSGs 可子網路或 Nic 相關聯。

### <a name="is-there-a-vnet-security-whitepaper"></a>有 VNet 安全性白皮書嗎？

[是]。 您可以下載[此處](http://go.microsoft.com/fwlink/?LinkId=386611)。

## <a name="apis-schemas-and-tools"></a>Api、 結構描述，以及工具

### <a name="can-i-manage-vnets-from-code"></a>管理 VNets 從程式碼？

[是]。 您可以使用 REST Api 來管理 VNets 和交互內部部署的連線。 詳細資訊，請參閱[以下](http://go.microsoft.com/fwlink/?LinkId=296833)。

### <a name="is-there-tooling-support-for-vnets"></a>有 VNets 工具支援嗎？

[是]。 您可以使用 PowerShell 和命令列工具各式各樣的平台。 詳細資訊，請參閱[以下](http://go.microsoft.com/fwlink/?LinkId=317721)。
