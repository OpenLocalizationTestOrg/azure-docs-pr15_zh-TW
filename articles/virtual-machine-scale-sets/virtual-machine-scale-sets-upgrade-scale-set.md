<properties
    pageTitle="部署虛擬機器比例集應用程式 |Microsoft Azure"
    description="部署上虛擬機器縮放比例設定應用程式"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>升級虛擬機器縮放設定

本文將說明如何您可以復原 OS 更新沒有任何停機時間設定 Azure 虛擬機器小數位數。 在此情況下，OS 更新會包括變更版本或作業系統的 SKU，或變更自訂影像 URI。 更新不停機時間所代表的意義而不一次更新虛擬機器一次或群組 （例如，一次一個錯誤網域）。 如此一來，可以繼續執行任何未升級的虛擬機器。

若要避免混淆，讓我們來辨別 OS 更新，您可能會想要執行的三種︰

- 變更版本或 SKU 的平台圖像。 例如，變更 Ubuntu 14.04.201507060，從 14.04.201506100 14.04.2-LTS 版本或變更 16.04.0-LTS/latest Ubuntu 15.10/最新 SKU。 本文討論這種情況。

- 變更您指向的自訂圖像的新版本的 URI 內建 (**屬性** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **圖像** > **uri**)。 本文討論這種情況。

- 修補虛擬機器中的作業系統 （此範例會包含安全性更新程式安裝及執行 Windows Update）。 這種情況下會支援，但不是包含在本文中。

本文涵蓋的支援的需求有前兩個選項。 您需要建立新的比例設定為執行第三個選項。

[Azure 服務布料的轉印圖樣](https://azure.microsoft.com/services/service-fabric/)叢集的組件與部署虛擬機器縮放比例設定不包含在此處。

變更 OS 版本/SKU 的平台圖像或自訂影像 URI 的基本順序如下所示︰

1. 取得虛擬機器縮放比例設定模型。

2. 變更版本、 SKU 或 URI 模型中的值。

3. 更新模型。

4. 在縮放比例設定虛擬機器上*manualUpgrade*呼叫。 此步驟內容才有*upgradePolicy*集的縮放比例設定為**手動**。 如果設定為 [**自動]**，所有虛擬機器就會都升級，因此造成停機時間。


注意此背景資訊，讓我們來看您可以更新的版本設定在 PowerShell，以及使用 REST API 的比例的方式。 這些範例封面的平台影像的大小寫，但這篇文章提供資訊不足，您可以調整此程序，自訂的圖像。

## <a name="powershell"></a>PowerShell##

此範例中就會更新設定 4.0.20160229 的新版本的 Windows 虛擬機器比例。 在更新之後模型，它會一次會更新虛擬機器執行個體之一。

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

如果您要更新的 URI，而不必變更平台圖像版本的自訂圖像，取代 「 設定新的版本 」 行結果如下所示︰

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>REST API

以下是幾個使用 Azure REST API 推行 OS 版本更新的 Python 範例。 同時使用輕量型[azurerm](https://pypi.python.org/pypi/azurerm)文件庫中的 Azure REST API 包裝紙函數執行縮放比例設定模型，後面接著與更新模型放入取得。 他們也查看虛擬機器執行個體檢視，以識別虛擬機器更新的網域。

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools)是 Python 指令碼，可用來執行的虛擬機器縮放推行 OS 升級設定一個更新的網域一次。

![選擇虛擬機器或更新的網域 Vmssupgrade 指令碼](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

這個指令碼可讓您選擇特定的虛擬機器更新或指定更新的網域。 支援變更平台圖像版本，或變更自訂影像 URI。

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard)是虛擬機器縮放比例設定虛擬機器狀態顯示為 heatmap 其中一列代表一個更新網域用途編輯器。 除此之外，您可以與新的版本、 SKU 或自訂圖像 URI，更新縮放比例設定的模型，然後選擇 [升級故障網域。 當您這麼做時，所有虛擬機器中的更新的網域就會都升級至新的模型。 或者，您可以執行輪流升級，根據您所選擇的批次大小。  

下圖顯示的小數位數為 Ubuntu 14.04 2LTS 版本 14.04.201507060 的模型。 由於此螢幕擷取畫面拍攝許多更多選項已新增至這項工具。

![小數位數為 Ubuntu 14.04 2LTS 的 Vmsseditor 模型](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

按一下 [**升級**，然後**取得詳細資料**之後，在 「 ud 」 0 的虛擬機器開始更新。

![Vmsseditor 顯示更新進行中](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
