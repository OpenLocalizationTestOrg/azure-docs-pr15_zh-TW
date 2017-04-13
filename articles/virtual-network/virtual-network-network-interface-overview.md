<properties 
   pageTitle="網路介面 |Microsoft Azure"
   description="深入了解 Azure 網路介面 Azure 資源管理員] 中。"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>網路介面

網路介面 (NIC) 是互相虛擬機器 (VM) 和軟體網路之間的連線。 本文說明網路介面，並將其 Azure 資源管理員部署模型中的使用方式。

Microsoft 建議部署新的資源使用資源管理員部署模型，但您也可以部署 Vm[傳統](virtual-network-ip-addresses-overview-classic.md)的部署模型中的網路連線。 如果您已熟悉了傳統的模型，有 VM 網路資源管理員部署模型中的重要差異。 進一步瞭解差異閱讀的[虛擬機器網路-傳統](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)的文件。

在 [Azure 網路介面︰

1. 您可以建立，刪除資源，自己可設定的設定。
2. 必須連接到一個 Azure 虛擬網路 (VNet) 中的一個子網路建立時。 如果您不熟悉 VNets，請閱讀[虛擬網路概觀](virtual-networks-overview.md)文章瞭解更多相關資訊。 在 NIC 必須連接到同一個 Azure[位置](https://azure.microsoft.com/regions)和[訂閱](../azure-glossary-cloud-terminology.md#subscription)為在 NIC 中存在 VNet 建立 NIC 之後，您可以變更已連接到，子網路，但是您無法變更已連接到 VNet。
3. 已指派給它 NIC 建立之後即無法變更的名稱。 名稱必須是唯一 Azure[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)] 內，但沒有訂閱、 Azure 位置建立或已連接到 VNet 中是唯一的。 多個 Nic 通常會建立 Azure 訂閱中。 建議您制訂可讓您管理多個 Nic 您更輕鬆地使用預設名稱的命名慣例。 請參閱[建議 Azure 資源的命名慣例](../guidance/guidance-naming-conventions.md)的建議。
4. 可能會附加 VM，但只能在 NIC 的相同位置中有單一 vm 連接
5. 有 MAC 地址]，只要將其附加至 VM 與的 NIC 保存。 重新啟動 VM 是否保存 MAC 地址 (從作業系統內) 或已停止 （解除配置） 和開始使用 Azure 入口網站、 PowerShell 的 Azure 或 Azure 命令列介面。 如果已中斷 VM 並連接至不同的 VM，NIC 會收到不同的 MAC 位址。 如果在 NIC 會刪除，MAC 地址已指派給其他 Nic 中。
6. 必須有一個主要**私人** *IPv4*靜態和動態 IP 位址指派給它。
8. 可能會有一個公用 IP 位址資源相關的它。
9. 支援加速網路與執行 Microsoft Windows 伺服器作業系統的特定版本的特定 VM 大小的單一根 I/O 模擬 (SR IOV)。 若要進一步瞭解此預覽功能，請閱讀[加速網路虛擬機器的](virtual-network-accelerated-networking-powershell.md)文章。
10. 可接收流量不私人 IP 轉接 NIC 如果指派的 IP 位址 如果 VM 執行的防火牆軟體，例如，其路由封包不直接它自己的 IP 位址。 VM 仍必須執行軟體能夠路由] 或 [轉接流量，但若要這麼做，IP 轉接必須啟用 NIC
11. 經常建立附加至 VM 或已連接至相同 VNet 相同的 [資源] 群組中雖然它不需要。

## <a name="vms-with-multiple-network-interfaces"></a>具有多個網路介面 Vm

多個 Nic 可附加 vm，但是這麼做，請注意下列任一動作︰  

- 虛擬記憶體大小必須支援多個 Nic。 若要瞭解更多有關的 VM 大小支援多個 Nic，閱讀的[Windows Server VM 大小](../virtual-machines/virtual-machines-windows-sizes.md)或[Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md)的文章。   
- 必須至少為兩個 Nic 以建立 VM。 如果 VM 建立與只有一個 NIC，即使虛擬記憶體大小支援超過一個，您無法將附加其他 Nic vm 建立後。 只要至少兩個 Nic 建立 VM，您可以附加其他 Nic VM 建立好的只要虛擬記憶體大小支援兩個以上的 Nic。  
- 如果 VM 具有附加的至少三個 Nic，您可以從 VM 中斷次要 Nic （主要 NIC 無法中斷連結）。 如果 VM 具有兩個或更少 Nic 附加，您無法將 Nic 中斷連結。  
- 從 Nic VM 內的順序會在隨機，也可能會變更 Azure 基礎結構更新。 不過，IP 位址和對應的乙太網路 MAC 位址，就會維持不變。 例如，假設作業系統識別為 Eth1 Azure NIC1。 Eth1 具備 IP 位址 10.1.0.100 及 00-0D-3A-B0-39-0D MAC 地址。 Azure 基礎結構更新並重新啟動之後，作業系統現在可能會識別為 Eth2，Azure NIC1，但 IP 和 MAC 的地址也是一樣的作業系統識別為 Eth1 Azure NIC1 時。 重新啟動時啟動客戶，NIC 順序仍會維持作業系統內相同。  
- 如果 VM[可用性設定](../azure-glossary-cloud-terminology.md#availability-set)成員，在可用性組中的所有 Vm 都必須單一 NIC 或多個 Nic。 如果 Vm 有多個 Nic 的數字每個有不必要的相同，只要每個 VM 有兩個以上的 Nic。

## <a name="next-steps"></a>後續步驟

- 瞭解如何建立單一 NIC VM 閱讀[建立 VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)文章。
- 瞭解如何建立多個 Nic VM 閱讀[部署與多個 NIC VM](virtual-network-deploy-multinic-arm-ps.md)文章。
- 瞭解如何建立多個 IP 組態 NIC 閱讀的[多個 IP 位址 Azure 虛擬機器的](virtual-network-multiple-ip-addresses-powershell.md)文件。
