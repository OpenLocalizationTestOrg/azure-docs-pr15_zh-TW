<properties
    pageTitle="擷取圖像的 Azure Windows VM |Microsoft Azure"
    description="擷取圖像的與傳統部署模型建立 Azure Windows 虛擬機器中。"
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>擷取圖像的與傳統部署模型建立 Azure Windows 虛擬機器中。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型的詳細資訊，請參閱[建立複本 Windows VM 執行 Azure 中](virtual-machines-windows-vhd-copy.md)。


本文將示範如何擷取，您可以將其作為圖像建立其他虛擬機器執行 Windows Azure 虛擬機器。 此圖像包含作業系統磁碟及任何附加至虛擬機器中的資料磁碟。 不包括網路的設定，因此您需要這些設定，當您建立其他使用圖像的虛擬機器。

Azure 會儲存在**我的圖像**的圖像。 這是您已上傳任何圖像的儲存位置的相同位置。 如需圖像的詳細資訊，請參閱[關於虛擬機器中的圖像](virtual-machines-linux-classic-about-images.md)。

##<a name="before-you-begin"></a>開始之前##

這些步驟是假設您已已經建立 Azure 虛擬機器並設定作業系統，包括附加任何資料磁碟。 如果您還沒有尚未完成，請參閱這些指示︰

- [圖像中建立虛擬機器](virtual-machines-windows-classic-createportal.md)
- [如何將資料磁碟附加至虛擬機器](virtual-machines-windows-classic-attach-disk.md)
- 請確定 Sysprep 支援的伺服器角色。 如需詳細資訊，請參閱[Sysprep 支援的伺服器角色](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

> [AZURE.WARNING] 此程序會刪除原始的虛擬機器後擷取。 

之前 caputuring Azure 虛擬機器中的圖像，建議目標虛擬機器備份。 Azure 虛擬機器備份可以使用 Azure 備份。 如需詳細資訊，請參閱[備份 Azure 虛擬機器](../backup/backup-azure-vms.md)。 其他方案，可從通過認證的合作夥伴。 若要瞭解什麼是目前可用，搜尋 Azure Marketplace。


##<a name="capture-the-virtual-machine"></a>擷取虛擬機器

1. 在[Azure 傳統入口網站](http://manage.windowsazure.com)，**連線**到虛擬機器。 如需相關指示，請參閱[如何登入執行 Windows Server 的虛擬機器] []。

2.  以管理員身分開啟命令提示字元視窗。

3.  若要將目錄變更`%windir%\system32\sysprep`，然後再執行 sysprep.exe。

4.  [**系統準備工具**] 對話方塊隨即出現。 執行下列動作︰

    - 在**系統清理動作**] 下，選取 [**輸入系統的全新體驗 (OOBE)** ，請確定已核取 [**一般化**。 如需有關使用 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep: 簡介][]。

    - 在 [**關閉選項**] 中，選取 [**關閉**]。

    - 按一下**[確定]**。

    ![執行 Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep 關閉的虛擬機器，虛擬機器 Azure 傳統入口網站中的狀態變更為 [**已停止]**。

8.  在 Azure 傳統的入口網站中，按一下 [**虛擬機器**，選取您想要擷取的虛擬機器。

9.  在 [命令列中，按一下 [**擷取**]。

    ![擷取虛擬機器](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    **擷取虛擬機器**] 對話方塊隨即出現。

10. 在 [**影像名稱**] 輸入新的圖像的名稱。

11. Windows Server 圖像加入您的自訂圖像設定之前，必須歸納起來執行 Sysprep 中的上一個步驟的指示。 按一下 [**我有執行 Sysprep 虛擬機器上的**，表示您所做的。

12. 按一下擷取圖像的核取記號。 新的圖像現在可在**圖像**。

    ![成功的圖像擷取](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>後續步驟

圖像可以用來建立虛擬機器。 若要這麼做，您會建立虛擬機器使用**從樣式庫**功能表項目，然後選取您剛剛建立的圖像。 如需相關指示，請參閱[建立虛擬機器的圖像](virtual-machines-windows-classic-createportal.md)。



[如何登入執行 Windows Server 的虛擬機器]: virtual-machines-windows-classic-connect-logon.md
[如何使用 Sysprep︰ 簡介]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
