
<properties
    pageTitle="Azure RemoteApp 圖像需求 |Microsoft Azure"
    description="瞭解如何建立圖像和 Azure RemoteApp 搭配使用的需求"
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



# <a name="requirements-for-azure-remoteapp-images"></a>Azure RemoteApp 圖像的需求

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 使用 Windows Server 2012 R2 圖像裝載所有您想要與您的使用者共用的程式。 若要建立自訂的圖像，您可以開始與現有的圖像或[建立新的範本](remoteapp-create-custom-image.md)。

> [AZURE.TIP] 您知道 Azure RemoteApp 訂閱可讓您存取，您可以用來建立您自己的範本圖像的 Azure VM 圖庫中的 Windows Server 2012 R2 圖像至嗎？ [查看](remoteapp-image-on-azurevm.md)。  


用於 Azure RemoteApp 可以上傳圖像的需求如下︰


- 自訂應用程式不將資料儲存在本機上的圖像。 這些影像無狀態，而且只能包含應用程式。
- 圖像不包含資料的可能會遺失。
- 圖像大小應該 Mb 的倍數。 如果您嘗試要上傳圖像的不是倍數，就無法上傳。
- 圖像大小必須 127 GB 或更小。
- 它必須 VHD 檔案 （VHDX 檔案目前不支援）。
- VHD 不能產生 2 虛擬機器。
- VHD 可以是固定大小或動態擴充。 動態擴充 VHD 建議，因為超過固定大小 VHD 檔案上傳至 Azure 時間。
- 必須使用母片開機記錄 (MBR) 分割樣式初始化磁碟。 不支援 GUID 分割表格 (GPT) 磁碟分割樣式。
- VHD 必須包含單一的 Windows Server 2012 R2 安裝。 它可以包含多個區，但只有一個包含的 Windows 安裝。
- 必須安裝遠端桌面工作階段 host （主機) (RDSH) 角色和 「 桌面體驗 」 功能。
- 遠端桌面連線代理人角色必須*不*會安裝。
- 加密檔案系統 (EFS) 必須先停用。
- 圖像必須使用參數 SYSPREPed **/oobe / 一般化 /shutdown** （請勿使用**/mode:vm**參數）。
- 上傳您從快照鏈結 VHD 不受支援。

如需有關建立 Azure RemoteApp 圖像，請參閱[建立 Azure RemoteApp 圖像](remoteapp-imageoptions.md)。
