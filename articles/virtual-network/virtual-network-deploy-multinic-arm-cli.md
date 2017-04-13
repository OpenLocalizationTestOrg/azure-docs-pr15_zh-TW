<properties
   pageTitle="部署使用 Azure CLI 資源管理員] 中的多重 NIC Vm |Microsoft Azure"
   description="瞭解如何使用 Azure CLI 資源管理員] 中的多重 NIC Vm 的部署"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-using-the-azure-cli"></a>部署多重 NIC Vm 使用 Azure CLI

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](virtual-network-deploy-multinic-classic-cli.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

目前，您不能使用單一 NIC Vm 和使用多個 Nic Vm 相同的 [資源] 群組中。 因此，您需要實作後端伺服器位於不同的資源群組的其他所有元件。 下列步驟使用 [資源群組*IaaSStory*主要資源] 群組中，與*IaaSStory 後端*的後端伺服器。

## <a name="prerequisites"></a>必要條件

您可以佈署後端伺服器之前，必須先部署這個案例中所需的所有資源的主要資源群組。 若要部署這些資源，請遵循下列步驟。

1. 瀏覽至[[範本] 頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在 [範本] 頁面右側的 [**父項資源群組**中，按一下 [**部署至 Azure**]。
3. 如有需要變更參數值，然後遵循部署資源群組 Azure 預覽入口網站中的步驟。

> [AZURE.IMPORTANT] 請確定您儲存的帳戶名稱都是唯一的。 您不能 Azure 中有重複的儲存體帳戶名稱。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>部署後端 Vm

後端 Vm 取決於下列資源的建立。

- **儲存帳戶的資料**。 更佳的效能，請在資料庫伺服器上的資料磁碟會使用實心狀態磁碟機 (SSD) 技術，需要進階版儲存帳戶。 請確定您部署至支援進階版儲存空間的 Azure 位置。
- **Nic**。 每個 VM 會有兩個 Nic，其中一個資料庫存取另一個用於管理。
- **可用性設定**。 所有資料庫伺服器便會都新增至單一的可用性設定，以確保 Vm 中至少一個已啟動和執行期間進行的維修作業。

### <a name="step-1---start-your-script"></a>步驟 1-啟動您的指令碼

您可以下載用的完整艦隊指令碼[以下](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh)。 請遵循下列步驟來變更搭配使用您的環境中的指令碼。

1. 變更根據您現有的資源群組部署上方[的先決條件](#Prerequisites)變數下列的值。

        existingRGName="IaaSStory"
        location="westus"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"
        remoteAccessNSGName="NSG-RemoteAccess"

2. 變更您想要使用的後端部署的數值為根據的變數下列的值。

        backendRGName="IaaSStory-Backend"
        prmStorageAccountName="wtestvnetstorageprm"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        publisher="Canonical"
        offer="UbuntuServer"
        sku="14.04.2-LTS"
        version="latest"
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskName="datadisk"
        nicNamePrefix="NICDB"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

3. 擷取的識別碼`BackEnd`Vm 將會建立子網路。 您需要執行此動作，因為此子網路相關聯 Nic 位於不同的資源群組。

        subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
                        --vnet-name $vnetName \
                        --name $backendSubnetName|grep Id)"
        subnetId=${subnetId#*/}

    >[AZURE.TIP] 上述的第一個命令會使用[grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html)和[字串操作](http://tldp.org/LDP/abs/html/string-manipulation.html)（更明確地說，子字串移除）。

4. 擷取的識別碼`NSG-RemoteAccess`NSG。 您需要執行此動作，因為相關聯，到這個 NSG Nic 位於不同的資源群組。

        nsgId="$(azure network nsg show --resource-group $existingRGName \
                        --name $remoteAccessNSGName|grep Id)"
        nsgId=${nsgId#*/}

### <a name="step-2---create-necessary-resources-for-your-vms"></a>步驟 2-建立必要的資源，為您的 Vm

1. 建立新的後端的所有資源的資源群組。 請注意，使用`$backendRGName`變數的資源群組名稱，然後`$location`Azure 的區域。

        azure group create $backendRGName $location

2. 建立進階版儲存帳戶 OS 和資料使用您的帳戶 Vm 磁碟。

        azure storage account create $prmStorageAccountName \
            --resource-group $backendRGName \
            --location $location \
            --type PLRS

3. 建立設定 Vm 的可用性。

        azure availset create --resource-group $backendRGName \
            --location $location \
            --name $avSetName

### <a name="step-3---create-the-nics-and-backend-vms"></a>步驟 3-建立 Nic 和後端 Vm

1. 開始建立多個 Vm，根據迴圈`numberOfVMs`變數。

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. 針對每個 VM，建立資料庫存取 NIC。

            nic1Name=$nicNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x
            azure network nic create --name $nic1Name \
                --resource-group $backendRGName \
                --location $location \
                --private-ip-address $ipAddress1 \
                --subnet-id $subnetId

3. 針對每個 VM，建立遠端存取的 NIC。 通知`--network-security-group`參數，用來產生關聯到 NSG NIC。

            nic2Name=$nicNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x
            azure network nic create --name $nic2Name \
                --resource-group $backendRGName \
                --location $location \
                --private-ip-address $ipAddress2 \
                --subnet-id $subnetId $vnetName \
                --network-security-group-id $nsgId

4. 建立 VM。

            azure vm create --resource-group $backendRGName \
                --name $vmNamePrefix$suffixNumber \
                --location $location \
                --vm-size $vmSize \
                --subnet-id $subnetId \
                --availset-name $avSetName \
                --nic-names $nic1Name,$nic2Name \
                --os-type linux \
                --image-urn $publisher:$offer:$sku:$version \
                --storage-account-name $prmStorageAccountName \
                --storage-account-container-name vhds \
                --os-disk-vhd $osDiskName$suffixNumber.vhd \
                --admin-username $username \
                --admin-password $password

5. 針對每個 VM，建立兩個資料磁碟，及結束與循環播放`done`] 命令。

            azure vm disk attach-new --resource-group $backendRGName \
                --vm-name $vmNamePrefix$suffixNumber \        
                --storage-account-name $prmStorageAccountName \
                --storage-account-container-name vhds \
                --vhd-name $dataDiskName$suffixNumber-1.vhd \
                --size-in-gb $diskSize \
                --lun 0

            azure vm disk attach-new --resource-group $backendRGName \
                --vm-name $vmNamePrefix$suffixNumber \        
                --storage-account-name $prmStorageAccountName \
                --storage-account-container-name vhds \
                --vhd-name $dataDiskName$suffixNumber-2.vhd \
                --size-in-gb $diskSize \
                --lun 1
        done


### <a name="step-4---run-the-script"></a>步驟 4-執行指令碼

現在，您可以下載並變更您的需求所根據的指令碼，執行使用多個 Nic 建立後端資料庫 Vm 的指令碼。

1. 儲存您的指令碼，並從**被**終端機執行。 您會看到 [初始輸出] 清單，如下所示。

        info:    Executing command group create
        info:    Getting resource group IaaSStory-Backend
        info:    Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command availset create
        info:    Looking up the availability set "ASDB"
        info:    Creating availability set "ASDB"
        info:    availset create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB1-DA"
        info:    Creating network interface "NICDB1-DA"
        info:    Looking up the network interface "NICDB1-DA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
        data:    Name                            : NICDB1-DA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB1-RA"
        info:    Creating network interface "NICDB1-RA"
        info:    Looking up the network interface "NICDB1-RA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
        data:    Name                            : NICDB1-RA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.54
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command vm create
        info:    Looking up the VM "DB1"
        info:    Using the VM Size "Standard_DS3"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account wtestvnetstorageprm
        info:    Looking up the availability set "ASDB"
        info:    Found an Availability set "ASDB"
        info:    Looking up the NIC "NICDB1-DA"
        info:    Looking up the NIC "NICDB1-RA"
        info:    Creating VM "DB1"

2. 請稍候幾分鐘執行會結束，如下所示，您會看到輸出的其餘部分。

        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB1"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
        info:    Updating VM "DB1"
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB1"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
        info:    Updating VM "DB1"
        info:    vm disk attach-new command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB2-DA"
        info:    Creating network interface "NICDB2-DA"
        info:    Looking up the network interface "NICDB2-DA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
        data:    Name                            : NICDB2-DA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.5
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB2-RA"
        info:    Creating network interface "NICDB2-RA"
        info:    Looking up the network interface "NICDB2-RA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
        data:    Name                            : NICDB2-RA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.55
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command vm create
        info:    Looking up the VM "DB2"
        info:    Using the VM Size "Standard_DS3"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account wtestvnetstorageprm
        info:    Looking up the availability set "ASDB"
        info:    Found an Availability set "ASDB"
        info:    Looking up the NIC "NICDB2-DA"
        info:    Looking up the NIC "NICDB2-RA"
        info:    Creating VM "DB2"
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB2"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
        info:    Updating VM "DB2"
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB2"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
        info:    Updating VM "DB2"
        info:    vm disk attach-new command OK
