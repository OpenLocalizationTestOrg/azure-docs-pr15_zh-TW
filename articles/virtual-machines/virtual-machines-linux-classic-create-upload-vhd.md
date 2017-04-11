<properties
    pageTitle="建立和上傳 Linux VHD |Microsoft Azure"
    description="建立並使用傳統的部署模型包含 Linux 作業系統上傳 Azure 虛擬硬碟 (VHD)。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>建立及上傳虛擬硬碟包含 Linux 作業系統

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]您也可以[上傳自訂磁碟映像使用 Azure 資源管理員](virtual-machines-linux-upload-vhd.md)。

本文將示範如何建立和上傳虛擬硬碟 (VHD)，因此您可以將其作為您自己的圖像建立 Azure 虛擬機器。 瞭解如何準備作業系統，您可以使用它來建立多個根據圖像的虛擬機器。 

>  [AZURE.NOTE] 如果您有幾分鐘的時間，請協助我們改進 Azure Linux VM 文件進行[快速問卷](https://aka.ms/linuxdocsurvey)的體驗。 每個答案可幫助幫助您完成工作。

## <a name="prerequisites"></a>必要條件
本文假設您有下列項目︰

- **安裝.vhd 檔案中的 Linux 作業系統**-您已安裝[Azure 背書 Linux 分配](virtual-machines-linux-endorsed-distros.md)（或看到[資訊非背書散佈](virtual-machines-linux-create-upload-generic.md)） 虛擬磁碟 VHD 格式。 若要建立的 VM 和 VHD 有多個的工具︰
    - 安裝和設定[QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU)或[KVM](http://www.linux-kvm.org/page/RunningKVM)，小心 VHD 作為影像格式。 如有需要您可以[將影像轉換](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)使用`qemu-img convert`。
    - 您也可以使用 HYPER-V[在 Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install)或[Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)。

> [AZURE.NOTE] Azure 中不支援較新的 VHDX 格式。 當您建立 VM 時，指定 VHD 成的格式。 如有需要您可以將 VHDX 磁碟轉換成 VHD 使用[`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)或[`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx)PowerShell 指令程式。 此外，Azure 不支援，因此您需要將此類磁碟轉換成靜態 Vhd 上, 傳之前上傳動態 Vhd。 您可以使用工具，例如[Azure VHD 公用程式移](https://github.com/Microsoft/azure-vhd-utils-for-go)至轉換動態磁碟上傳到 Azure 的程序期間。

- **Azure 命令列介面**-安裝最新[Azure 命令列介面](../virtual-machines-command-line-tools.md)上傳 VHD。

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>步驟 1︰ 準備上傳圖像

Azure 支援各種不同的 Linux 分配 （請參閱[背書散佈](virtual-machines-linux-endorsed-distros.md)）。 下列文章會引導您如何準備 Azure 都支援各種 Linux 散佈。 完成下列指南中的步驟之後，回來後您已經準備好要上傳至 Azure VHD 檔案︰

- **[CentOS 型分配](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[紅色的角色企業 Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 與 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[其他-非背書散佈](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] Azure 平台虛擬機器執行 Linux OS，其中一個 endorsed 散佈使用[Linux Azure-Endorsed 散佈上的](virtual-machines-linux-endorsed-distros.md)中的 ' 支援版本] 底下所指定的設定詳細資料時，只適用於 SLA。 Azure 圖像庫中的所有 Linux 散發都是背書分配，使用所需的設定。

請參閱**[Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**一般準備 Azure Linux 圖像的秘訣。


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>步驟 2︰ 準備 Azure 的連線

請確定您使用的 Azure CLI 傳統部署模型中 (`azure config mode asm`)，然後登入您的帳戶︰

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>步驟 3︰ 將圖像上傳至 Azure

您需要上傳 VHD 檔案時，要儲存的帳戶。 您可能可以選擇現有的儲存空間帳戶或[建立新](../storage/storage-create-storage-account.md)。

使用 Azure CLI，使用下列命令上, 傳圖像︰

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

在上一個範例中︰

- **BlobStorageURL**是您打算使用的儲存空間帳戶的 URL
- **YourImagesFolder**是 blob 儲存體中的容器您要儲存圖像的位置
- **VHDName**是出現在入口網站以識別虛擬硬碟中的標籤。
- **PathToVHDFile**是完整路徑和您的電腦上.vhd 檔案的名稱。

下列範例顯示完整的範例︰

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>步驟 4︰ 建立 VM 從圖像
您建立 VM 使用`azure vm create`一般 VM 為相同的方式。 指定您在上一個步驟中提供圖像的名稱。 在下列範例中，我們使用**UbuntuLTS**圖像指定的名稱，在先前的步驟︰

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

若要建立您自己的 Vm，提供您自己的使用者名稱 + 密碼、 位置、 DNS 名稱及圖像的名稱。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Azure CLI 參考 Azure 傳統部署模型](../virtual-machines-command-line-tools.md)。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
