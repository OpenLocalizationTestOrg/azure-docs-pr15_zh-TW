<properties 
   pageTitle="如何設定靜態私人 ip 位址手臂模式使用 CLI |Microsoft Azure"
   description="了解靜態 IPs (DIPs)，以及如何管理其使用 CLI ARM 模式"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-azure-cli"></a>如何在 Azure CLI 設定私人的靜態 IP 位址

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[管理靜態私人傳統部署模型中的 IP 位址](virtual-networks-static-private-ip-classic-cli.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

下列範例 Azure CLI 命令預期已經建立一個簡單環境。 如果您想要執行這份文件中所顯示的命令，先建立測試環境中[建立 vnet](virtual-networks-create-vnet-arm-cli.md)所述。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何建立 VM 時，指定私人的靜態 IP 位址
若要建立 VM 中名為 「 *DNS01*名為 [固定的*192.168.1.101*私人 ip *TestVNet* VNet *FrontEnd*子網路，請遵循下列步驟︰

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。

2. 執行**azure config 模式**命令以切換到資源管理員模式，如下所示。

        azure config mode arm

    預期的輸出︰

        info:    New mode is arm

3. 執行**azure 網路的公用 ip 建立**vm 建立的公用 IP。 顯示後輸出說明所用的參數的清單。

        azure network public-ip create -g TestRG -n TestPIP -l centralus
    
    預期的輸出︰

        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK

    - **-g （或--資源群組）**。 公用 IP 將會建立在資源群組的名稱。
    - **-n （或--名稱）**。 公用 IP 的名稱。
    - **-l （或--位置）**。 將會建立的公用 IP azure 的區域。 我們案例中， *centralus*。

3. 執行**azure 網路 nic 建立**命令以建立 NIC 靜態私人 ip 位址。 顯示後輸出說明所用的參數的清單。

        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

    預期的輸出︰

        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

    - **-（或--私人 ip 位址）**。 在 NIC 私人靜態 IP 位址
    - **-m （或--子網路 vnet 名稱）**。 將會建立在 NIC VNet 的名稱。
    - **-k （或--子網路名稱）**。 在 NIC 將會建立子網路的名稱。

4. 執行**azure vm 建立**的命令來建立 VM 使用的公用 IP 及 NIC 先前所建立。 顯示後輸出說明所用的參數的清單。

        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

    預期的輸出︰

        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK

    - **-y （或-os 類型）**。 類型的作業系統 vm， *Windows*或*Linux*。
    - **-f （或-nic 名稱）**。 會使用 NIC VM 的名稱。
    - **-i （或--公用 ip 名稱）**。 會使用的公用 IP VM 名稱。
    - **-F （或-vnet 名稱）**。 將會建立 VM VNet 的名稱。
    - **-j （或-vnet 子網路名稱）**。 將會建立 VM 子網路的名稱。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何擷取 vm 靜態私人 IP 位址資訊

若要檢視使用上述的指令碼建立 vm 靜態私用的 IP 位址資訊，請執行下列 Azure CLI 命令並*私人 IP 配置方法*和*私人 IP 位址*觀察值︰

    azure vm show -g TestRG -n DNS01

預期的輸出︰

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何從 VM 移除私人的靜態 IP 位址
您無法從 Azure CLI 的資源管理員在 NIC 移除私人的靜態 IP 位址。 您必須建立新的 NIC 使用動態 IP 與上一個 NIC 移除 VM，將新 NIC 新增至 VM。 若要變更 VM 使用 int 吧 NIC 上方的命令，請遵循下列步驟。
    
1. 執行**azure 網路 nic 建立**的命令來建立新的 NIC 使用動態 IP 配置。 請注意如何不需要這次指定的 IP 位址。

        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

    預期的輸出︰

        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

2. 執行 [若要變更使用 VM NIC **azure vm 設定**] 命令。

        azure vm set -g TestRG -n DNS01 -N TestNIC2

    預期的輸出︰

        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK

3. 如果不想，執行刪除舊 NIC **azure 網路 nic 刪除**命令

        azure network nic delete -g TestRG -n TestNIC --quiet

    預期的輸出︰

        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何將私人的靜態 IP 位址新增至現有的 VM
若要使用的是使用上述的指令碼所建立的 VM 在 NIC 新增私人的靜態 IP 位址，執行下列命令︰

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

預期的輸出︰

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>後續步驟

- 深入了解[保留的公用 IP](virtual-networks-reserved-public-ip.md)位址。
- 瞭解[執行個體層級的公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)地址。
- [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)，請參閱。
