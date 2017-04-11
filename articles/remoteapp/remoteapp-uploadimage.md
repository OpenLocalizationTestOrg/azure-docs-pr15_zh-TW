
<properties
    pageTitle="將自訂的圖像上傳的 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何用 Azure RemoteApp 上傳的自訂圖像"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Azure RemoteApp 用上傳的自訂圖像

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

現在您已建立的自訂範本圖像或有更新的變更，您需要上傳至您 Azure RemoteApp 圖像文件庫的圖像。 使用這些步驟。


## <a name="before-you-start"></a>在您開始之前

1.      請確認您的自訂圖像符合的[圖像需求](remoteapp-imagereqs.md)及[應用程式的需求](remoteapp-appreqs.md)。
2.      安裝[Azure PowerShell 模組](../powershell-install-configure.md)。

## <a name="step-by-step-on-how-to-upload-custom-image"></a>逐步執行的方式上傳自訂圖像

1.      開啟 Azure 管理入口網站，然後瀏覽至 RemoteApp 頁面。
2.      在 [**範本圖像**] 索引標籤上按一下 [**上傳**底部的頁面。
4.      輸入您的圖像的易記名稱，然後指定儲存帳戶位置。 請確定位置是您 RemoteApp 集合的相同位置或您想要用來建立一個位置。
5.      出現提示時，下載指令碼至您的本機電腦。
6.      在文字方塊中複製的命令參數，剪貼簿。
7.      開啟提高權限的 Windows PowerShell 視窗。
8.      提高權限的 Windows PowerShell 視窗中，瀏覽至相同的目錄下載指令碼的位置。
9.      貼上的 [複製] 命令，然後按**Enter**。

    上傳程序會開始並持續時間可能會取決於許多因素包括您的網路速度和圖像的大小

11.    如果您上傳不成功因網路中斷或項目，而想，您可以隨時恢復您開始上傳程序。 若要繼續傳執行一次使用相同的命令列的指令碼。

> [AZURE.WARNING] 永不修改的上傳指令碼。 已經實作特定檢查以確保圖像符合的圖像需求及應用程式的需求。

## <a name="common-problems"></a>常見的問題

- 請確定您使用 Windows PowerShell 不 PowerShell 的 Azure。 您需要安裝 Azure PowerShell 模組，因為某些模組中需要上傳程序時。
- 永遠不會改變 [指令碼，驗證有您方便時觀看。
- 如果 vhd 檔案上傳期間會鎖定，將檔案複製或一次將其移至新的位置，並嘗試上傳。 可能導致無法上傳一些 Windows 程序。  
