<properties
    pageTitle="將光碟附加至 VM |Microsoft Azure"
    description="將資料磁碟附加至 Windows 虛擬機器建立傳統的部署模型，並將它初始化。"
    services="virtual-machines-windows, storage"
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
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>將資料磁碟附加至 Windows 虛擬機器建立傳統的部署模型

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]如果您想要使用新的入口網站，請參閱[如何將附加至 Windows VM Azure 入口網站中的資料磁碟](virtual-machines-windows-attach-disk-portal.md)。

如果您需要的其他資料磁碟，您可以附加 VM 空的磁碟或現有的資料磁碟。 在這兩種情況下，磁碟是.vhd 檔案位於 Azure 儲存體帳戶。 新的磁碟，若是附加磁碟之後, 您也需要，讓它使用 Windows VM 準備好將它初始化。

如需有關磁碟的詳細資訊，請參閱[關於磁碟及 Vhd 虛擬機器](virtual-machines-windows-about-disks-vhds.md)。


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>初始化磁碟

1. 連線至虛擬機器。 如需相關指示，請參閱[如何登入執行 Windows Server 的虛擬機器][logon]。

2. 虛擬機器登入之後，開啟 [**伺服器管理員**]。 在左窗格中，選取 [**檔案及儲存服務**。

    ![開啟的伺服器管理員](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. 展開功能表，然後選取**磁碟**。

4. [**磁碟**] 區段中列出的磁碟。 在大部分情況下，會有磁碟 0 與 1，磁碟磁碟 2。 磁碟 0 作業系統磁碟、 磁碟 1 是暫時磁碟，而磁碟 2 是您剛才附加至 VM 資料磁碟。 新的資料磁碟會列出分割為**未知**。 以滑鼠右鍵按一下磁碟，然後選取 [**初始化**。

5.  通知您，磁碟初始化時，會清除所有的資料。 按一下 [ **]**確認警告，並初始化磁碟。 完成之後，Partion 會列為**GPT**。 磁碟上按一下滑鼠右鍵，然後選取**新的音量**。

6.  完成精靈，使用預設值。 精靈完成時，[**區**] 區段會列出新的區。 磁碟現在線上並且可供儲存的資料。

    ![成功初始化的音量](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] VM 的大小會決定您可以將附加至該多少磁碟。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

## <a name="additional-resources"></a>其他資源

[如何卸離從 Windows 虛擬機器磁碟](virtual-machines-windows-classic-detach-disk.md)

[有關磁碟與 Vhd 虛擬機器](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
