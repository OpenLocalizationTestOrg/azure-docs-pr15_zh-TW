<properties
    pageTitle="建立一份您 Azure Linux VM |Microsoft Azure"
    description="瞭解如何在資源管理員部署模型中建立一份 Azure Linux 虛擬機器"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>建立 Linux 虛擬機器 Azure 上執行的複本


本文將示範如何建立一份 Azure 虛擬機器 (VM) 執行 Linux 使用資源管理員部署模型。 第一次您的作業系統和資料磁碟複製到新的容器，然後設定網路資源及建立新的虛擬機器。

您也可以[上傳並建立自訂的磁碟映像從 VM](virtual-machines-linux-upload-vhd.md)。


## <a name="before-you-begin"></a>開始之前

請確定您符合下列條件，然後才能開始步驟︰

- 您有 [Azure CLI] (.../ xplat-cli-install.md） 下載並安裝在您的電腦上。 

- 您也會需要您現有的 Azure Linux VM 相關的部分資訊︰

| 來源 VM 資訊 | 若要取得的位置 |
|------------|-----------------|
| VM 名稱 | `azure vm list` |
| 資源群組名稱 | `azure vm list` |
| 位置 | `azure vm list` |
| 儲存體帳戶名稱 | `azure storage account list -g <resourceGroup>` |
| 容器名稱 | `azure storage container list -a <sourcestorageaccountname>` |
| 來源 VM VHD 檔案名稱 | `azure storage blob list --container <containerName>` |



- 您將需要您的新 VM 一些選擇︰   <br> 容器名稱   <br> -VM 名稱   <br> -虛擬記憶體大小   <br> -vNet 名稱   <br> 子網路名稱   <br> IP 名稱   <br> -NIC 名稱
    

## <a name="login-and-set-your-subscription"></a>登入並設定您的訂閱

1. 登入 CLI。
        
        azure login

2. 請確定您是在資源管理員模式。
    
        azure config mode arm

3. 設定正確的訂閱。 您可以使用 「 azure 帳戶清單 」 若要查看您所有的訂閱。

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>停止 VM 

停止，且解除 VM 的來源。 您可以使用 「 azure vm 清單 」，取得清單的所有在您的訂閱，其資源 Vm 群組名稱。
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>複製 VHD


您也可以將 VHD 從來源儲存空間中，複製到目的地使用`azure storage blob copy start`。 在此範例中，我們將 VHD 複製到相同的儲存空間帳戶，但不同的容器。

若要將 VHD 複製到另一個容器中的相同的儲存空間帳戶，請輸入︰

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>設定您的新 VM 虛擬網路

針對您的新 VM 設定的虛擬網路和 NIC。 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>建立新的 VM 

您現在可以建立 VM[使用資源管理員範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)您上傳的虛擬磁碟，或透過 CLI 指定到您複製的磁碟 URI 輸入︰

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 Azure CLI 管理新的虛擬機器，請參閱[Azure CLI 命令為 Azure 資源管理員](azure-cli-arm-commands.md)。
