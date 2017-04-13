<properties 
   pageTitle="加速網路虛擬機器-PowerShell |Microsoft Azure"
   description="瞭解如何使用 PowerShell 的 Azure 虛擬機器中設定 [加速網路。"
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

# <a name="accelerated-networking-for-a-virtual-machine"></a>虛擬機器加速的網路

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

加速的網路啟用單一根 I/O 虛擬化 (SR IOV) 虛擬機器 (VM)，大幅改善網路效能。 此高效能路徑會略過主機減少延遲、 抖動，以及用於最重要的網路負載支援 VM 類型的 CPU 使用率資料路徑。 本文說明如何使用 PowerShell 的 Azure 設定 Azure 資源管理員部署模型中的 [加速網路。 您也可以建立 VM 具有加速網路使用 Azure 入口網站。 若要瞭解作法，按一下 [這份文件頂端的 [Azure 入口網站] 方塊。

下圖顯示兩個虛擬機器 (VM) 與不包含加速網路之間的通訊︰

![比較](./media/virtual-network-accelerated-networking-powershell/image1.png)

沒有加速網路主機和虛擬切換，必須通過與 VM 登出的所有網路流量。 虛擬切換提供所有的原則強制，例如網路安全性群組、 存取控制清單、 隔離與其他網路虛擬化服務網路流量。 若要深入瞭解，請閱讀[HYPER-V 網路虛擬化和虛擬切換](https://technet.microsoft.com/library/jj945275.aspx)文章。

含加速網路網路流量到達網路介面卡 (NIC)，然後再轉寄給 VM。 虛擬切換適用於不加速網路的所有網路原則卸載，並在 [硬體套用。 套用原則的硬體可直接至維持套用主應用程式中的所有原則，略過主機和虛擬切換，VM 轉寄網路流量 NIC。

在已啟用 VM 只適用於加速網路的優點。 為了獲得最佳的結果，很適合用來啟用此功能在兩個以上的 Vm 連線到相同的 VNet。  當通訊過 VNets 或內部部署連線，此功能會有整體的延遲的影響最小。

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>優點

- **較低的延遲較高每一封包秒 (.pps):**移除資料路徑的虛擬切換原則處理主機中移除封包花費的時間，並增加可以處理 VM 內的封包數目。
- **精簡抖動︰**虛擬切換處理取決於所要套用原則的數量和執行作業的 cpu 的工作量。 卸載原則強制執行的硬體會移除該變化提供封包直接 VM，移除主應用程式 VM 通訊所有軟體中斷和內容切換。
- **縮小內容的 CPU 使用率︰**略過主機虛擬切換引導較少的 CPU 使用率處理網路流量。

## <a name="limitations"></a>限制

使用此功能時，有下列限制︰
 
- **網路介面建立︰**僅限啟用新的網路介面加速的網路。  它不能啟用現有的網路介面上。
- **VM 建立︰**網路介面加速的網路啟用僅可附加 VM VM 建立時。 網路介面無法附加至現有的 VM。
- **區域︰**在 [美國中部西部和西部歐洲 Azure 區域只提供。 將會在未來展開的地區設定。
- **支援的作業系統︰**Microsoft Windows Server 2012 R2 和 Windows Server 2016 Technical Preview 5。 將推出加入 Linux 與 Windows Server 2012 支援。
- **虛擬記憶體大小︰**Standard_D15_v2 和 Standard_DS15_v2 都是唯一的支援 VM 執行個體大小。 如需詳細資訊，請參閱[Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md)文件。 將會在未來展開支援 VM 執行個體大小的設定。

將本項變更這些限制，透過[Azure 虛擬網路更新](https://azure.microsoft.com/updates/accelerated-networking-in-preview)頁面。

## <a name="create-a-windows-vm-with-accelerated-networking"></a>建立 Windows VM 加速網路

1. 開啟 PowerShell 命令提示字元，並完成其餘的步驟，在此區段內的單一 PowerShell 工作階段。 如果您還沒有 PowerShell 安裝和設定，完成[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文件中的步驟。
2. 若要註冊預覽，傳送電子郵件與您的訂閱 ID[加速網路的訂閱](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，適合使用。 您收到電子郵件通知您已在預覽將已接受之後，請完成其餘的步驟，直到。
3. 註冊的功能，與您的訂閱，輸入下列命令︰

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. 取代*westcentralus*支援此功能 （如有需要），這篇文章的 [[限制](#limitations)] 區段中所列的其他位置的名稱。 輸入下列命令以設定一個變數的位置︰

        $locName = "westcentralus"

5. 資源群組，將包含新的網路介面輸入下列命令以建立名稱來取代*RG1* :

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. 變更*VM1 NIC1*到想要命名的網路介面，然後輸入下列命令︰

        $NICName = "VM1-NIC1"

7. 網路介面必須連接到的子網路中現有 Azure 虛擬網路 (VNet) 中的相同位置和[訂閱](../azure-glossary-cloud-terminology.md#subscription)的網路介面。 進一步瞭解 VNets 閱讀[虛擬網路概觀](virtual-networks-overview.md)文章，如果您不熟悉這些。 若要建立 VNet，完成[建立 VNet](virtual-networks-create-vnet-arm-ps.md)文件中的步驟。 變更 [值] 的下列 $Variables VNet 及您要連線至網路介面子網路的名稱。

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    如果您不知道您在步驟 3 中所選擇的位置中現有 VNet 的名稱，輸入下列命令︰
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    傳回的清單是空值，如果您需要建立 VNet 的位置。 若要建立 VNet，完成[建立虛擬網路](virtual-networks-create-vnet-arm-ps.md)文件中的步驟。

    若要取得 VNet 中的子網路的名稱，輸入下列命令，然後*Subnet1*上方取代子網路的名稱︰
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. 輸入下列命令以擷取 VNet 和子網路，並將他們指派給變數。

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. 找出現有公用 IP 位址的資源，讓您可以透過網際網路連線至該可以網路介面相關聯。 如果您不想要透過網際網路存取網路介面 VM，您可以略過此步驟。 沒有公用 IP 位址，您必須從另一個 VM 連線到相同的 VNet 連線至 VM。 

    變更*PIP1*名稱的現有公用 IP 位址資源存在於中您要建立的網路介面和無法與其他網路介面目前相關聯的位置。 如有必要，變更 $rgName 的公用 IP 位址資源的 [資源] 群組的名稱中存在，然後輸入下列命令︰

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    如果您不知道現有的公用 IP 位址資源的名稱，請輸入下列命令︰

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    如果**IPConfiguration**資料行傳回的輸出中沒有值的公用 IP 位址資源無法與現有的網路介面相關聯，也可以使用。 如果清單是空白的或沒有可用的公用 IP 位址資源，您可以建立一個使用 [新增 AzureRmPublicIPAddress] 命令。

    >[AZURE.NOTE] 公用 IP 位址已 nominal 的費用。 進一步瞭解價格閱讀 [ [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)的頁面。
10. 如果您選擇不新增公用的 IP 位址資源介面，移除*-PublicIPAddress $PIP1*結尾的後面的命令。 建立與加速網路的網路介面輸入下列命令︰

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. 依照下列步驟 3 和 6 的[建立 VM](../virtual-machines/virtual-machines-windows-ps-create.md)文章中的指示建立 VM vm 指派的網路介面。 在步驟 6-2 取代*Standard_A1* VM 大小[限制](#limitations)] 區段的本文中所列的其中一個。

    >[AZURE.NOTE] 如果您變更*名稱*$locName、 $rgName 或在本文中的 $nic 變數]，將無法建立 VM 文章中的步驟 6。 不過，您可以變更變數的*值*。

12. VM 建立後，下載[加速網路驅動程式](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)、 連線至 VM，並執行 VM 內部驅動程式安裝程式。

13. 以滑鼠右鍵按一下 [Windows] 按鈕，然後按一下**[裝置管理員**]。 驗證的**Mellanox ConnectX 3 虛擬函數乙太網路介面卡**會出現在 [**網路**] 選項中展開時，如下圖所示︰

    ![[裝置管理員](./media/virtual-network-accelerated-networking-powershell/image2.png)