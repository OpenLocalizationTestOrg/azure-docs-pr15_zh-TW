<properties
   pageTitle="多個 IP 位址的虛擬機器-PowerShell |Microsoft Azure"
   description="瞭解如何使用 PowerShell 的 Azure 虛擬機器中指定多個 IP 位址。"
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
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>將多個 IP 位址指派給虛擬機器

Azure 虛擬機器 (VM) 可以有一或多個網路介面 (NIC) 附加至該。 任何 NIC 可以有一或多個公用或私用 IP 位址指定給它。 如果您目前不熟悉 Azure 中的 IP 位址，請參閱[Azure 中的 IP 位址](virtual-network-ip-addresses-overview-arm.md)的文章若要進一步瞭解這些。 本文說明如何使用 PowerShell 的 Azure 指派 vm Azure 資源管理員部署模型中的多個 IP 位址。

將多個 IP 位址指派給 VM 啟用下列功能︰

- 管理多個網站或使用不同的 IP 位址和單一伺服器上的 SSL 憑證的服務。
- 做為網路虛擬應用裝置，例如防火牆或負載平衡器。
- 將功能新增任何私用的 IP 位址的 Nic 至 Azure 負載平衡器後端資料庫。 過去的主要 NIC 只有主要 IP 位址可能會新增到後端資料庫。

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

若要註冊預覽，傳送電子郵件至您的訂閱識別碼的[多個 Ip](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) ，適合使用。

## <a name="scenario"></a>案例

本文中，您會建立關聯的網路介面的三個 IP 設定。
下列範例設定會建立並指派給會有三個私用的 IP 位址及指派一個公用 IP 位址的 NIC:

- IPConfig-1︰ 動態私人 IP 位址 （預設值） 和公用 IP 位址從名為 PIP1 的公用 IP 位址資源。
- IPConfig 2︰ 私人的靜態 IP 位址和沒有公用 IP 位址。
- IPConfig 3︰ 動態私人的 IP 位址和沒有公用 IP 位址。

    ![圖像的替代文字](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

此案例假設您有一個稱為*RG1*內中，有一部分*VNet1* VNet 和稱為*Subnet1*子網路的資源群組。 此外，假設您有稱為*VM1*VM 與網路介面一部分*VM1 NIC1*相關聯的公用 IP 位址稱為*PIP1*。

[本文](./virtual-machines/virtual-machines-windows-ps-create.md )將引導如何建立前述以防您還沒有建立他們的資源。

## <a name = "create"></a>使用多個 IP 位址建立 VM

1. 開啟 PowerShell 命令提示字元，並完成其餘的步驟，在此區段內的單一 PowerShell 工作階段。 如果您還沒有 PowerShell 安裝和設定，完成[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文件中的步驟。

2. 變更下列 $Variables [值] 的 Azure[位置](https://azure.microsoft.com/regions)虛擬網路中的[資源] 群組中](../azure-resource-manager/resource-group-overview.md#resource-groups)，在 [資源] 群組、 您要連線，NIC 子網路和 NIC 的名稱 VNet 名稱 完成的步驟，將多個 IP 位址新增到任何 NIC 附加到 VM，視需要。

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    如果您不知道現有 Azure 位置或資源群組的名稱，輸入下列命令︰

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>在 NIC 必須連接到的子網路中現有 Azure 虛擬網路 (VNet)。 三個元件︰ NIC、 子網路及 VNet，所有中必須有相同的地區和[訂閱](../azure-glossary-cloud-terminology.md#subscription)。  如果您不熟悉 VNets，閱讀詳細了解這些或閱讀[建立 VNet](virtual-networks-create-vnet-arm-ps.md)文件，瞭解如何建立一個[虛擬網路概觀](virtual-networks-overview.md)文章。

    如果您不知道現有的 VNet 的名稱，輸入下列命令，並在上一個變數的*VNet1*取代 VNet 的名稱︰

        Get-AzureRmVirtualNetwork | Format-Table Name

    傳回清單是空值，如果您需要建立 VNet。 若要瞭解作法，請參閱[建立虛擬網路](virtual-networks-create-vnet-arm-ps.md)。

    輸入下列命令以取得 VNet 的現有子名稱，並*Subnet1*上方取代子網路名稱︰

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. 輸入下列命令以擷取子網路，並將其指派給變數。

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>定義您想要指派給在 NIC IP 設定 每個設定可以有一個靜態和動態私用的 IP 位址與一個公用 IP 位址資源靜態和動態的地址。

    新增或移除任何數目的追蹤取決於您想要設定您想要在 NIC 和設定關聯的多少 IP 位址的設定。

    **IPConfig-1**

    變更值*PIP1*的現有公用 IP 位址資源的位置，您要建立在 NIC 中存在的名稱，並無法與另一個 NIC 目前相關聯 資源群組的公用 IP 位址資源的名稱變更*RG1*存在於。 變更您想要讓第一個 IP 設定的名稱*IPConfig-1* 。 輸入下列命令︰

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    請注意*-主要*切換。 當您將多個 IP 組態指派給 NIC 時，必須指派一個設定為*主要*。 如果您不知道現有的公用 IP 位址資源的名稱，輸入下列命令︰ 取得 AzureRMPublicIPAddress |格式化表格名稱] 的位置，IPAddress IpConfiguration

    如果**IPConfiguration**資料行傳回的輸出中沒有值的公用 IP 位址資源無法與現有的 NIC 相關聯，也可以使用。 如果清單是空白的或沒有可用的公用 IP 位址資源，您可以建立一個使用 [**新增 AzureRmPublicIPAddress** ] 命令。

    >[AZURE.NOTE] 公用 IP 位址已 nominal 的費用。 若要進一步瞭解 IP 位址價格，閱讀 [ [IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses)的頁面。

    **IPConfig 2**

    變更您想要讓第二個 IP 設定，並將*10.0.0.5*變更為 [未使用有效的 IP 位址，要指派至 NIC 的子網路的名稱*IPConfig 2* 。 輸入下列命令︰

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    輸入下列命令，如果您不知道 IP 位址範圍指派給子網路︰

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    變更您想要授與給第三個 IP 設定，然後輸入下列命令的名稱*IPConfig 3* :

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] 您可以指定 NIC 最大 250 私用的 IP 位址 有可用於訂閱的公用 IP 位址的數目上限。 若要深入瞭解，請閱讀[Azure 限制](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager)文章。

6. 建立使用上一個步驟所述的 IP 設定 NIC。

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. 在 NIC 時附加建立 VM[建立 VM](../virtual-machines/virtual-machines-windows-ps-create.md)文件中的步驟進行。 雖然，請參閱建立執行 Windows Server VM，步驟會相同的 Linux VM，非選取不同的作業系統。 完成文件的步驟 1 到 3。 略過步驟 4 和 5，然後完成步驟 6 中建立的 VM 文章。

    >[AZURE.WARNING] 如果您變更為其他在本文中的步驟 6 中 $nic 變數或未完成此文件的先前的步驟，將會失敗建立 VM 文章中的步驟 6。

8. 檢視私用的 IP 位址指定給 NIC 該 Azure DHCP 及公用的 IP 位址資源分派給 NIC 中，輸入下列命令以︰

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>手動新增所有的私人 IP 位址 （包括主要） 作業系統 TCP/IP 設定。 

**Windows**

1. 在命令提示字元中，輸入*ipconfig/所有*。  您只會看到的*主要*私用的 IP 位址 （透過 DHCP)。
2. 下一步*依*輸入命令提示字元視窗。 這會開啟新視窗。
3. 開啟**區域連線**的內容。
4. 按兩下網際網路通訊協定第 4 (IPv4)
5. 選取 [**使用下列的 IP 位址**，然後輸入下列值︰
    - **IP 位址**︰ 輸入*主要*私用的 IP 位址
    - **子網路遮罩**︰ 根據您子網路的設定。 例如，如果子網路是 /24 子網路然後子網路遮罩是 255.255.255.0。
    - **預設閘道**︰ 子網路中的第一個 IP 位址。 如果您子網路 10.0.0.0/24，閘道器 IP 位址是 10.0.0.1。
    - 按一下 [**使用下列的 DNS 伺服器位址**，然後輸入下列值︰
        - **慣用的 DNS 伺服器︰**如果您不使用您自己的 DNS 伺服器，請輸入 168.63.129.16。  如果您是，輸入您的 DNS 伺服器 IP 位址。
    - 按一下 [**進階**] 按鈕，並新增額外的 IP 位址。 新增每個次要私人列出 NIC 到步驟 8 中使用相同的子網路的主要的 IP 位址指定的 IP 位址。
    - 按一下**[確定**] 關閉 TCP/IP 的設定，然後**[確定**] 以關閉介面卡設定。 這會再重新建立 RDP 連線。

6. 在命令提示字元中，輸入*ipconfig/所有*。 顯示您所新增的所有 IP 位址和 DHCP 已關閉。


**Linux (Ubuntu)**

1. 開啟終端機視窗。
2. 請確定您是根使用者。 如果您不是，您可以使用下列命令︰

            sudo -i
3. 更新設定檔的網路介面 （假設 「 eth0 」）。
    - 保留現有項目，以 dhcp。 這將會使用早設定主要的 IP 位址。
    - 新增其他靜態 IP 位址，並輸入下列命令的設定︰

                cd /etc/network/interfaces.d/
                ls

        您應該會看到.cfg 檔案。
4. 開啟的檔案︰ 六部分*檔案名稱*。

    您應該會看到下列幾行結尾的檔案︰

            auto eth0
            iface eth0 inet dhcp
5. 新增下列幾行後存在於檔案中的各行︰

            iface eth0 inet static
            address <your private IP address here>
6. 將檔案儲存使用下列命令︰

            :wq
7.  重設網路介面使用下列命令︰

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] 在同一行執行 ifdown 和 ifup，如果使用遠端連線。
8. 驗證的 IP 位址會新增至 [網路介面使用下列命令︰

            ip addr list eth0

    您應該會看到您新增為清單的組件的 IP 位址。

**Linux （Redhat CentOS，與其他人）**

1. 開啟終端機視窗。
2. 請確定您是根使用者。 如果您不是，您可以使用下列命令︰

            sudo -i
3. 輸入密碼，然後依照提示，請依照指示進行。 一旦根使用者，瀏覽至 [網路] 指令碼資料夾，使用下列命令︰

            cd /etc/sysconfig/network-scripts
4. 列出的相關的 ifcfg 檔案使用下列命令︰

            ls ifcfg-*

    您應該會看到*ifcfg eth0*為其中一個檔案。
5. 複製*ifcfg eth0*檔案，並將其命名*ifcfg-eth0:0*使用下列命令︰

            cp ifcfg-eth0 ifcfg-eth0:0
6. 編輯*ifcfg-eth0:0*檔案下列命令︰

            vi ifcfg-eth1
7. 將裝置變更為適當的名稱，在檔案。*eth0:0*在此例中使用下列命令︰

            DEVICE=eth0:0
8. 變更*IPADDR = YourPrivateIPAddress*行，以反映的 IP 位址。
9. 將檔案儲存與下列命令︰

            :wq
10. 重新啟動網路服務，請確定所做的變更是成功藉由執行下列命令︰

            /etc/init.d/network restart
            Ipconfig

    您應該會看到您所新增的 IP 位址*eth0:0*，傳回的清單中。

## <a name="add"></a>新增現有的 vm IP 位址

完成下列步驟將額外的 IP 位址新增到現有的 NIC:

1. 開啟 PowerShell 命令提示字元，並完成其餘的步驟，在此區段內的單一 PowerShell 工作階段。 如果您還沒有 PowerShell 安裝和設定，完成[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文件中的步驟。

2. 變更下列 $Variables [值]，而您想要新增的 IP 位址的資源群組及位置 NIC 中存在的名稱︰

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    如果您不知道您想要變更，輸入下列命令 NIC 的名稱，然後變更前一個變數的值︰

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. 建立一個變數，並將它設定為現有 NIC 中，輸入下列命令︰

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. 擷取 NIC 已連線至完成[步驟 3](#subnet)的 [建立 VM 的多個 IP 位址] 區段中的 [這份文件的子網路識別碼。

5. 建立您想要新增至網路，依照下列指示的[步驟 5](#ipconfigs)的 [建立 IP 設定 VM 使用多個 IP 位址] 區段中的 [這份文件。

6. 變更*$IPConfigName4* IP 設定您在上一個步驟中建立的名稱。 若要新增的設定，請輸入以下命令︰

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. 若要設定的 IP 設定 NIC，輸入下列命令︰

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. 新增您新增至 NIC VM 作業系統，依照下列指示的[步驟 9](#os)建立多個 IP 位址] 區段中的 [這份文件與 VM IP 位址。
