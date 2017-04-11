<properties
    pageTitle="建立根據 Azure VM Azure RemoteApp 圖像 |Microsoft Azure"
    description="瞭解如何開始使用 Azure 虛擬機器 Azure RemoteApp 建立圖像。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>建立根據 Azure 虛擬機器 Azure RemoteApp 圖像

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

您可以從 Azure 虛擬機器建立 Azure RemoteApp 影像 （保留在集合您共用的應用程式）。 您也可以選擇要使用我們新增至 Azure VM 圖像庫符合所有 Azure RemoteApp 圖像需求的虛擬機器圖像-您可以使用 VM 圖像作為起點的自己 VM，如果您想要。 只尋找文件庫中的 「 Windows Server 遠端桌面工作階段主機 」 圖像。

有兩個步驟建立您自己的圖像根據 Azure VM-建立圖像，然後上傳其從 Azure VM 文件庫至 Azure RemoteApp。

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>建立自訂圖像，根據 Azure VM

若要建立圖像根據 Azure VM 使用這些步驟。

1. 建立 Azure 虛擬機器。 從 Azure 虛擬機器圖像庫，您可以使用 「 Windows Server 遠端桌面工作階段主機 」 或 「 Windows Server 遠端桌面工作階段 host （主機) 與 Microsoft Office 365 ProPlus 」 的圖像。 此圖像符合所有 Azure RemoteApp 範本圖像的需求。

    如需詳細資訊，請參閱[建立 VM 執行 Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。

2. 連線至 VM 安裝並設定您想要透過 RemoteApp 共用的應用程式。 請務必執行您的應用程式所需的任何其他 Windows 設定。

    如需詳細資訊，請參閱[如何登入虛擬機器執行 Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md)。

3. 如果您使用其中一個 Windows Server 遠端桌面工作階段主機影像，有可確保您 VM 符合 RemoteApp 前 reqs.包含的驗證指令碼 若要執行指令碼，請在桌面上按兩下**ValidateRemoteAppImage** 。 確定所有錯誤報告的指令碼所都修正再繼續執行下一個步驟。

4. SYSPREP 一般化並擷取圖像。 如需相關指示，請參閱[如何擷取 Windows 虛擬機器使用另存為範本](../virtual-machines/virtual-machines-windows-classic-capture-image.md)。



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>匯入至 Azure RemoteApp 圖像的文件庫的圖像

若要將 Azure RemoteApp 匯入新的圖像，按照以下步驟︰

1. 在 [**範本圖像**] 索引標籤︰
    - 如果您不有任何現有的圖像，請按一下 [**上傳或匯入範本圖像**]。
    - 如果您原本就有一個以上的圖像，按一下 [**+**新增新的圖像。

2. 選取 [**匯入您的虛擬機器中的圖像**庫]，然後按一下 [**下一步**。

3. 在下一個頁面上，從清單中選取您的自訂圖像，然後確認您已追蹤您在建立圖像時，所列的步驟。 按一下 [**下一步**]。
4. 輸入新的 RemoteApp 圖像的名稱並選擇位置]，然後按一下核取記號，開始匯入程序。

> [AZURE.NOTE] 您可以從任何支援的 Azure 虛擬機器 Azure Azure RemoteApp 所支援的任何位置的 Azure 位置匯入圖像。 根據位置匯入需要 25 分鐘。

現在您已經準備好要建立新的集合，請[雲端](remoteapp-create-cloud-deployment.md)集合或[混合式部署](remoteapp-create-hybrid-deployment.md)，根據您的需求。
