<properties
 pageTitle="管理 HPC 套件叢集運算節點 |Microsoft Azure"
 description="瞭解如何新增、 移除、 啟動和停止 HPC 套件叢集運算節點 Azure 中的 PowerShell 指令碼工具"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>管理的數字與運算叢集的節點 HPC 套件 Azure 中的可用性

如果您建立 HPC 套件叢集 Azure Vm 中時，您可以輕鬆地新增、 移除、 啟動 （提供），或停止叢集 (deprovision) 計算的數字節點 Vm 的方法。 若要執行這些工作，請執行標頭節點 VM 上所安裝的 Azure PowerShell 指令碼。 這些指令碼可協助您控制的數字和 HPC 套件叢集資源可用性，讓您控制成本。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>必要條件

* **在 Azure Vm HPC 套件叢集**-傳統部署模型中建立 HPC 套件叢集至少使用 HPC 套件 2012 R2 更新 1。 例如，您可以自動化部署使用 Azure Marketplace 和 Azure PowerShell 指令碼目前 HPC 套件 VM 圖像。 資訊及必要條件，請參閱[建立 HPC 套件 IaaS 部署指令碼 HPC 叢集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。

    部署之後，尋找節點管理指令碼 %ccp\_在常用 %bin 資料夾。 您必須以系統管理員身分執行每個指令碼。

* **Azure 發佈設定檔案或管理認證**-您需要執行在下列其中一項︰

    * [**匯入 Azure 發佈的設定檔**]。 若要這麼做，請執行下列 cmdlet，PowerShell 的 Azure 在︰

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **設定在 Azure 管理憑證**。 如果您已經.cer 檔案，匯入的 CurrentUser\My 憑證存放區，然後執行下列 Azure PowerShell cmdlet Azure 環境 （AzureCloud 或 AzureChinaCloud）︰

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>新增運算節點 Vm

新增計算與**新增 HpcIaaSNode.ps1**指令碼的節點。

### <a name="syntax"></a>語法
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>參數

* **ServiceName**名稱雲端服務 Vm 便會新增至該新運算節點。

* **ImageName** -Azure VM 圖像名稱，您可以透過 Azure 傳統入口網站或 Azure PowerShell 指令程式**取得 AzureVMImage**取得。 圖像必須符合下列需求︰

    1. 必須安裝 Windows 作業系統。

    2. 必須安裝 HPC 套件計算節點角色。

    3. 圖像必須是在 [使用者] 類別中，不公用 Azure VM 圖像的私人圖像。

* **數量**-運算節點 Vm 要新增的數字。

* **InstanceSize** -運算節點 Vm 的大小。

* **DomainUserName** -網域使用者名稱，將會用於新 Vm 加入網域。

* **DomainUserPassword** -網域使用者的密碼。

* **NodeNameSeries**（選擇性）-運算節點命名圖樣。 格式必須是&lt;*根\_名稱*&gt;&lt;*開始\_數字*&gt;%。 例如，MyCN %10%所代表的意義一系列運算節點的名稱從 MyCN11 開始。 如果未指定的指令碼會使用命名數列 HPC 叢集設定的節點。

### <a name="example"></a>範例

下列範例會在雲端服務*hpcservice1*，根據 VM 圖像*hpccnimage1*新增 20 大小大型運算節點 Vm。

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>移除運算節點 Vm

移除與**移除 HpcIaaSNode.ps1**指令碼運算節點。

### <a name="syntax"></a>語法

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>參數

* **名稱**-叢集節點要移除的名稱。 使用萬用字元。 參數設定名稱就是名稱。 您無法指定 [**名稱**] 和 [**節點**的參數。

* **節點**-HpcNode 物件節點中移除，您可以透過 HPC PowerShell 指令程式[取得 HpcNode](https://technet.microsoft.com/library/dn887927.aspx)取得。 參數設定名稱是節點。 您無法指定 [**名稱**] 和 [**節點**的參數。

* **DeleteVHD**（選擇性）-若要刪除的相關聯的磁碟 Vm 所移除的設定。

* **強制**（選擇性）-若要強制離線 HPC 節點才能移除這些設定。

* **確認**（選擇性）-提示確認之前執行命令。

* **WhatIf** -設定來描述如果您執行此命令，而不實際執行] 命令，會發生什麼情況。

### <a name="example"></a>範例

下列範例會強制離線節點名稱開頭*HPCNode-CN-*並加以移除節點和其相關聯的磁碟。

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>開始運算節點 Vm

開始**開始 HpcIaaSNode.ps1**指令碼運算節點。

### <a name="syntax"></a>語法

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>參數

* **名稱**-啟動叢集節點的名稱。 使用萬用字元。 參數設定名稱就是名稱。 您無法指定 [**名稱**] 和 [**節點**的參數。

* **節點**-HpcNode 物件節點，開始使用時，您可以透過 HPC PowerShell 指令程式[取得 HpcNode](https://technet.microsoft.com/library/dn887927.aspx)取得。 參數設定名稱是節點。 您無法指定 [**名稱**] 和 [**節點**的參數。

### <a name="example"></a>範例

下列範例會將節點開頭名稱開頭*HPCNode-CN-*。

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>停止運算節點 Vm

停止**停駐點 HpcIaaSNode.ps1**指令碼運算節點。

### <a name="syntax"></a>語法

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>參數


* **名稱**-停止叢集節點的名稱。 使用萬用字元。 參數設定名稱就是名稱。 您無法指定 [**名稱**] 和 [**節點**的參數。

* **節點**-HpcNode 物件為節點停止，您可以透過 HPC PowerShell 指令程式[取得 HpcNode](https://technet.microsoft.com/library/dn887927.aspx)取得。 參數設定名稱是節點。 您無法指定 [**名稱**] 和 [**節點**的參數。

* **強制**（選擇性）-設定之前停止強制離線 HPC 節點。

### <a name="example"></a>範例

下列範例會強制離線節點名稱開頭*HPCNode-CN-*然後停止節點。

停止 HPCIaaSNode.ps1 – 名稱 HPCNodeCN-*-強制

## <a name="next-steps"></a>後續步驟

* 如果您想要自動放大或縮小叢集節點根據目前的工作負載的工作和叢集上的工作，請參閱[自動變大和縮小根據叢集工作量 Azure 中的 HPC 套件叢集資源](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)。
