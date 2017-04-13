<properties
   pageTitle="部署多重 NIC Vm 傳統部署模型中使用 Azure CLI |Microsoft Azure"
   description="瞭解如何部署多重 NIC Vm 傳統部署模型中使用 Azure CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>部署使用 Azure CLI 多重 NIC Vm （傳統）

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

您可以建立 Azure 虛擬機器 (Vm)，並將多個網路介面 (Nic) 附加至每個您 Vm。 多個 Nic 上 Nic 啟用流量類型的分隔。 例如，NIC 可能會與網際網路通訊，另一個通訊只能搭配內部資源未連線到網際網路時。 跨多個 Nic 分隔網路流量的功能時需要多個網路虛擬應用裝置，例如應用程式後傳遞，但 WAN 最佳解決方案。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](virtual-network-deploy-multinic-arm-cli.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

目前，您不能使用單一 NIC Vm 和使用多個 Nic Vm 相同的雲端服務中。 因此，您需要在不同的雲端服務比和在此案例中的其他所有元件實作後端伺服器。 下列步驟使用的後端伺服器命名*IaaSStory*的主要的資源及*IaaSStory 後端*雲端服務。

## <a name="prerequisites"></a>必要條件

您可以佈署後端伺服器之前，必須先部署這種情況下的所有必要的資源主雲端服務。 最小值，在您要使用的後端子網路建立虛擬網路。 若要瞭解如何部署虛擬網路[建立使用 Azure CLI 虛擬網路](virtual-networks-create-vnet-classic-cli.md)，請造訪。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>部署後端 Vm

後端 Vm 取決於下列資源的建立。

- **儲存帳戶的資料**。 更佳的效能，請在資料庫伺服器上的資料磁碟會使用實心狀態磁碟機 (SSD) 技術，需要進階版儲存帳戶。 請確定您部署至支援進階版儲存空間的 Azure 位置。
- **Nic**。 每個 VM 會有兩個 Nic，其中一個資料庫存取另一個用於管理。
- **可用性設定**。 所有資料庫伺服器便會都新增至單一的可用性設定，以確保 Vm 中至少一個已啟動和執行期間進行的維修作業。

### <a name="step-1---start-your-script"></a>步驟 1-啟動您的指令碼

您可以下載用的完整艦隊指令碼[以下](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)。 請遵循下列步驟來變更搭配使用您的環境中的指令碼。

1. 變更根據您現有的資源群組部署上方[的先決條件](#Prerequisites)變數下列的值。

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. 變更您想要使用的後端部署的數值為根據的變數下列的值。

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>步驟 2-建立必要的資源，為您的 Vm

1. 建立新的雲端服務的所有的後端 Vm。 請注意，使用`$backendCSName`變數的資源群組名稱，然後`$location`Azure 的區域。

        azure service create --serviceName $backendCSName \
            --location $location

2. 建立進階版儲存帳戶 OS 和資料使用您的帳戶 Vm 磁碟。

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>步驟 3-使用多個 Nic 建立 Vm

1. 開始建立多個 Vm，根據迴圈`numberOfVMs`變數。

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. 針對每個 VM，指定的名稱及 IP 位址的兩個 Nic 每個。

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. 建立 VM。 請注意的使用方式`--nic-config`參數，其中包含的所有 Nic 含有名稱、 子網路與 IP 位址清單。

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. 針對每個 VM，建立兩個資料磁碟。

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>步驟 4-執行指令碼

現在，您可以下載並變更您的需求所根據的指令碼，執行使用多個 Nic 建立後端資料庫 Vm 的指令碼。

1. 儲存您的指令碼，並從**被**終端機執行。 您會看到 [初始輸出] 清單，如下所示。

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. 請稍候幾分鐘執行會結束，如下所示，您會看到輸出的其餘部分。

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
