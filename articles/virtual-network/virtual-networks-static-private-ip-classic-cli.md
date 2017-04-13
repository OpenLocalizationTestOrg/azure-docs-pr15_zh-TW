<properties 
   pageTitle="如何設定靜態私人 ip 位址的傳統模式 ausing CLI |Microsoft Azure"
   description="了解靜態私人 IPs (DIPs)，以及如何管理其中使用 CLI 傳統模式"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>如何設定私人的靜態 IP 位址 （傳統） 中 Azure CLI

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[管理資源管理員部署模型的靜態私用的 IP 位址](virtual-networks-static-private-ip-arm-cli.md)。

下列範例 Azure CLI 命令預期已經建立一個簡單環境。 如果您想要執行這份文件中所顯示的命令，先建立測試環境中[建立 vnet](virtual-networks-create-vnet-classic-cli.md)所述。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何建立 VM 時，指定私人的靜態 IP 位址
若要建立新的 VM 中名為 「 *DNS01*新名為*TestService*根據上述情況雲端服務，請遵循下列步驟︰

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。
1. 執行**azure 服務所建立**的命令來建立雲端服務。

        azure service create TestService --location uscentral

    預期的輸出︰

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. 執行**azure 建立 vm**命令以建立 VM。 請注意私人的靜態 IP 位址] 的值。 顯示後輸出說明所用的參數的清單。

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    預期的輸出︰

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l （或--位置）**。 將會建立 VM azure 的區域。 我們案例中， *centralus*。
    - **-n （或-vm 名稱）**。 若要建立 VM 的名稱。
    - **-w （或--虛擬網路名稱）**。 將會建立 VM VNet 的名稱。 
    - **日 （或--靜態 ip）**。 私人的靜態 IP 位址 VM。
    - **TestService**。 將會建立 VM 雲端服務的名稱。
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**。 用來建立 VM 的圖像。
    - **機器**。 Windows vm 本機系統管理員。
    - **AdminP@ssw0rd**. Windows vm 本機系統管理員密碼。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何擷取 vm 靜態私人 IP 位址資訊
若要檢視使用上述的指令碼建立 vm 靜態私用的 IP 位址資訊，請執行下列 Azure CLI 命令並*網路 StaticIP*觀察值︰

    azure vm static-ip show DNS01

預期的輸出︰

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何從 VM 移除私人的靜態 IP 位址
若要移除私人的靜態 IP 位址新增 VM 中的指令碼，請執行下列 Azure CLI 命令︰
    
    azure vm static-ip remove DNS01

預期的輸出︰

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>如何將靜態私人 ip 位址新增至現有的 VM
若要新增的靜態私人 IP 位址是使用上述 runt 的指令碼所建立的 vm 他下列命令︰

    azure vm static-ip set DNS01 192.168.1.101

預期的輸出︰

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>後續步驟

- 深入了解[保留的公用 IP](virtual-networks-reserved-public-ip.md)位址。
- 瞭解[執行個體層級的公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)地址。
- [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)，請參閱。
