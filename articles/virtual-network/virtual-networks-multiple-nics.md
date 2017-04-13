<properties
   pageTitle="建立多個 Nic VM"
   description="瞭解如何建立及設定多個 nic vm"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>建立多個 Nic VM

您可以建立 Azure 虛擬機器 (Vm)，並將多個網路介面 (Nic) 附加至每個您 Vm。 多重 NIC 是許多網路虛擬應用裝置，例如應用程式後傳遞，但 WAN 最佳解決方案的需求。 多重 NIC 也會提供更多的網路流量管理功能，包括隔離的最上層之間的流量結束 NIC 並返回結束 NIC 或資料平面流量的分隔管理平面流量。

![多重 NIC 的 VM](./media/virtual-networks-multiple-nics/IC757773.png)

上述圖 VM 與三個 Nic，每個連接至不同的子網路。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- 「 預設 」 NIC 只支援網際網路的 VIP （傳統部署） 有一個 VIP 的預設 NIC ip
- 目前執行個體層級公用 IP (LPIP) 地址 （傳統部署） 不支援的多重 NIC Vm。
- 從 Nic VM 內的順序會在隨機，也可能會變更 Azure 基礎結構更新。 不過，IP 位址及相對應的乙太網路 MAC 地址會維持不變。 例如，假設**Eth1** IP 位址 10.1.0.100 及 MAC 位址 00-0D-3A-B0-39-0D;Azure 基礎結構後更新並重新開機，它可能會變更為**Eth2**，但 IP MAC 配對會維持不變。 重新啟動時啟動客戶，NIC 順序會維持不變。
- 地址的每個 VM 上每個 NIC 必須位於子網路、 單一 VM 在多個 Nic 可以指定在相同的子網路中的地址。
- 虛擬記憶體大小會決定您可以建立的 VM NIC 數目。 參考[Windows Server](../virtual-machines/virtual-machines-windows-sizes.md)及[Linux](../virtual-machines/virtual-machines-linux-sizes.md) VM 大小文件，以判斷多少 NIC 每個虛擬記憶體大小支援。 

## <a name="network-security-groups-nsgs"></a>網路安全性群組 (NSGs)
在資源管理員部署中，在 VM 任何 NIC 可能相關聯的網路安全性群組 (NSG)，包括任何 Nic VM 已啟用的多個 Nic 上。 如果 NIC 指派子網路哪裡 NSG 與相關聯的子網路中的地址，然後規則中的子網路 NSG 也適用於該 NIC 除了關聯 NSGs 子網路，您也可以與 NSG 關聯 NIC。

如果子網路相關聯的 NSG 和子網路中的 NIC 個別與相關 NSG，根據進或移出 NIC 傳遞的流量的方向的**流程順序**套用相關聯的 NSG 規則︰

- 其目的是在 NIC 問題的**內送流量**流向第一次子網路，觸發的子網路 NSG 規則]，再傳遞到 NIC，然後觸發 NIC NSG 規則。
- **在 [外寄流量**的來源是問題 NIC 順著第一次查看觸發 NIC NSG 規則，請先通過子網路，然後觸發的子網路 NSG 規則，在 NIC。

進一步瞭解[網路安全性群組](virtual-networks-nsg.md)及如何套用根據子網路 Vm，Nic 的關聯。

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>如何設定在傳統的部署中的多 NIC VM

下列程序將協助您建立多 NIC VM 包含 3 Nic: 預設 NIC 和兩個其他 Nic。 設定步驟會建立 VM 會根據 [服務設定檔片段下列設定︰

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


您嘗試執行範例中的 PowerShell 命令之前需要下列先決條件。

- Azure 的訂閱。
- 設定的虛擬網路。 如需 VNets 相關資訊，請參閱[虛擬網路概觀](virtual-networks-overview.md)。
- 最新版的 PowerShell 的 Azure 下載並安裝。 瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

若要使用多個 Nic 建立 VM，請遵循下列步驟︰

1. 從 Azure VM 圖像庫中選取的 VM 圖像。 請注意，圖像經常變更可依地區。 在下面的範例中指定的圖像可能會變更，或不可能會在您的地區，因此請務必指定您需要的圖像。

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. 建立 VM 設定。

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. 建立預設管理員登入。

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. 新增其他 Nic VM 設定。

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. 指定預設 NIC 子網路和 IP 位址

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. 建立 VM 虛擬網路。

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] 您在這裡指定 VNet 必須已存在 （如下所述的先決條件）。 下列範例會指定名為**MultiNIC VNet**的虛擬網路。

## <a name="limitations"></a>限制

使用多重 NIC 功能時，以下的限制是適用︰

- 必須在 Azure 虛擬網路 (VNets) 中建立多重 NIC Vm。 非 VNet Vm 無法使用多重 Nic 設定。
- 若要使用 [多重 NIC 或單一 NIC 的可用性組中的所有 Vm 都需要 無法混合多重 NIC Vm 和可用性組中的單一 NIC Vm。 雲端服務中，為 Vm 套用相同的規則。
- 與單一 NIC VM 無法使用多重 Nic （反之亦然） 來設定後部署，但不刪除和重新建立。


## <a name="secondary-nics-access-to-other-subnets"></a>第二個 Nic 存取其他子網路

依預設將不會次要 Nic 設定預設的閘道器，因為其在次要 Nic 流量會限制為子網路相同。 如果使用者想要啟用外部子網路交談的次要 Nic，必須設定閘道器，如下所述路由表格中新增項目。

>[AZURE.NOTE] 2015 年 7 月可能會有預設的閘道設定的所有 Nic 之前，先建立 Vm。 這些 Vm 生效，不會移除第二個 Nic 預設閘道。 使用弱式主機路由模型 Linux，例如的作業系統如果輸入與輸出流量使用不同的 Nic，可以中斷網際網路連線。

### <a name="configure-windows-vms"></a>設定 Windows Vm

假設您有兩個 Nic 與 Windows VM，如下所示︰

- 主要 NIC IP 位址︰ 192.168.1.4
- 次要 NIC IP 位址︰ 192.168.2.5

此 VM IPv4 路由表格如下所示︰

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

請注意預設路由 (0.0.0.0) 只有主要 NIC 使用 您將無法存取子網路外的資源的次要 NIC，如下所示︰

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

若要新增的預設路由次要在 NIC 上，遵循下列步驟︰

1. 在命令提示字元中，執行下列命令，以識別次要 NIC 編號︰

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. 請注意在資料表中，以 27 （在此範例中） 的索引的第二個項目。
3. 在命令提示字元中，執行**路由新增**的命令，如下所示。 在此範例中，您要指定為預設閘道 192.168.2.1 的次要 NIC:

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. 若要測試連線，回到 [命令提示字元中，並嘗試偵測 （ping） 次要 NIC 從不同的子網路為顯示 int 是不下面範例︰

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. 您也可以查看檢查新加入的路徑，您傳送的資料表，如下所示︰

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>設定 Linux Vm

Linux Vm 的預設行為使用弱式主機路由]，因為我們建議次要 Nic 限制只在相同的子網路流量。 但是如果特定案例要求外部子網路的連線，使用者應該啟用原則以確保輸入與輸出流量使用相同的 NIC 路由

## <a name="next-steps"></a>後續步驟

- 部署[MultiNIC Vm 在資源管理員部署 2 層情境中](virtual-network-deploy-multinic-arm-template.md)。
- 部署[MultiNIC Vm 在傳統部署 2 層情境中](virtual-network-deploy-multinic-classic-ps.md)。
