<properties
    pageTitle="建立和上傳自訂 Linux 影像 |Microsoft Azure"
    description="建立和上載文虛擬硬碟 (VHD) 至 Azure 自訂 Linux 圖像使用資源管理員部署模型。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>上傳並建立自訂的磁碟映像 Linux VM

本文將說明如何上傳至 Azure 使用資源管理員部署模型的虛擬硬碟 (VHD)，並建立 Linux Vm 從這個自訂的圖像。 此功能可讓您安裝並設定您的需求 Linux distro 然後使用該 VHD 快速地建立 Azure 虛擬機器 (Vm)。

## <a name="quick-commands"></a>快速命令
若要快速完成工作，下列區段詳細資料基底命令 VM 其上傳到 Azure。 更詳細的資訊和每個步驟的內容可找到其餘的文件中，[從這裡開始](#requirements)。

請確定您有[Azure CLI](../xplat-cli-install.md)登入並使用資源管理員模式︰

```bash
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `mystorageaccount`，及`myimages`。

首先，建立資源群組。 下列範例會建立資源群組名稱`myResourceGroup`中`WestUs`位置︰

```bash
azure group create myResourceGroup --location "WestUS"
```

建立保留虛擬磁碟儲存帳戶。 下列範例會建立名為儲存帳戶`mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

列出您的儲存空間帳戶便捷鍵。 請記下`key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

建立使用您得到的儲存空間金鑰您儲存帳戶內容器。 下列範例會建立名為容器`myimages`使用中的儲存空間值`key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

最後，將您 VHD 上傳到您所建立的容器。 指定在您 VHD 本機路徑`/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

您現在可以建立 VM[使用資源管理員範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)您上傳的虛擬磁碟。 您也可以使用 CLI 指定磁碟 URI (`--image-urn`)。 下列範例會建立名為 VM`myVM`先前使用虛擬磁碟上傳︰

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

目的地儲存帳戶必須是您上傳至虛擬磁碟相同。 您也需要指定，或回應提示所需的所有其他參數的`azure vm create`命令，例如虛擬網路、 的公用 IP 位址、 使用者名稱和 SSH 鍵。 您可以閱讀更多關於[使用 CLI 資源管理員參數](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)。

## <a name="requirements"></a>需求
若要完成下列步驟，您需要︰

- **Linux 作業系統安裝.vhd 檔案中**-安裝[Azure 背書 Linux 分配](virtual-machines-linux-endorsed-distros.md)（或看到[資訊非背書散佈](virtual-machines-linux-create-upload-generic.md)） 虛擬磁碟 VHD 格式。 若要建立的 VM 和 VHD 有多個的工具︰
    - 安裝和設定[QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU)或[KVM](http://www.linux-kvm.org/page/RunningKVM)，小心 VHD 作為影像格式。 如有需要您可以[將影像轉換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)使用`qemu-img convert`。
    - 您也可以使用 HYPER-V[在 Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)或[Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)。

> [AZURE.NOTE] Azure 中不支援較新的 VHDX 格式。 當您建立 VM 時，指定 VHD 成的格式。 如有需要您可以將 VHDX 磁碟轉換成 VHD 使用[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)或[`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx)PowerShell 指令程式。 此外，Azure 不支援，因此您需要將此類磁碟轉換成靜態 Vhd 上, 傳之前上傳動態 Vhd。 您可以使用工具，例如[Azure VHD 公用程式移](https://github.com/Microsoft/azure-vhd-utils-for-go)至轉換動態磁碟上傳到 Azure 的程序期間。

- 從您的自訂圖像建立 Vm 必須位於相同的儲存空間帳戶為本身的圖像
    - 建立儲存帳戶並按住您自訂的圖像 」 和 「 建立的 Vm 容器
    - 建立您的 Vm 之後，您可以安全地刪除圖像

請確定您有[Azure CLI](../xplat-cli-install.md)登入並使用資源管理員模式︰

```bash
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `mystorageaccount`，及`myimages`。


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>準備要上傳圖像

Azure 支援各種不同的 Linux 分配 （請參閱[背書散佈](virtual-machines-linux-endorsed-distros.md)）。 下列文章會引導您如何準備 Azure 都支援各種 Linux 散佈︰

- **[CentOS 型分配](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[紅色的角色企業 Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 與 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[其他-非背書散佈](virtual-machines-linux-create-upload-generic.md)**

請參閱**[Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**一般準備 Azure Linux 圖像的秘訣。

> [AZURE.NOTE] [Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)適用於 Vm endorsed 散佈其中使用[Linux Azure-Endorsed 散佈上的](virtual-machines-linux-endorsed-distros.md)中的 ' 支援版本] 底下所指定的設定詳細資料時，才執行 Linux。


## <a name="create-a-resource-group"></a>建立資源群組
邏輯資源群組在一起顯示所有 Azure 的資源，以支援您的虛擬機器，例如虛擬網路和儲存空間。 進一步瞭解[以下 Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 上傳您的自訂磁碟映像，建立 Vm 之前，您需要建立資源群組。 

下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`位置︰

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>建立儲存的帳戶
Vm 會儲存為網頁 blob 儲存體帳戶內。 進一步瞭解[Azure blob 儲存體](../storage/storage-introduction.md#blob-storage)。 您在建立您的自訂磁碟映像和 Vm 儲存帳戶。 您可建立自訂的磁碟映像任何 Vm 必須在同一個儲存帳戶為圖像。

下列範例會建立名為儲存帳戶`mystorageaccount`先前建立的 [資源] 群組中︰

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>清單中儲存的帳戶金鑰
Azure 產生針對每個儲存帳戶的兩個 512 位元便捷鍵。 這些便捷鍵會用於驗證儲存帳戶，例如時執行寫入作業。 閱讀更多有關[管理存取以下儲存空間](../storage/storage-create-storage-account.md#manage-your-storage-account)。 您可以檢視與便捷鍵`azure storage account keys list`] 命令。

檢視便捷鍵，為您建立的儲存空間帳戶︰

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

輸出非常類似︰

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
請記下`key1`當您將使用它使用您儲存的帳戶中的下一個步驟。

## <a name="create-a-storage-container"></a>建立存放容器
在您建立不同的目錄，組織您的本機檔案系統的相同方式，您可以建立容器內的儲存空間帳戶，來組織您的虛擬磁碟和圖像。 儲存帳戶可以包含容器的任何數字。 

下列範例會建立名為容器`myimages`，指定便捷鍵取得在先前的步驟 (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>上傳 VHD
現在您可以實際上傳您自訂的磁碟映像。 為所有 Vm 所使用的虛擬磁碟您上傳並將您的自訂磁碟映像儲存為網頁 blob。

指定您便捷鍵，您在上一個步驟，然後按一下 [自訂磁碟映像的路徑中建立您的本機電腦的容器︰

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>建立 VM 從自訂圖像
當您從您的自訂磁碟映像建立 Vm 時，指定要磁碟映像的 URI。 請確認目的地儲存帳戶相符項目儲存您的自訂磁碟映像的位置。 您可以建立使用 Azure CLI 或資源管理員 JSON 範本您 VM。


### <a name="create-a-vm-using-the-azure-cli"></a>建立使用 Azure CLI VM
您指定`--image-urn`參數與`azure vm create`指向您的自訂磁碟映像的命令。 確定`--storage-account-name`符合儲存空間帳戶儲存您的自訂磁碟映像的位置。 您沒有儲存您的 Vm 作為自訂磁碟映像的相同的容器。 請確定在先前的步驟相同的方式來建立任何其他容器上, 傳您的自訂磁碟映像之前。

下列範例會建立名為 VM`myVM`從您的自訂磁碟映像︰

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

您仍需要指定，或回應提示所需的所有其他參數的`azure vm create`命令，例如虛擬網路、 的公用 IP 位址、 使用者名稱和 SSH 鍵。 進一步瞭解[可用的 CLI 資源管理員參數](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)。

### <a name="create-a-vm-using-a-json-template"></a>建立使用 JSON 範本 VM
Azure 資源管理員範本是定義您想要建立的環境的 JavaScript 物件標記法 (JSON) 檔案。 範本會分解不同的資源提供者，例如計算或網路。 您可以使用現有的範本或寫下您自己。 進一步瞭解[使用資源管理員和範本](../azure-resource-manager/resource-group-overview.md)。

內`Microsoft.Compute/virtualMachines`範本的提供者，必須`storageProfile`節點，其中包含您 VM 設定的詳細資訊。 若要編輯的兩個主要的參數是`image`和`vhd`指向您的自訂磁碟映像與您的新 VM 虛擬磁碟的 Uri。 以下顯示 [使用自訂的磁碟映像的 JSON 的範例︰

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

您可以使用[這個現有的範本來建立 VM 從自訂圖像](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)，或進一步瞭解[建立您自己的 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 

一旦您有設定範本時，您會建立您使用的 Vm `azure group deployment create` ] 命令。 指定使用 JSON 範本的 URI`--template-uri`參數︰

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

如果您有 JSON 檔案儲存在本機電腦上，您可以使用`--template-file`參數改為︰

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>後續步驟
您已準備好上, 傳您自訂的虛擬磁碟之後，您可以閱讀更多關於[使用資源管理員和範本](../azure-resource-manager/resource-group-overview.md)。 您可能也要將[資料磁碟](virtual-machines-linux-add-disk.md)您的新 Vm。 如果您有您要存取您 Vm 上執行應用程式，請務必[開啟連接埠和結束點](virtual-machines-linux-nsg-quickstart.md)。