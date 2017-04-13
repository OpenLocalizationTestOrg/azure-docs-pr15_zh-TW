<properties
   pageTitle="公用與私人的 IP 位址中 Azure 資源管理員 |Microsoft Azure"
   description="進一步瞭解公開及私密金鑰的 IP 位址 Azure 資源管理員中的商務連絡人"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Azure 中的 IP 位址
您可以與其他 Azure 資源、 內部網路和網際網路通訊 Azure 資源指定的 IP 位址。 有兩種類型的 IP 位址，您可以使用 Azure 中︰

- **公用 IP 位址**︰ 使用網際網路，包括 Azure-在公開服務的通訊
- **私人 IP 位址**︰ 用於溝通 Azure 虛擬網路 (VNet)，和您的內部部署網路，當您使用的 VPN 閘道器或 ExpressRoute 電路將您的網路延伸到 Azure。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](virtual-network-ip-addresses-overview-classic.md)。

如果您熟悉的傳統部署模型，請核取[IP 位址傳統之間的差異和資源管理員](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments)。

## <a name="public-ip-addresses"></a>公用 IP 位址
公用 IP 位址允許 Azure 資源進行通訊的網際網路和 Azure-在公開服務，例如[Azure Redis 快取](https://azure.microsoft.com/services/cache/)、 [Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)、 [SQL 資料庫](../sql-database/sql-database-technical-overview.md)和[Azure 儲存空間](../storage/storage-introduction.md)。

Azure 資源管理員] 中，[公用 IP](resource-groups-networking.md#public-ip-address)位址是資源有自己的內容。 您可以建立公用的 IP 位址資源關聯的下列資源︰

- 虛擬機器 (VM)
- 具網際網路負載平衡器
- VPN 閘道器
- 應用程式的閘道器

### <a name="allocation-method"></a>配置方法
有兩種方法的*公用*IP 資源-*動態*或*靜態*配置 IP 位址。 在 IP 位址的*動態*，預設配置方法，是**不**一次建立的配置。 不過，當您啟動 （或建立） 相關聯的資源 （例如 VM 或負載平衡器），會配置的公用 IP 位址。 當您停止 （或刪除） 資源發行的 IP 位址。 這會使變更當您停止和開始資源的 IP 位址。

若要確保相關聯的資源的 IP 位址保持不變，您可以將配置方法明確為*靜態*。 在此情況下會立即指派 IP 位址。 在刪除資源，或變更其配置方法*動態*時才放開。

>[AZURE.NOTE] 即使您將配置方法為*靜態*時，您無法指定給*公用 IP 資源*分派的實際 IP 位址。 不過，它會配置可用的 IP 位址 Azure 位置中建立資源資料庫中。

靜態的公用 IP 位址常使用的是在以下情況︰

- 使用者需要更新 Azure 資源通訊的防火牆規則。
- DNS 名稱解析，在 [IP 位址變更位置需要更新記錄。
- 與其他應用程式或服務使用的 IP 位址為基礎的安全性模型的通訊 Azure 資源。
- 您使用連結至 IP 位址的 SSL 憑證。

>[AZURE.NOTE] 從 Azure 資源配置的公用 IP 位址 （動態/靜態） 的 IP 範圍清單刊登在[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

### <a name="dns-hostname-resolution"></a>DNS 主機名稱解析
您可以指定所建立的*domainnamelabel*對應的公用 IP 資源 DNS 網域名稱標籤。*位置*。 cloudapp.azure.com Azure 受管理的 DNS 伺服器中的公用 IP 位址。 例如，如果您的公用 IP 資源以**contoso**作為建立**西美國** *domainnamelabel* Azure 的*位置*，完整網域名稱 (FQDN) **contoso.westus.cloudapp.azure.com**會解析為資源的公用 IP 位址。 若要建立指向 Azure 中的公用 IP 位址的自訂網域的 CNAME 記錄，您可以使用這個 FQDN。

>[AZURE.IMPORTANT] 建立的每個網域名稱標籤中必須是唯一的 Azure 的位置。  

### <a name="virtual-machines"></a>虛擬機器
指定給其**網路介面**，您可以使用[Windows](../virtual-machines/virtual-machines-windows-about.md)或[Linux](../virtual-machines/virtual-machines-linux-about.md) VM 關聯的公用 IP 位址。 若是 VM 多網路介面，您可以將它指派給*主要*網路介面只。 您可以與 VM 指定動態或公用的靜態 IP 位址。

### <a name="internet-facing-load-balancers"></a>具網際網路負載平衡器
您可以指定給負載平衡器**frontend**設定[Azure 負載平衡器](../load-balancer/load-balancer-overview.md)，關聯的公用 IP 位址。 這個公用 IP 位址可做為負載平衡虛擬 IP 位址 (VIP)。 您可以將動態或公用的靜態 IP 位址指派給前端負載平衡器。 您也可以指派負載平衡器前端，這可讓[多重 VIP](../load-balancer/load-balancer-multivip.md)案例，例如使用 SSL 為基礎的網站在多租用戶環境的多個的公用 IP 位址。

### <a name="vpn-gateways"></a>VPN 閘道器
[Azure VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)用來連線到其他 Azure VNets 或內部網路的 Azure 虛擬網路 (VNet)。 您需要為其**IP 設定**，讓它與遠端網路通訊的公用 IP 位址。 目前，您僅可以 VPN 閘道指派*動態*的公用 IP 位址。

### <a name="application-gateways"></a>應用程式的閘道器
您可以指定給閘道器的**frontend**設定[應用程式閘道](../application-gateway/application-gateway-introduction.md)Azure，關聯的公用 IP 位址。 這個公用 IP 位址可做為負載平衡 VIP。 目前，您僅可以應用程式的閘道器 frontend 設定中指定*動態*的公用 IP 位址。

### <a name="at-a-glance"></a>在然
下表顯示用的公用 IP 位址可相關聯的最上層的資源，並可能的配置 （動態或靜態） 的方法可以使用的特定屬性。

|最上層的資源|IP 位址關聯|動態|靜態|
|---|---|---|---|
|虛擬機器|網路介面|[是]|[是]|
|負載平衡器|前端設定|[是]|[是]|
|VPN 閘道器|閘道器 IP 設定|[是]|無|
|應用程式的閘道器|前端設定|[是]|無|

## <a name="private-ip-addresses"></a>私人 IP 位址
私人 IP 位址允許 Azure[虛擬網路](virtual-networks-overview.md)或透過 VPN 閘道器或 ExpressRoute 電路內部部署網路中的其他資源與通訊，而不使用網際網路連線的 IP 位址的資源。

Azure 資源管理員部署模型中私用的 IP 位址是下列類型的 Azure 資源相關的︰

- Vm
- 內部負載平衡器 (ILBs)
- 應用程式的閘道器

### <a name="allocation-method"></a>配置方法
從子網路資源所連接的地址範圍配置私用的 IP 位址。 子網路本身的地址範圍是範圍的 VNet 的地址。

私用的 IP 位址已配置中的兩種方法︰*動態*或*靜態*。 預設配置方法是*動態*，會自動從資源的子網路 （使用 DHCP） 配置的 IP 位址。 當您停止並開始資源時，可以變更此 IP 位址。

您可以將配置方法*靜態*以確保的 IP 位址保持不變。 在此情況下，您也需要提供正確的 IP 位址的資源的子網路。

私人的靜態 IP 位址常用的︰

- 作為網域或 DNS 伺服器的 Vm。
- 需要使用的 IP 位址的防火牆規則的資源。
- 存取的其他應用程式/資源透過 IP 位址的資源。

### <a name="virtual-machines"></a>虛擬機器
私用的 IP 位址會指派到[Windows](../virtual-machines/virtual-machines-windows-about.md)或[Linux](../virtual-machines/virtual-machines-linux-about.md) VM 的**網路介面**。 若多網路介面 VM，每個介面取得指派的私人 IP 位址。 您可以指定配置方法為動態或靜態網路介面。

#### <a name="internal-dns-hostname-resolution-for-vms"></a>（適用於 Vm) 的內部 DNS 主機名稱解析
所有 Azure Vm 都設定[Azure 受管理的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)根據預設，除非您明確設定自訂 DNS 伺服器。 這些 DNS 伺服器的位於相同的 VNet 的 Vm 提供內部名稱解析。

當您建立 VM 時，對應至其私用的 IP 位址的主機名稱會新增到 Azure 受管理的 DNS 伺服器。 若多網路介面 VM，主機名稱] 已對應至私人 IP 位址的主要網路介面。

Azure 受管理的 DNS 伺服器以設定 Vm 無法解決的其 VNet 內的所有 Vm hostname 與其私用的 IP 位址。

### <a name="internal-load-balancers-ilb--application-gateways"></a>內部負載平衡器 (ILB) 與應用程式的閘道器
您可以對**前端**設定[Azure 內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)(ILB) 或[Azure 應用程式的閘道器](../application-gateway/application-gateway-introduction.md)的指定私用的 IP 位址。 這個私用的 IP 位址做為 [是內部端點，只有存取其虛擬網路 (VNet) 中的資源，遠端網路連線到 VNet。 您可以對前端設定來指定 [動態或靜態私人的 IP 位址。

### <a name="at-a-glance"></a>在然
下表顯示透過私用的 IP 位址可最上層的資源，並可能的配置 （動態或靜態） 的方法可供相關聯的特定屬性。

|最上層的資源|IP 位址關聯|動態|靜態|
|---|---|---|---|
|虛擬機器|網路介面|[是]|[是]|
|負載平衡器|前端設定|[是]|[是]|
|應用程式的閘道器|前端設定|[是]|[是]|

## <a name="limits"></a>限制

在 IP 位址所設定的限制會表示完整 Azure 中的 [[限制的網路](azure-subscription-service-limits.md#networking-limits)中。 這些限制為每個地區和每個訂閱。 您可以[連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以增加進位到最大限制根據您的業務需求的預設限制。

## <a name="pricing"></a>價格

公用 IP 位址可能會有名目的費用。 若要進一步瞭解 Azure 中的 IP 位址價格，檢閱 [ [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)的頁面。

## <a name="next-steps"></a>後續步驟
- [部署 VM 固定的公用 ip 使用 Azure 入口網站](virtual-network-deploy-static-pip-arm-portal.md)
- [部署 VM 固定的公用 ip 使用範本](virtual-network-deploy-static-pip-arm-template.md)
- [部署 VM 具有靜態私人使用 Azure 入口網站的 IP 位址](virtual-networks-static-private-ip-arm-pportal.md)
