<properties
    pageTitle="部署使用 PowerShell 的 Azure 堆疊的範本 |Microsoft Azure"
    description="瞭解如何部署虛擬機器使用資源管理員範本與 PowerShell。"
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>部署使用 PowerShell 的 Azure 堆疊的範本

使用 PowerShell 來 Azure 堆疊 POC 部署 Azure 資源管理員範本。  資源管理員範本部署，並提供您的應用程式，在單一、 協調運算中的所有資源。

## <a name="run-azurerm-powershell-cmdlets"></a>執行 AzureRM PowerShell cmdlet

在此範例中，您執行指令碼部署 Azure 堆疊 POC 虛擬機器使用資源管理員範本。  在進行之前，請確定您有[安裝並設定 PowerShell](azure-stack-connect-powershell.md)  

在此範例範本中使用 VHD 是預設的服務商場圖像 （WindowsServer-2012年-R2-資料中心）。

1.  移至<http://aka.ms/AzureStackGitHub>，搜尋**101 簡單-windows vm**範本，並將其儲存到下列位置︰ c:\\範本\\azuredeploy-101-簡單-windows-vm.json。

2.  在 PowerShell 中執行下列部署指令碼。 取代您的使用者名稱和密碼*的使用者名稱*和*密碼*。 在後續的使用，遞增*$myNum*參數，若要避免覆寫您的部署中的值。

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  開啟 Azure 堆疊入口網站**瀏覽]**、 按一下**虛擬機器**，，然後按一下新虛擬機器 (*myDeployment001*) 的查詢。

## <a name="video-example-hybrid-virtual-machine-deployment"></a>視訊的範例︰ 混合式虛擬機器部署

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>後續步驟

[以 Visual Studio 中部署範本](azure-stack-deploy-template-visual-studio.md)
