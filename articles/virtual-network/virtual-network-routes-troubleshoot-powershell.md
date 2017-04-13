<properties 
   pageTitle="疑難排解路由-PowerShell |Microsoft Azure"
   description="瞭解如何使用 PowerShell 的 Azure Azure 資源管理員部署模型中的疑難排解。"
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
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
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-azure-powershell"></a>疑難排解路由使用 PowerShell 的 Azure

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

如果您遇到網路連線發生問題或您 Azure 虛擬機器 (VM)，而可能會影響您 VM 流量。 本文提供概觀以協助進一步疑難排解路由診斷功能。

將資料表與子網路相關聯，有效子網路中所有的網路介面 (NIC) 上。 下列類型的路由可套用至每個網路介面︰

- **系統路由︰**根據預設，在 Azure 虛擬網路 (VNet) 中建立的每一個子網路會有允許本機 VNet 流量與內部部署流量 VPN 閘道器，透過網際網路流量的系統路由表格。 系統路由也有 peered VNets。
- **BGP 路由︰**傳播到透過 ExpressRoute 或網站-VPN 連線網路介面。 進一步瞭解 BGP 路由閱讀[與 VPN 閘道 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)和[ExpressRoute 概觀](../expressroute/expressroute-introduction.md)文章。
- **使用者定義的路徑 (UDR):**如果您使用或網路虛擬應用裝置強制通道傳輸至網站 VPN 透過內部部署網路，您可能需要使用者定義的路由 (UDRs) 子網路路由表格相關聯。 如果您不熟悉 UDRs，閱讀[使用者定義的路由](virtual-networks-udr-overview.md#user-defined-routes)文件。

使用可套用至 [網路介面的各種路由，很難以判斷哪些彙總路由有效。 若要協助疑難排解 VM 網路連線，您可以檢視所有有效的路由網路介面 Azure 資源管理員部署模型中。

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>使用有效路由疑難排解 VM 流量

本文會使用下列案例作為範例說明如何疑難排解網路介面的有效路由︰

VM (*VM1*) 連線到 VNet (*VNet1*、 首碼︰ 10.9.0.0/16) 無法連線至 VM(VM3) 中新 peered VNet (*VNet3*，前置詞 10.10.0.0/16)。 有任何 UDRs 或 BGP 路由套用至連線的 vm VM1 NIC1 網路介面，只系統路由就會套用。

本文說明如何判斷 Azure 資源管理部署模型中使用有效路由功能連線失敗的原因。
範例使用只系統路由時, 相同的步驟可用來決定傳入和傳出連線失敗，透過任何路由類型。

>[AZURE.NOTE] 如果您 VM 有一個以上的 NIC 附加，核取每個診斷網路連線問題與 VM Nic 有效的路由。

### <a name="view-effective-routes-for-a-virtual-machine"></a>檢視有效路由虛擬機器

若要查看套用至 VM 的彙總路由，請完成下列步驟︰

### <a name="view-effective-routes-for-a-network-interface"></a>檢視有效路由網路介面

若要查看套用至 [網路介面的彙總路由，請完成下列步驟︰

1.  啟動 Azure PowerShell 工作階段並登入 Azure。 如果您不熟悉 PowerShell 的 Azure，瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文章。

2.  下列命令會傳回套用至 [網路] 介面*VM1 NIC1* *RG1*上的 [資源] 群組中的所有路由。

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] 如果您不知道網路介面的名稱，輸入下列命令以擷取資源 group.* 中的所有網路介面的名稱

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    下列輸出看起來就像輸出效果套用至子網路 NIC 已連線至每個路由︰

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    請注意下列輸出中︰
    - **名稱**︰ 有效路由名稱可能會是空的除非明確指定的使用者定義的路徑。 
    - **狀態**︰ 指示有效路由的狀態。 可能的值為 「 使用中 」 或 「 正確 」
    - **AddressPrefixes**︰ 指定有效路線的地址字首 CIDR 法。 
    - **nextHopType**︰ 指出躍指定路由。 可能的值是*VirtualAppliance*、*網際網路*、 *VNetLocal*、 *VNetPeering*，或*Null*。 **NextHopType** UDR 中的*Null*值可能無效的路由。 例如，如果**nextHopType** *VirtualAppliance*和虛擬網路有應用裝置 VM 不在執行佈建狀態。 如果**nextHopType** *VPNGateway* ，有無閘道佈建/執行中指定 VNet，路徑可能會變成無效。
    - **NextHopIpAddress**︰ 指定一個有效路由的躍點的 IP 位址。
    
    下列命令會傳回路由中的更容易檢視的資料表︰

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    下列輸出會收到案例先前所述的輸出部分︰

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. 沒有路由列於 [ *WestUS VNet3* VNet (前置字元 10.10.0.0/16)**從*WestUS VNet1* (前置字元 10.9.0.0/16) 輸出中的上一個步驟。 下圖所示，使用*WestUS VNet3* VNet VNet 對等連結是在*中斷連線*狀態。
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    雙向連結的對等已損毀，其中說明為什麼 VM1 無法連線至 VM3 *WestUS VNet3* VNet 中。 設定一次*WestUS VNet1*和*WestUS VNet3* VNets 雙向 VNet 「 對等的連結。 傳回正確地建立 VNet 對等連結後的輸出所示︰

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    一旦您判斷問題，您可以新增、 移除或變更路由和路由表格。 輸入下列命令，請參閱執行此作業用的指令清單︰

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>考量

傳回檢閱的路由清單時，請記住的一些事項︰

- 路由為基礎的最長加上字首須相符 (LPM) 之間 UDRs，BGP 與系統路由。 如果有多個路由相同 LPM 相符項目，然後傳送已根據原始以下列順序︰
    - 使用者定義的傳送
    - BGP 路由
    - 系統 （預設） 傳送

    有效路線，您可以只看到根據所有了路由 LPM 相符的有效路由。 顯示路由針對指定的 NIC 會如何評估，這可讓您輕鬆地進行疑難排解可能會影響從您 VM 連線的特定路由。

- 如果您有 UDRs，流量傳送給使用*VirtualAppliance*為**nextHopType**網路虛擬應用 (NVA)，請確定 IP 轉接已啟用接收流量 NVA 或封包會遭到捨棄。 
- 如果已啟用強制通道，將會將所有輸出網際網路流量路由至內部部署。 RDP/SSH 從網際網路您 vm 可能不適用於這項設定，取決於內部部署如何處理此流量。 
  您可以啟用強制通道︰
    - 如果使用網站-VPN 與 nextHopType 為 VPN 閘道器設定使用者定義的路徑 (UDR)
    - 如果預設路由透過 BGP 通知
- VNet 等流量到正常運作， **nextHopType** *VNetPeering*系統路由必須存在 peered 的 VNet 的前置字元範圍。 如果這類路由不存在，且 VNet 對等連結看來︰
    - 請稍候，並再次如果是新建立對等的連結。 有時候花更長的時間，才會傳播到子網路中的所有網路介面路由。
    - 網路安全性群組 (NSG) 規則可能會影響流量。 如需詳細資訊，請參閱[疑難排解網路安全性群組](virtual-network-nsg-troubleshoot-powershell.md)文件。
