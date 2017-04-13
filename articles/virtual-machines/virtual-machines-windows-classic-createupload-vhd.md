<properties
    pageTitle="建立和上傳使用 Powershell VM 圖像 |Microsoft Azure"
    description="瞭解如何建立和上傳的是通用的 Windows Server 圖像 (VHD) 使用傳統的部署模型和 Powershell 的 Azure。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>建立並上傳至 Azure 的 Windows Server VHD

本文將說明如何為虛擬硬碟 (VHD) 上傳一般化的 VM 圖像，讓您可以使用它來建立虛擬機器。 如需磁碟和 Vhd Microsoft Azure 中的詳細資訊，請參閱[關於磁碟及 Vhd 虛擬機器](virtual-machines-linux-about-disks-vhds.md)。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. 您也可以[上傳](virtual-machines-windows-upload-image.md)虛擬機器使用資源管理員模型。 

## <a name="prerequisites"></a>必要條件

本文假設您有︰

- **Azure 訂閱**-如果您沒有帳戶，可以[免費開啟 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)。

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** -您必須安裝並設定為使用您的訂閱的 Microsoft Azure PowerShell 模組。 

- **A .VHD 檔案**-支援的 Windows 作業系統儲存.vhd 檔案，並附加至虛擬機器。 請檢查 Sysprep 所支援 VHD 上執行的伺服器角色。 如需詳細資訊，請參閱[Sysprep 支援的伺服器角色](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

> [AZURE.IMPORTANT] Microsoft Azure 中不支援 VHDX 格式。 您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或[轉換 VHD 指令程式](http://technet.microsoft.com/library/hh848454.aspx)。 如需詳細資訊，請參閱此[blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。

## <a name="step-1-prep-the-vhd"></a>步驟 1︰ 準備 VHD 

您將 VHD 上傳至 Azure 之前，它必須使用 Sysprep 工具會一般化。 準備要做為圖像 VHD。 如需 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep: 簡介](http://technet.microsoft.com/library/bb457073.aspx)。 執行 Sysprep 之前，先備份 VM。

若要安裝作業系統虛擬機器，從完成下列程序︰

1. 登入的作業系統。

2. 以管理員身分開啟命令提示字元視窗。 將目錄變更**%windir%\system32\sysprep**，然後再執行`sysprep.exe`。

    ![開啟命令提示字元視窗](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  [**系統準備工具**] 對話方塊隨即出現。

    ![開始 Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  在**系統準備工具**中，選取**輸入系統全新體驗 (OOBE)** ，請確定已核取 [**一般化**。

5.  在 [**關閉選項**] 中，選取 [**關閉**]。

6.  按一下**[確定]**。

## <a name="step-2-create-a-storage-account-and-a-container"></a>步驟 2︰ 建立儲存帳戶與容器

您需要 Azure 中的儲存空間帳戶，因此您不必.vhd 檔案上傳的位置。 此步驟會說明如何建立帳戶，或是從現有的帳戶取得您所需要的資訊。 取代中的變數&lsaquo;括弧括住&rsaquo;使用您自己的資訊。

1. 登入

        Add-AzureAccount

1. 設定您的 Azure 訂閱。

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. 建立新的儲存空間帳戶。 儲存帳戶的名稱都必須是唯一的 3 24 個字元。 名稱可以是字母和數字的任何組合。 您也需要指定的位置，例如 「 東亞美國 」
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. 設定為預設的新儲存的帳戶。
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. 建立新的容器。

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>步驟 3:.vhd 檔案上傳

使用[新增 AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx)上傳 VHD。

您在上一個步驟中使用 PowerShell 的 Azure 視窗中，輸入以下命令，並取代中的變數&lsaquo;括弧括住&rsaquo;使用您自己的資訊。

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>步驟 4︰ 新增圖像至您的自訂圖像的清單

您可以使用 [[新增 AzureVMImage](https://msdn.microsoft.com/library/mt589167.aspx) cmdlet 來新增圖像至您的自訂圖像的清單。

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>後續步驟

您可以立即[建立自訂 VM](virtual-machines-windows-classic-createportal.md)使用您上傳的圖像。

