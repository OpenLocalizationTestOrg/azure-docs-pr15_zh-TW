<properties 
   pageTitle="管理 Azure 自動化資料 |Microsoft Azure"
   description="本文包含 Azure 自動化環境管理多個主題。  目前包括資料保留資料以及備份 Azure 自動化損毀修復，在 Azure 自動化。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>管理 Azure 自動化資料

本文包含 Azure 自動化環境管理多個主題。

## <a name="data-retention"></a>資料保留資料

當您刪除 Azure 自動化中的資源時，但會保留稽核作業的 90 天之前永久移除。  您無法看到或在此期間內使用此資源。  此原則也適用於資源的自動化帳戶已刪除的工作。

Azure 自動化自動刪除，並將會永久移除 [早於 90 天的工作。

下表摘要列出其他資源的保留原則。

|資料|原則|
|:---|:---|
|帳戶|永久移除帳戶由使用者刪除後的 90 天。|
|資產|資產刪除的使用者後, 的 90 天或 90 天後的保留資產刪除使用者帳戶，將會永久移除。|
|模組|永久移除使用者，請刪除模組後的 90 天或保留模組刪除使用者帳戶後的 90 天。|
|Runbooks|90 天後由使用者，則刪除資源或 90 天後的保留資源刪除使用者帳戶，將會永久移除。|
|工作|已刪除及永久移除後的 90 天上次修改。 這可能是後工作完成、 停止或暫停。|
|節點設定/MOF 檔案| 舊節點設定會永久移除產生新的節點設定後的 90 天。|
|DSC 節點| 永久移除節點的註冊從使用中 Windows PowerShell 的 Azure 入口網站或[取消註冊 AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) cmdlet 的自動化帳戶後的 90 天。 節點也將會永久移除保留節點刪除使用者帳戶後的 90 天。 |
|節點報表| 永久移除該節點產生新的報表後的 90 天|

保留原則套用至所有使用者，且目前無法自訂。

## <a name="backing-up-azure-automation"></a>備份 Azure 自動化

當您刪除 Microsoft Azure 中的自動化帳戶時，會刪除帳戶中的所有物件，包括 runbooks、 模組、 設定、 設定、 工作和資產。 在刪除帳戶之後，就無法復原物件。  若要備份您的自動化帳戶的內容在刪除之前，您可以使用下列資訊。 

### <a name="runbooks"></a>Runbooks

您可以將您 runbooks 匯出指令碼檔案使用中 Windows PowerShell 的 Azure 管理入口網站或[取得 AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx)指令程式。  這些指令碼檔案可以匯入另一個自動化帳戶中[建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx)中所述。


### <a name="integration-modules"></a>整合模組

您無法從 Azure 自動化匯出整合模組。  您必須確定可用的自動化帳戶以外。

### <a name="assets"></a>資產

您無法從 Azure 自動化匯出[資產](https://msdn.microsoft.com/library/dn939988.aspx)。  使用 [Azure 管理入口網站，您必須注意變數、 認證、 憑證、 連線及排程詳細的資料。  您必須然後手動建立任何您匯入另一個自動化的 runbooks 所使用的資產。

您可以使用[Azure cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)來擷取的資產加密並儲存這些內容的詳細資料，以供日後參考或其他自動化帳戶中建立對等的資產。

您無法擷取加密的變數或使用 cmdlet 的認證的 [密碼] 欄位的值。  如果您不知道這些值，然後您可以從取回 runbook，使用[取得 AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx)及[取得 AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx)活動。

您無法從 Azure 自動化匯出憑證。  您必須確定有可用 Azure 以外的任何憑證。

### <a name="dsc-configurations"></a>DSC 設定

您可以將您的設定匯出指令碼檔案使用中 Windows PowerShell 的 Azure 管理入口網站或[匯出 AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx)指令程式。 可以匯入及其他自動化帳戶中使用這些設定。


##<a name="geo-replication-in-azure-automation"></a>在 Azure 自動化地理複寫

地理複寫，Azure 自動化帳戶中的標準備份到不同的地理區域的重複的資料。 設定您的帳戶時，您可以選擇的主要區域，然後在第二個區域會自動獲指派至該。 從主要的區域，所複製的次要資料會持續更新，若資料遺失。  

下表顯示可用的主要和次要區域配對。

|主要            |次要
| ---------------   |----------------
|美國中部南美洲   |北美美國中部
|美國東部 2          |美國中部
|西歐        |北美歐洲
|東南亞    |中式地址
|日本東亞         |日本西部

在不太事件中主要地區資料會遺失，Microsoft 嘗試復原。 如果主要的資料無法復原，接著執行地理容錯是受影響的客戶就會收到關於此透過其訂閱。

