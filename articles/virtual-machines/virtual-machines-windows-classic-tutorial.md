<properties
    pageTitle="在 [傳統] 入口網站中建立 VM |Microsoft Azure"
    description="建立 Windows 虛擬機器中 Azure 傳統入口網站。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>建立虛擬機器執行 Windows Azure 傳統入口網站中

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](virtual-machines-windows-classic-tutorial.md)
- [PowerShell︰ 傳統部署](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟，使用資源管理員部署模型](virtual-machines-windows-hero-tutorial.md)使用**新的 Azure 入口網站**。 

本教學課程教您如何建立是 Azure 的虛擬機器 (VM) 執行 Windows Azure 傳統入口網站中。 我們將使用的 Windows Server 圖像作為範例，但這只是下列其中一項 Azure 提供許多圖像。 請注意您圖像選項取決於您的訂閱。 例如，Windows 桌面圖像可能 MSDN 訂閱者可以使用。

本節說明如何建立虛擬機器 Azure 傳統入口網站中使用 [**從圖庫**] 選項。 這個選項會提供更多設定選擇比**快速建立**選項。 例如，如果您想要加入虛擬機器虛擬網路，您會需要使用**從樣式庫**] 選項。

您也可以建立 Vm 使用[您自己的影像](virtual-machines-windows-classic-createupload-vhd.md)。 若要瞭解這和其他方法，請參閱[建立 Windows 虛擬機器不同方式](virtual-machines-windows-creation-choices.md)。



## <a name="video-walkthrough"></a>視訊的逐步解說

以下是本教學課程的逐步解說。

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"></a>建立虛擬機器

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>後續步驟

- 瞭解如何[建立使用資源管理員部署模型 VM](virtual-machines-windows-hero-tutorial.md)新的 Azure 入口網站。 

- 登入虛擬機器。 如需相關指示，請參閱[登入執行 Windows Server 的虛擬機器](virtual-machines-windows-classic-connect-logon.md)。

- 附加] 以儲存資料的磁碟。 您可以附加同時空白及磁碟包含資料。 如需相關指示，請參閱[附加到 Windows 虛擬機器建立傳統的部署模型資料磁碟](virtual-machines-windows-classic-attach-disk.md)。
