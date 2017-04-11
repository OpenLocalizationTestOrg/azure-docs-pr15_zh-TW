<properties
   pageTitle="Azure RemoteApp 搭配使用 PowerShell cmdlet |Microsoft Azure"
   description="瞭解如何使用 Windows PowerShell cmdlet Azure RemoteApp 中。"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Azure RemoteApp 搭配使用 Windows PowerShell cmdlet

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

 您可以使用 Azure RemoteApp PowerShell cmdlet 來管理與維護您的集合。 若要開始，請使用下列資訊。

## <a name="get-the-cmdlets"></a>取得 cmdlet 
-------------
第一次下載 Azure Powershell cmdlet[以下](http://go.microsoft.com/?linkid=9811175)RemoteApp 內所含的 cmdlet。 

請參閱[Azure RemoteApp 指令程式說明](https://msdn.microsoft.com/library/mt428031.aspx)。

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>設定 Azure cmdlet，以使用您的訂閱
------------------
讓您可以使用 cmdlet，針對您 Azure 的訂閱，請依照[本指南](../powershell-install-configure.md)。

您可以使用下列步驟，即可快速開始使用︰

1.  下載並安裝[Azure PowerShell cmdlet](http://go.microsoft.com/?linkid=9811175)。
2.  啟動 Microsoft Azure PowerShell。
3.  執行**新增 AzureAccount**驗證 Azure 訂閱。 出現提示時，輸入相同的使用者名稱和密碼您用來登入 Azure 入口網站。  
4.  執行**取得 AzureSubscription**清單與您的使用者帳戶相關聯的訂閱。 
5.  執行**選取 AzureSubscription**並指定的訂閱名稱或識別碼]，使用 PowerShell 主控台中。

恭喜您，您 Azure PowerShell 主控台已設定且可供使用。 請注意，您將需要 repeate 步驟 2 到 5 每次啟動 Azure PowerShell 主控台。  

## <a name="create-a-cloud-collection"></a>建立雲端集合
--------------------
很簡單，執行下列命令︰

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

上述的命令會自動發佈 Microsoft Office 365 應用程式 （Excel、 OneNote、 Outlook、 PowerPoint、 Visio 及 Word）。

集合建立可能需要 30 分鐘或更長的時間才能完成。 因此，此命令會傳回，如下所示，您可以使用追蹤識別碼︰


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

集合完畢後，您可以使用下列命令集合新增使用者︰

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

大功告成 ！ 使用者可以連線至使用找到 Azure RemoteApp 用戶端應用程式[以下](https://www.remoteapp.windowsazure.com/)。

## <a name="available-cmdlets"></a>可用的 cmdlet
有許多還有其他命令]，將引進了即將進行的文件︰

基本 RemoteApp 集合 cmdlet: 

- 新 AzureRemoteAppCollection
- 取得 AzureRemoteAppCollection
- 設定 AzureRemoteAppCollection
- 更新 AzureRemoteAppCollection
- 移除 AzureRemoteAppCollection
- 新增 AzureRemoteAppUser
- 取得 AzureRemoteAppUser
- 移除 AzureRemoteAppUser
- 取得 AzureRemoteAppSession
- 中斷連線 AzureRemoteAppSession
- 呼叫 AzureRemoteAppSessionLogoff
- 傳送 AzureRemoteAppSessionMessage
- 取得 AzureRemoteAppProgram
- 取得 AzureRemoteAppStartMenuProgram
- 發佈 AzureRemoteAppProgram
- 解除發佈 AzureRemoteAppProgram
- 取得 AzureRemoteAppCollectionUsageDetails
- 取得 AzureRemoteAppCollectionUsageSummary
- 取得 AzureRemoteAppPlan

RemoteApp 虛擬網路 cmdlet:

- 新 AzureRemoteAppVNet
- 取得 AzureRemoteAppVNet
- 設定 AzureRemoteAppVNet
- 移除 AzureRemoteAppVNet
- 取得 AzureRemoteAppVpnDevice
- 取得-AzureRemoteAppVpnDeviceConfigScript
- 重設 AzureRemoteAppVpnSharedKey

RemoteApp 範本圖像 cmdlet:

- 新 AzureRemoteAppTemplateImage
- 取得 AzureRemoteAppTemplateImage
- 重新命名 AzureRemoteAppTemplateImage
- 移除 AzureRemoteAppTemplateImage

其他 RemoteApp cmdlet:

- 取得 AzureRemoteAppLocation
- 取得 AzureRemoteAppWorkspace
- 設定 AzureRemoteAppWorkspace
- 取得 AzureRemoteAppOperationResult
 
