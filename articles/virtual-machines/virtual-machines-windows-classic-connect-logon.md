<properties
    pageTitle="登入傳統的 Azure 虛擬機器 |Microsoft Azure"
    description="使用 Azure 傳統的入口網站登入建立使用傳統的部署模型 Windows 虛擬機器。"
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
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>登入 Windows 虛擬機器使用 Azure 傳統入口網站

在 Azure 傳統的入口網站中，您可以使用 [**連線**] 按鈕啟動遠端桌面工作階段並登入 Windows 虛擬機器。

您要連線至 Linux VM 嗎？ 了解[如何登入執行 Linux 虛擬機器](virtual-machines-linux-mac-create-ssh-keys.md)。

了解如何[執行這些步驟使用新的 Azure 入口網站](virtual-machines-windows-connect-logon.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>影片逐步解說

以下是在本教學課程的影片逐步解說的步驟。 也會涵蓋端點和公用及私人連接到 Windows VM Azure 中使用的連接埠。

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>連線至虛擬機器

1. Azure 傳統入口網站登入。

2. 按一下 [**虛擬機器**，然後再選取 [虛擬機器。

3. 在頁面底部的命令列，按一下 [**連線**]。

    ![登入的虛擬機器](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] 如果 [**連線**] 按鈕無法使用，請參閱本文結尾處的疑難排解秘訣。

## <a name="log-on-to-the-virtual-machine"></a>登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>後續步驟

-   如果 [**連線**] 按鈕未作用或您已有其他問題與遠端桌面連線，請嘗試重設設定。 虛擬機器儀表板的 [**快速一覽**，按一下 [**重設遠端設定**。
-   針對您的密碼的問題，請嘗試重設其。 虛擬機器儀表板的 [**快速一覽**，按一下 [**重設密碼**。

如果這些秘訣不搭配或不需要什麼，請參閱 ＜[疑難排解遠端桌面連線至 windows Azure 虛擬機器](virtual-machines-windows-troubleshoot-rdp-connection.md)。 本文會引導您透過診斷並解決常見問題。


