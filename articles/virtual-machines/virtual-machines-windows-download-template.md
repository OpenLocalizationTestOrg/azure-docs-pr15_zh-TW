<properties
    pageTitle="從 Azure VM 建立 VM 圖像 |Microsoft Azure"
    description="瞭解如何從資源管理員部署模型中建立的現有 Azure VM 建立一般化的 VM 映像"
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
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Vm 下載範本

當您使用的入口網站或 PowerShell 的 Azure 中建立 VM 時，資源管理員範本會自動為您建立。 您可以使用這個範本，快速地重複部署。 範本包含的所有資源的 [資源] 群組中的相關資訊。 虛擬機器，這表示範本容器中的 [資源] 群組中，包括網路資源 VM 產生所建立的所有項目。

## <a name="download-the-template-using-the-portal"></a>下載使用入口網站範本

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 中心] 功能表上，選取**虛擬機器**。
3. 從清單中選取的虛擬機器。
5. 選取 [**自動化指令碼**]。
6. 選取 [**下載**]，然後將.zip 檔案儲存到本機電腦。
7. 開啟.zip 檔案，並擷取檔案的資料夾。 .Zip 檔案會包含︰
    
    - deploy.ps1
    - deploy.sh 
    - deployer.rb
    - DeploymentHelper.cs
    - parameters.json
    - template.json

.Json 檔案是範本。
    
## <a name="download-the-template-using-powershell"></a>使用 PowerShell 將範本下載

您也可以下載.json 範本檔案[匯出 AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx)指令程式。 您可以使用`-path`.json 檔案提供檔名和路徑的參數。 此範例會示範如何下載至本機電腦上的 [ **C:\users\public\downloads** ] 資料夾的**myResourceGroup**資源群組的範本。

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>後續步驟

若要進一步瞭解部署使用範本的資源，請參閱[資源管理員範本逐步解說](../resource-manager-template-walkthrough.md)。