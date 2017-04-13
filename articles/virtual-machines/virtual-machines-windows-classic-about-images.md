<properties
    pageTitle="Windows 虛擬機器圖像相關 |Microsoft Azure"
    description="深入了解如何圖像搭配使用的是 Windows Azure 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>關於 Windows 虛擬機器的圖像

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>使用圖像

您可以使用 PowerShell 的 Azure 模組 Azure 訂閱管理圖像。 您也可以使用 [Azure 傳統入口網站的幾項圖像的工作，但是命令列可讓您更多選項]。


-   **取得所有圖像**︰`Get-AzureVMImage`會傳回所有可用的影像中您目前的訂閱清單︰ 圖像以及 Azure 或合作夥伴提供。 這表示您可能會收到大型清單。 在下一個示範如何取得較短清單。
-   **取得圖像系列**︰`Get-AzureVMImage | select ImageFamily`顯示字串**ImageFamily**屬性取得圖像系列的清單。
-   **取得特定系列中的所有圖像**︰`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **尋找 VM 圖像**︰`Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName`運作的方式是篩選僅適用於 VM 圖像的 [DataDiskConfiguration] 屬性。 此範例中也會篩選僅標籤和圖像名稱輸出。
-   **儲存的通用的圖像**︰`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **儲存特定的圖像**︰`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] 如果您想要建立 VM 圖像，其中包含資料磁碟，以及作業系統磁碟需要 OSState 參數。 如果您不是使用參數，指令程式會建立 OS 圖像。 參數的值會指出是否一般化或專用的圖像，根據作業系統磁碟是否已準備好要重複使用。
-   **刪除圖像**︰`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>後續步驟

您也可以[建立使用傳統的入口網站的 Windows 電腦](virtual-machines-windows-classic-tutorial.md)

