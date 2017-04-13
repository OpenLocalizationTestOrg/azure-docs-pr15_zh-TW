<properties
    pageTitle="Azure 中建立一份特定 VM |Microsoft Azure"
    description="瞭解如何建立特殊的 Windows VM Azure 中執行資源管理員部署模型中的複本。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>建立特殊的 Windows VM Azure 中執行的複本 

本文將示範如何使用 AZCopy 工具來建立 VHD 複本從特殊的 Windows VM Azure 中執行。 VHD 複本然後可用來建立新的 VM。 

- 如果想要複製一般化的 VM，請參閱[如何建立從現有的一般化 Azure VM VM 圖像](virtual-machines-windows-capture-image.md)。

- 如果您想要上傳 VHD 從內部部署 VM，使用 HYPER-V[上, 傳至 Azure 內部部署 VM 從 Windows VHD](virtual-machines-windows-upload-image.md)，請參閱建立如下。


## <a name="before-you-begin"></a>開始之前

請確定您︰

- 有**來源與目的地儲存帳戶**的相關資訊。 來源 VM，您需要儲存帳戶和容器名稱。 通常容器名稱會**vhd**。 您也需要擁有目的地儲存帳戶。 如果您已經沒有帳戶，您可以建立一個使用入口網站 (**其他服務**> 儲存帳戶 > 新增)，或使用[新 AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx)指令程式。 

- 有 Azure [PowerShell 1.0](../powershell-install-configure.md) （或更新版本） 安裝。

- 有下載並安裝[AzCopy 工具](../storage/storage-use-azcopy.md)。 


## <a name="deallocate-the-vm"></a>解除 VM

解除 VM 釋放複製 VHD。 

- **入口網站**︰ 按一下 [**虛擬機器** > **myVM** > 停止
- **Powershell**:`Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM`解除配置 VM 名為**myVM**資源群組**myResourceGroup**中。

**停止 （解除配置）**從**停止**變更**狀態**vm Azure 入口網站中。


## <a name="get-the-storage-account-urls"></a>取得儲存帳戶 Url

您需要的來源與目的地儲存帳戶的 Url。 Url 看起來像︰ `https://<storageaccount>.blob.core.windows.net/<containerName>/`。 如果您已經知道儲存體帳戶與容器名稱，您就可以取代建立您的 URL 括弧之間的資訊。 

您可以使用 Azure Powershell 的 Azure 入口網站以取得 URL:

- **入口網站**︰ 按一下 [**更多服務** > **儲存帳戶** >  <storage account> **二進位大型物件**和您的來源 VHD 檔案可能是**vhd**容器中。 按一下 [**屬性**容器，然後複製標示為**URL**的文字。 您必須在來源與目的地容器的 Url。 

- **Powershell**:`Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"`的 VM 名為**myVM**資源群組**myResourceGroup**中取得的資訊。 在結果]，查看**儲存空間的設定檔**區段**Vhd Uri**。 Uri 的第一個部分容器的 URL，而最後的部分會 vm OS VHD 名稱。

## <a name="get-the-storage-access-keys"></a>取得儲存便捷鍵

尋找來源與目的地儲存帳戶的便捷鍵。 如需有關便捷鍵的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

- **入口網站**︰ 按一下 [**更多服務** > **儲存帳戶** >  <storage account> **所有設定** > **便捷鍵**。 複製 [標示為**key1**] 鍵。
- **Powershell**:`Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup`資源群組**myResourceGroup**中取得儲存帳戶**mystorageaccount**儲存索引鍵。 複製 [標示為**key1**] 鍵。


## <a name="copy-the-vhd"></a>複製 VHD 

您可以複製檔案儲存空間帳戶使用 AzCopy 之間。 目的地容器，如果不存在指定的容器，它將會建立適合您。 

若要使用 AzCopy，開啟您的本機電腦上的命令提示字元，然後瀏覽至 AzCopy 安裝所在的資料夾。 它會是*C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*類似。 

若要複製所有容器內的檔案，您可以使用**/S**參數。 這可以用於複製 OS VHD 和所有資料磁碟，如果有相同的容器中。 此範例會示範如何複製的所有檔案中的儲存空間帳戶**mysourcestorageaccount**容器**mysourcecontainer**容器**mydestinationcontainer** **mydestinationstorageaccount**儲存帳戶。 使用您自己取代儲存帳戶和容器的名稱。 取代`<sourceStorageAccountKey1>`和`<destinationStorageAccountKey1>`使用您自己的索引鍵。

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

如果您只想要複製容器中的特定 VHD 使用多個檔案，您也可以指定使用 /Pattern 切換的檔案名稱。 在此範例中，將複製只將名稱為**myFileName.vhd**的檔案。

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


完成後，您會收到一則訊息，看起來像︰

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>疑難排解

- 當您使用 AZCopy，如果您看到錯誤 「 無法驗證要求的伺服器。 請確定授權標頭的值挑正確包括簽章。 」 與您使用的金鑰 2 或 [次要儲存體] 鍵，嘗試使用 [主要或 1 的儲存空間] 鍵。


## <a name="next-steps"></a>後續步驟

- 您可以建立新的 VM[附加為 OS 磁碟 vm VHD 複本](virtual-machines-windows-create-vm-specialized.md)。












