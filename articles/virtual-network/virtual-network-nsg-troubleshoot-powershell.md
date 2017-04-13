<properties 
   pageTitle="疑難排解網路安全性群組-PowerShell |Microsoft Azure"
   description="瞭解如何使用 PowerShell 的 Azure Azure 資源管理員部署模型中的疑難排解網路安全性群組。"
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

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>疑難排解使用 PowerShell 的 Azure 網路安全性群組

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

如果您設定您的虛擬機器 (VM) 網路安全性群組 (NSGs)，會發生 VM 連線發生問題，本文會提供概觀以協助進一步疑難排解 NSGs 的診斷功能。

NSGs 可讓您控制類型的流量與登出您的虛擬機器 (Vm) 的流量。 NSGs 可以套用至子網路中 Azure 虛擬網路 (VNet)、 網路介面 (NIC)，或兩者。 有效的規則套用至 NIC 會彙總的規則套用至 NIC NSGs 存在於和已連接到子網路。 這些 NSGs 過規則有時可以互相衝突，並會影響 VM 的網路連線。  

當您 VM Nic 上套用，您可以從您的 NSGs 檢視所有有效的安全性規則。 本文說明如何疑難排解 VM Azure 資源管理員部署模型中使用這些規則的連線發生問題。 如果您不熟悉 VNet 和 NSG 的概念，閱讀文章[虛擬網路](virtual-networks-overview.md)和[網路安全性群組](virtual-networks-nsg.md)概觀。

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>若要疑難排解 VM 流量使用有效的安全性規則

遵循此案例是常見的連線問題的範例︰

名為*VM1* VM 是名為*Subnet1*中名為*WestUS VNet1*VNet 子網路的一部分。 嘗試透過 TCP 連接埠 3389 使用 RDP vm 連線失敗。 NSGs 會套用在 NIC *VM1 NIC1*和子網路*Subnet1*。 TCP 連接埠 3389 流量導向允許 NSG 相關聯的網路介面*VM1 NIC1*，不過 TCP 偵測 （ping） 至 VM1 的連接埠 3389 失敗。

雖然這個範例使用 TCP 連接埠 3389，下列步驟可用來決定傳入和傳出連線失敗，透過任何連接埠。

## <a name="detailed-troubleshooting-steps"></a>取得詳細的疑難排解步驟
完成下列步驟，以疑難排解 NSGs vm:

1. 啟動 Azure PowerShell 工作階段並登入 Azure。 如果您不熟悉如何使用 PowerShell 的 Azure，瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文章。

2. 輸入下列命令以傳回所有 NSG 規則套用至命名*VM1 NIC1* *RG1*上的 [資源] 群組中的 NIC:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] 如果您不知道 NIC 的名稱，輸入下列命令以擷取的資源群組中的所有 Nic 名稱︰ 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    下列文字是有效的規則輸出傳回*VM1 NIC1* NIC 的範例︰

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    請注意，輸出中的下列資訊︰

    - 有兩個**NetworkSecurityGroup**區段︰ 其中一個子網路 (*Subnet1*) 相關聯，而是 NIC (*VM1 NIC1*) 相關聯。 在此範例中，每個已套用 NSG。
    - **關聯**會顯示資源 （子網路或 NIC） 指定的 NSG 相關聯。 如果 NSG 資源是移/分離立即執行此命令之前，您可能需要等候幾秒鐘的變更，以反映在命令輸出。 
    - 規則名稱的前面會有*defaultSecurityRules*︰ 建立時 NSG、 內建立多個預設安全性規則。 無法移除預設規則，但可以將它們覆寫較高的優先順序規則。
     請閱讀[NSG 概觀](virtual-networks-nsg.md#default-rules)文章，若要進一步瞭解 NSG 預設的安全性規則。
    - **ExpandedAddressPrefix**展開 NSG 預設標籤的地址首碼。 標記代表多個地址前置詞。 疑難排解從特定的地址前置詞 VM 連線時，便十分實用擴充的標籤。 例如，與 VNET 對等，VIRTUAL_NETWORK 標籤展開以顯示 peered 的 VNet 前置詞先前的列印輸出。

        >[AZURE.NOTE] ] 命令只會顯示有效規則如果 NSG 子網路、 NIC，或兩者與相關聯。 VM 可能需要使用不同的 NSGs 套用多個 Nic。 疑難排解時，請針對每個 NIC 執行命令
        
3. 若要簡化在較大的數目，NSG 規則的篩選，輸入下列命令以進一步疑難排解︰ 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    篩選 RDP 流量 （TCP 連接埠 3389），會套用到 [格線] 檢視中，如下圖所示︰

    ![規則清單](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. 您可以看到 [格線] 檢視中，有兩允許和拒絕 RDP 規則。 步驟 2 的輸出會顯示*DenyRDP*規則套用至子網路 NSG 中。 輸入規則套用至子網路 NSGs 會先處理。 如果找到相符項目，就不會處理套用至 [網路介面 NSG。 在此情況下，從子網路*DenyRDP*規則會封鎖 RDP vm (**VM1**)。

    >[AZURE.NOTE] VM 可能會有多個 Nic 附加。 每個可能以不同的子網路連線。 由於對 NIC 執行上述步驟中的命令，請務必以確保您指定您無法連線失敗 NIC。 如果您不確定，則您永遠可以針對每個 NIC 附加至 VM 執行命令。

5. 若要將 VM1 RDP，變更*拒絕 RDP (3389)*規則以*允許 RDP(3389)* **Subnet1 NSG** NSG 中。 確認 [TCP 連接埠 3389 是開啟的 vm RDP 連線，或使用 PsPing 工具來開啟]。 您可以進一步瞭解 PsPing 閱讀的[PsPing 下載頁面](https://technet.microsoft.com/sysinternals/psping.aspx)

    您可以從或移除規則 NSG 下列命令輸出中使用的資訊︰

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>考量

疑難排解連線問題時，請考慮下列幾點︰

- 預設 NSG 規則會封鎖從網際網路輸入的存取權，並只允許 VNet 連入流量。 若要從網際網路]，允許輸入的存取，視需要應該明確新增規則。
- 如果沒有 NSG 安全性規則造成 VM 的網路連線失敗，問題可能會到期日︰
    - 執行 VM 的作業系統內的防火牆軟體
    - 設定虛擬就或內部部署的傳輸路由。 網際網路流量可以重新導向至內部部署透過強制通道。 從網際網路 RDP/SSH 連線至您 VM 可能不適用於這項設定，取決於內部部署網路硬體如何處理此流量。 請參閱[疑難排解路由](virtual-network-routes-troubleshoot-powershell.md)，瞭解如何診斷傳送的問題，可能會索性流量傳送與 VM 登出。 
- 如果您有 peered VNets，根據預設，VIRTUAL_NETWORK 標記會自動展開至包含 peered VNets 前置字元]。 您可以檢視這些前置詞**ExpandedAddressPrefix**清單中，與 VNet 等連線相關的任何問題進行疑難排解。 
- 有效的安全性規則只會顯示是否有相關聯的 VM NIC 和或子網路 NSG。 
- 如果不有任何 NIC 相關聯的 NSGs 或子網路，而且您已指派給您 VM 的公用 IP 位址，會開啟傳入和傳出存取所有的連接埠。 如果 VM 的公用 IP 位址，強烈建議將 NSGs 套用至 NIC 或子網路。  
