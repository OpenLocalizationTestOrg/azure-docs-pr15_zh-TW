<properties
   pageTitle="公用與私人 IP 位址 （傳統） 的 Azure 中 |Microsoft Azure"
   description="進一步瞭解公開及私密金鑰的 IP 位址 Azure 中"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Azure 中的 IP 位址 （傳統）
您可以與其他 Azure 資源、 內部網路和網際網路通訊 Azure 資源指定的 IP 位址。 有兩種類型的 Azure 中，您可以使用的 IP 位址︰ 公用與私人。

公用 IP 位址用於與網際網路，包括 Azure-在公開服務的通訊。

當您使用的 VPN 閘道器或 ExpressRoute 電路將您的網路延伸到 Azure 私人 IP 位址用於內 Azure 虛擬網路 (VNet)、 雲端服務和您的內部網路的通訊。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員部署模型](virtual-network-ip-addresses-overview-arm.md)。

## <a name="public-ip-addresses"></a>公用 IP 位址
公用 IP 位址允許 Azure 資源進行通訊的網際網路和 Azure-在公開服務，例如[Azure Redis 快取](https://azure.microsoft.com/services/cache/)、 [Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)、 [SQL 資料庫](../sql-database/sql-database-technical-overview.md)和[Azure 儲存空間](../storage/storage-introduction.md)。

公用 IP 位址是下列的資源類型相關聯的︰

- 雲端服務
- IaaS 虛擬機器 (Vm)
- PaaS 角色執行個體
- VPN 閘道器
- 應用程式的閘道器

### <a name="allocation-method"></a>配置方法
公用 IP 位址需要指派給 Azure 的資源，則*動態*配置集區中的 [可用的公用 IP 位址中建立資源的位置。 停止資源時，會釋放這個 IP 位址。 萬一的雲端服務，這種情況時停止所有角色例項，其可避免使用*靜態*（保留） 的 IP 位址 （請參閱[雲端服務](#Cloud-services)）。

>[AZURE.NOTE] 從 Azure 資源配置的公用 IP 位址的 IP 範圍清單刊登在[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

### <a name="dns-hostname-resolution"></a>DNS 主機名稱解析
當您建立的雲端服務或 IaaS VM 時，您需要提供 Azure 中的所有資源是唯一的雲端服務的 DNS 名稱。 這會建立*dnsname*的 Azure 受管理的 DNS 伺服器對應。 cloudapp.net 資源的公用 IP 位址。 例如，當您建立雲端服務的**contoso**雲端服務的 DNS 名稱時，完整網域名稱 (FQDN) **contoso.cloudapp.net**會將解析雲端服務的公用 IP 位址 (VIP)。 若要建立指向 Azure 中的公用 IP 位址的自訂網域的 CNAME 記錄，您可以使用這個 FQDN。

### <a name="cloud-services"></a>雲端服務
在雲端服務具有稱為虛擬的 IP 位址 (VIP) 的公用 IP 位址。 您可以建立關聯上 Vm 和雲端服務中的角色執行個體的內部連接埠 VIP 中不同的連接埠雲端服務中建立結束點。 

在雲端服務可以包含多個 IaaS Vm 或 PaaS 角色執行個體，透過相同的雲端服務 VIP 所有公開。 您也可以指定[多個 Vip 到雲端服務](../load-balancer/load-balancer-multivip.md)，讓多 VIP 案例，例如多租用戶環境使用 SSL 為基礎的網站。

您可以確保雲端服務的公用 IP 位址保持不變，即使停止所有角色例項，使用*靜態*公用 IP 位址，稱為[保留 IP](virtual-networks-reserved-public-ip.md)。 您可以建立特定位置的靜態 （保留） IP 資源，並將其指派給任何雲端服務，在該位置。 您無法指定的實際的 IP 位址保留 ip，它會從資料庫中建立的位置使用的 IP 位址的配置。 這個 IP 位址會釋放鍵，直到您明確地將其刪除。

靜態 （保留） 的公用 IP 位址常使用的是案例中雲端服務的位置︰

- 需要防火牆規則是由使用者的設定。
- 外部 DNS 名稱解析，而定，動態 IP 需要更新記錄。
- 使用外部 web 服務使用 IP 為基礎的安全性模型。
- 使用連結至 IP 位址的 SSL 憑證。

>[AZURE.NOTE] 當您建立傳統 VM 時，Azure 虛擬的 IP 位址 (VIP) 建立容器*雲端服務*。 完成建立是透過入口網站，預設 RDP 或 SSH*端點*設定入口網站，您可以連線到雲端服務 VIP vm。 這項雲端服務 VIP 可以保留，有效率地提供您連線至 VM 保留的 IP 位址。 您可以藉由設定更多的結束點開啟額外的連接埠。

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS Vm 和 PaaS 角色執行個體
您可以指定的公用 IP 位址直接 IaaS [VM](../virtual-machines/virtual-machines-linux-about.md)或 PaaS 雲端服務中的角色執行個體。 這被指執行個體層級的公用 IP 位址 ([ILPIP](virtual-networks-instance-level-public-ip.md))。 可以只動態這個公用 IP 位址。

>[AZURE.NOTE] 這是不同的雲端服務，也就是 IaaS Vm 或 PaaS 角色執行個體，因為雲端服務可以包含多個 IaaS Vm，或 PaaS 角色執行個體，所有透過相同的雲端服務 VIP 公開容器 VIP。

### <a name="vpn-gateways"></a>VPN 閘道器
連線至其他 Azure VNets 或內部網路的 Azure VNet 可[VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 閘道指派公用 IP 位址*以動態方式*，讓遠端網路通訊。

### <a name="application-gateways"></a>應用程式的閘道器
Azure[應用程式的閘道器](../application-gateway/application-gateway-introduction.md)可以用於 Layer7 負載平衡根據 HTTP 的網路流量路由。 公用 IP 位址*動態*，做為負載平衡 VIP 指派的應用程式的閘道器。

### <a name="at-a-glance"></a>概覽
下表顯示每個可能的配置方法 （動態/靜態） 與可以指定多個的公用 IP 位址的資源類型。

|資源|動態|靜態|多個 IP 位址|
|---|---|---|---|
|雲端服務|[是]|[是]|[是]|
|IaaS VM 或 PaaS 角色執行個體|[是]|無|無|
|VPN 閘道器|[是]|無|無|
|應用程式的閘道器|[是]|無|無|

## <a name="private-ip-addresses"></a>私人 IP 位址
私人 IP 位址允許 Azure 資源進行通訊的其他資源在雲端服務或[虛擬網路](virtual-networks-overview.md)(VNet)，或內部網路 （透過 VPN 閘道器或 ExpressRoute 電路），而不使用網際網路連線的 IP 位址。

Azure 傳統部署模型] 中，可以為下列 Azure 資源指定私用的 IP 位址︰

- IaaS Vm 和 PaaS 角色執行個體
- 內部負載平衡器
- 應用程式的閘道器

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS Vm 和 PaaS 角色執行個體
建立使用傳統的部署模型的虛擬機器 (Vm) 一律會在雲端服務，類似於 PaaS 角色執行個體。 私用的 IP 位址的行為因此類似這些資源。

請務必注意雲端服務，可以部署兩種方法︰

- 為*獨立*雲端服務，不在虛擬的網路。
- 虛擬網路的一部分。

#### <a name="allocation-method"></a>配置方法
若*獨立*雲端服務，資源私人 IP 位址配置*動態*取得從 Azure 資料中心私人 IP 位址範圍。 可以用於僅適用於使用相同的雲端服務中的其他 Vm 通訊。 停止和開始資源時，可以變更此 IP 位址。

若內虛擬網路部署雲端服務，資源取得私人配置 （如同在其網路設定中指定） 相關聯的子網路位址範圍的 IP 位址。 此私用的 IP 位址可用於 VNet 內的所有 Vm 之間的通訊。

此外，若內 VNet 雲端服務，私用的 IP 位址是配置*動態*（使用 DHCP） 預設。 停止和開始資源時，可以變更其項目。 若要確保的 IP 位址保持不變，您需要的配置方式設定為*靜態*，並提供正確的 IP 位址的相對應的地址範圍內。

私人的靜態 IP 位址常用的︰

 - 作為網域或 DNS 伺服器的 Vm。
 - 需要使用的 IP 位址的防火牆規則的 Vm。
 - Vm 執行服務會透過 IP 位址的其他應用程式。

#### <a name="internal-dns-hostname-resolution"></a>內部 DNS 主機名稱解析
所有 Azure Vm 和 PaaS 角色執行個體以都設定[Azure 受管理的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)根據預設，除非您明確設定自訂 DNS 伺服器。 這些 DNS 伺服器提供內部名稱解析 Vm 和位於相同的 VNet 或雲端服務中的角色執行個體。

當您建立 VM 時，對應至其私用的 IP 位址的主機名稱會新增到 Azure 受管理的 DNS 伺服器。 若多重 NIC VM，主機名稱] 已對應至主 NIC 的私人 IP 位址 不過，這項對應資訊會套用至相同的雲端服務或 VNet 內的資源。

若*獨立*雲端服務，您可以解決 hostname 相同雲端服務只內的所有 Vm/角色執行個體。 若內 VNet 雲端服務，您可以解決 hostname VNet 內的所有 Vm/角色執行個體。

### <a name="internal-load-balancers-ilb--application-gateways"></a>內部負載平衡器 (ILB) 與應用程式的閘道器
您可以對**前端**設定[Azure 內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)(ILB) 或[Azure 應用程式的閘道器](../application-gateway/application-gateway-introduction.md)的指定私用的 IP 位址。 這個私用的 IP 位址做為 [是內部端點，只有存取其虛擬網路 (VNet) 中的資源，遠端網路連線到 VNet。 您可以對前端設定來指定 [動態或靜態私人的 IP 位址。 您也可以指定多個要啟用多 vip 案例私用的 IP 位址。

### <a name="at-a-glance"></a>概覽
下表顯示每個可能的配置方法 （動態/靜態） 與可以指定多個私用的 IP 位址的資源類型。

|資源|動態|靜態|多個 IP 位址|
|---|---|---|---|
|VM （*獨立*雲端服務中）|[是]|[是]|[是]|
|PaaS 角色執行個體 （*獨立*雲端服務中）|[是]|無|[是]|
|VM 或 PaaS 角色執行個體 （在 VNet)|[是]|[是]|[是]|
|內部負載平衡器前端|[是]|[是]|[是]|
|應用程式閘道器前端|[是]|[是]|[是]|

## <a name="limits"></a>限制

下表顯示限制 ip 位址 Azure 中每個訂閱。 您可以[連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以增加進位到最大限制根據您的業務需求的預設限制。

||預設的限制|最大限制|
|---|---|---|
|公用 IP 位址 （動態）|5|連絡支援人員|
|保留的公用 IP 位址|20|連絡支援人員|
|公用 VIP 每部署 （雲端服務）|5|連絡支援人員|
|私人 VIP (ILB) 每部署 （雲端服務）|1|1|

請確定您閱讀完整的 Azure 中的 [[限制的網路](azure-subscription-service-limits.md#networking-limits)。

## <a name="pricing"></a>價格

在大部分情況下，程式為免費的公用 IP 位址。 有 nominal 電力使用其他及/或靜態的公用 IP 位址。 請確定您瞭解[價格的公用 Ip 結構](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="differences-between-resource-manager-and-classic-deployments"></a>資源管理員與傳統部署之間的差異
以下是 [IP 位址中的功能比較資源管理員] 及 [傳統部署模型。

||資源|傳統|資源管理員|
|---|---|---|---|
|**公用 IP 位址**|VM|稱為 ILPIP （僅限動態）|稱為 （動態或靜態） 的公用 IP|
|||指派給 IaaS VM 或 PaaS 角色執行個體|與相關聯的 VM NIC|
||網際網路具負載平衡器|稱為 VIP （動態） 或保留 IP （靜態）|稱為 （動態或靜態） 的公用 IP|
|||指派到雲端服務|與相關聯的負載平衡器前端設定|
||||
|**私人 IP 位址**|VM|稱為 DIP|稱為私用的 IP 位址|
|||指派給 IaaS VM 或 PaaS 角色執行個體|指派給 VM NIC|
||內部負載平衡器 (ILB)|指派給 ILB （動態或靜態）|指派給 ILB 前端設定 （動態或靜態）|

## <a name="next-steps"></a>後續步驟
- 使用 [傳統] 入口網站的[部署 VM 與私人的靜態 IP 位址](virtual-networks-static-private-ip-classic-pportal.md)。
