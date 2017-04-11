<properties
    pageTitle="擷取 Linux VM 使用另存為範本 |Microsoft Azure"
    description="瞭解如何擷取及一般化 Linux 型 Azure 虛擬機器 (VM) 使用 Azure 資源管理員部署模型建立的圖像。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>擷取 Azure 上執行 Linux 虛擬機器

請遵循本文一般化擷取 Azure Linux 虛擬機器 (VM) 資源管理員部署模型中的步驟進行。 當您一般化 VM 時，移除個人帳戶資訊並準備要做為圖像 VM。 然後，您會擷取一般化虛擬硬碟 (VHD) 圖像 OS、 Vhd 附加的資料磁碟和新的 VM 部署[資源管理員範本](../azure-resource-manager/resource-group-overview.md)。 

若要建立 Vm 使用圖像，設定網路資源的每個新的 VM，並將其部署擷取 VHD 圖像中使用的範本 （JavaScript Object Notation 或 JSON，檔案）。 如此一來，您可以複製 VM 與目前軟體設定，您可以使用圖像中 Azure Marketplace 的方式很類似。

>[AZURE.TIP]如果您想要使用其特殊的狀態，以備份或偵錯時，建立您現有的 Linux VM 複本，請參閱[建立 Linux 虛擬機器 Azure 上執行的複本](virtual-machines-linux-copy-vm.md)。 如果您想要上傳 Linux VHD 從內部部署 VM，請參閱和[上傳並建立自訂的磁碟映像的 Linux VM](virtual-machines-linux-upload-vhd.md)。  

## <a name="before-you-begin"></a>開始之前

請確定您符合下列條件︰

* **資源管理員部署模型中建立的 azure VM** -如果您還沒有建立 Linux VM，您可以使用[入口網站](virtual-machines-linux-quick-create-portal.md)、 [Azure CLI](virtual-machines-linux-quick-create-cli.md)或[資源管理員範本](virtual-machines-linux-cli-deploy-templates.md)。 

    如有需要請設定 VM。 例如您[新增資料磁碟](virtual-machines-linux-add-disk.md)，套用更新，並安裝應用程式。 
* **Azure CLI** - [Azure CLI](../xplat-cli-install.md)本機電腦上安裝。

## <a name="step-1-remove-the-azure-linux-agent"></a>步驟 1︰ 移除 Azure Linux 代理程式

首先，請執行**waagent**命令和**deprovision**參數 Linux VM 上。 這個命令刪除檔案和資料，讓 VM 準備好進行一般化。 如需詳細資訊，請參閱[Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)。

1. 連線至您的 SSH 用戶端的 Linux VM。

2. 在 [SSH] 視窗中，輸入下列命令︰

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] 只有在您想要擷取圖像為 VM 上執行此命令。 它不保證圖像會清除所有的機密資訊，或適用於發佈。

3. 輸入**y**繼續進行。 您可以新增**-強制**參數，若要避免此確認步驟。

4. 命令完成之後，輸入**結束**。 此步驟會關閉 SSH 用戶端。

    
## <a name="step-2-capture-the-vm"></a>步驟 2︰ 擷取 VM

使用 Azure CLI 歸納與擷取 VM。 在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含**myResourceGroup**、 **myVnet**及**myVM**。

5. 從您的本機電腦，開啟 [Azure CLI [Azure 訂閱登入](../xplat-cli-connect.md)。 

6. 請確定您是在資源管理員模式。

    ```
    azure config mode arm
    ```

7. 關閉 VM 的已取消提供使用下列命令︰

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. 一般化 VM 使用下列命令︰

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. 現在，請執行**azure vm 擷取**命令，擷取 VM。 在下列範例中，使用名稱開頭為**MyVHDNamePrefix**，擷取圖像 Vhd 並**-t**選項指定範本**MyTemplate.json**的路徑。 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]根據預設，原始 VM 用的同一個儲存帳戶中建立圖像 VHD 檔案。 使用*相同的儲存空間帳戶*來儲存 Vhd 的任何新的 Vm 您建立的圖像。 

6. 若要尋找所擷取的圖像的位置，請在文字編輯器中開啟 JSON 範本。 在**storageProfile**中，尋找 [ **uri**位於**系統**容器中的**圖像**]。 例如，是類似 OS 磁碟映像的 URI`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步驟 3︰ 建立 VM 從所擷取的圖像
現在使用的範本圖像，建立 Linux VM。 這些步驟會說明如何使用 Azure CLI 和您建立新的虛擬網路 VM 擷取 JSON 檔案範本。

### <a name="create-network-resources"></a>建立網路資源

若要使用的範本，您必須設定您的新 VM 的虛擬網路和 NIC。 我們建議您建立這些資源的資源群組中儲存 VM 圖像的位置。 執行的命令類似下列、 相仿名稱資源與適當的 Azure 位置 (在這些命令為 「 centralus 」):

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>取得的 nic 識別碼

若要使用您儲存在擷取 JSON 部署 VM 從影像，您需要在 NIC 的識別碼 取得，執行下列命令︰

    azure network nic show MyResourceGroup1 myNIC

輸出中**Id**是類似`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>建立 VM
現在，請執行下列命令以建立您 VM 從所擷取的 VM 圖像。 您可以使用**-f**參數來指定您儲存的範本 JSON 檔案的路徑。

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

在命令輸出中，會提示您提供的新 VM 名稱與管理員的使用者名稱和密碼，您先前建立的 nic 的識別碼。

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

下列範例會顯示您看到成功部署︰

    + 初始化範本設定參數及參數值
    + 建立部署資訊︰ 建立範本部署 xxxxxxx
    + 等待部署完成資料︰ DeploymentName: MyDeployment 資料︰ ResourceGroupName: MyResourceGroup1 資料︰ ProvisioningState︰ 成功資料︰ 時間戳記︰ xxxxxxx 資料︰ 模式︰ 累加資料︰ 名稱] 類型的值


    data:    ------------------  ------------  -------------------------------------

    資料︰ vmName 字串 myNewVM


    資料︰ vmSize 字串 Standard_D1


    資料︰ adminUserName 字串 myAdminuser


    資料︰ adminPassword SecureString 未定義


    資料︰ networkInterfaceId 字串 /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic 資訊︰ 群組部署會建立 [確定] 命令

### <a name="verify-the-deployment"></a>驗證部署

現在您要建立驗證的部署及開始使用新的 VM 虛擬機器 SSH。 若要透過 SSH 連線，尋找您執行下列命令以建立的 VM 的 IP 位址︰

    azure network public-ip show MyResourceGroup1 myIP

在命令輸出列的公用 IP 位址。 依預設您連線至的連接埠 22 SSH Linux VM。

## <a name="create-additional-vms"></a>建立其他 Vm
用於擷取的影像與範本部署其他 Vm 使用前一節中的步驟。 若要從圖像建立 Vm 其他選項包括使用快速入門範本，或執行**azure vm 建立**命令。

### <a name="use-the-captured-template"></a>使用所擷取的範本

若要擷取的影像與範本使用，請遵循下列步驟 （前一節的詳細說明）︰

* 確定 VM 圖像位於相同的儲存空間帳戶裝載您 VM VHD。
* 複製範本 JSON 檔案，並指定新的 VM VHD （或 Vhd） OS 磁碟的唯一名稱。 例如，在**storageProfile**，底下**vhd**， **uri**中, 指定 [ **osDisk** VHD，類似的唯一名稱`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* 建立 NIC 相同或不同的虛擬網路中。
* 使用已修改的範本 JSON 檔案，請建立一個部署您設定虛擬網路上的 [資源] 群組中。

### <a name="use-a-quickstart-template"></a>使用範本快速入門

如果您想設定當您從自動建立 VM 圖像的網路，您可以在範本中指定的資源。 例如，請參閱 GitHub [101 vm 從-使用者-映像的範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)。 此範本建立 VM 自訂圖像必要的虛擬網路、 公用 IP 位址]，與 NIC 資源。 Azure 入口網站中使用範本的逐步解說，請參閱[如何建立虛擬機器從自訂範本資源管理員的圖像](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)。

### <a name="use-the-azure-vm-create-command"></a>使用 azure vm 建立] 命令

通常是使用資源管理員範本來建立圖像 VM 最簡單的方法。 不過，您可以建立 VM_強制_使用**-Q** **azure vm 建立**] 命令 (**-圖像 urn**) 參數。 如果您使用此方法時，您也可以傳遞**-d** (**-os-磁碟-vhd**) 參數，若要指定新 VM OS.vhd 檔案的位置。 這個檔案必須 vhd 容器圖像 VHD 檔案所在的儲存空間帳戶中。 命令複製 VHD 新 vm 自動**vhd**容器。

執行**azure vm 建立**內含圖像之前，先完成下列步驟︰

1.  建立資源群組，或找出以供部署現有的資源群組]。

2.  建立新的 VM 公用的 IP 位址資源和 NIC 資源。 若要使用 CLI 建立一個虛擬網路、 公用 IP 位址及 NIC 的步驟，請參閱本文前面。 （**azure vm 建立**也可以建立 NIC，但您需要將其他虛擬網路與子網路的參數傳遞）。


再執行命令，將 Uri 傳遞到新的 OS VHD 檔案與現有的圖像。 在此範例中，在大小 Standard_A1 VM 會建立在東亞美國地區。

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

使用其他的命令選項，請執行`azure help vm create`。

## <a name="next-steps"></a>後續步驟

若要管理您的 Vm CLI 使用，請參閱中的工作[部署及管理虛擬機器使用 Azure 資源管理員範本和 Azure CLI](virtual-machines-linux-cli-deploy-templates.md)。
