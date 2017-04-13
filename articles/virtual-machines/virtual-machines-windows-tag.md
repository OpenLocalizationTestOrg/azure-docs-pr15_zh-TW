<properties
   pageTitle="如何標記 VM |Microsoft Azure"
   description="深入了解標記 Azure 使用資源管理員部署模型中建立 Windows 虛擬機器"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>若要標記 Windows 虛擬機器 Azure 中的方式


本文將說明在 Azure 中標記 Windows 虛擬機器，透過資源管理員部署模型的不同方式。 標籤都可以直接放置在資源或資源群組的使用者定義的金鑰/值組。 Azure 目前支援最多為 15 的標籤，每個資源與資源群組。 標記可能會放置在資源時建立或新增至現有的資源。 請注意標記的支援資源管理員部署模型僅透過建立的資源。 如果您想要標記 Linux 虛擬機器，請參閱[如何標記 Linux 中 Azure 虛擬機器](virtual-machines-linux-tag.md)。

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>使用 PowerShell 標記

若要建立，新增和刪除透過 PowerShell，您需要先設定您的[PowerShell 環境與 Azure 資源管理員][]的標籤。 當您完成設定之後時，您可以將計算、 網路和儲存空間的資源，建立，或透過 PowerShell 建立資源後的標籤。 本文將著重在虛擬機器上顯示的檢視/編輯標籤。

首先，請瀏覽至透過虛擬機器`Get-AzureRmVM`指令程式。

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

如果您的虛擬機器已經包含標籤，然後您會所有標籤都看到您的資源︰

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

如果您想要新增標籤透過 PowerShell，您可以使用`Set-AzureRmResource`] 命令。 請注意更新透過 PowerShell 標籤時，以標籤整個更新。 因此，如果您要新增一個標記資源已經有標籤，您必須包含您要放在資源的所有標籤。 以下是如何將其他標籤新增至透過 PowerShell Cmdlet 資源的範例。

此第一個 cmdlet 設定的全部標記置於*MyTestVM* *$tags*變數中，使用`Get-AzureRmResource`和`Tags`屬性。

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

[第二個] 命令會顯示指定變數的標籤。

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

第三個命令*$tags*變數新增額外的標籤。 請注意使用**+=**若要將新的金鑰/值組新增至*$tags*清單。

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

第四個命令設定所有指定資源*$tags*變數中定義的標記。 在此情況下，則 MyTestVM。

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

第五個] 命令會顯示資源上所有的標籤。 如您所見，*位置*現在定義為標籤與*MyLocation*做為的值。

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

若要進一步瞭解透過 PowerShell 標記，請參閱[Azure 資源 Cmdlet][]。

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>後續步驟

* 若要進一步瞭解標記 Azure 資源，請參閱[Azure 資源管理員概觀][]與[使用的標籤，來組織 Azure 資源][]。
* 若要查看標籤如何協助您管理資源 Azure 減少使用，請參閱[瞭解 Azure 帳單][]和[獲得深入見解到消耗 Microsoft Azure 資源][]。

[PowerShell 環境與 Azure 資源管理員]: ../powershell-azure-resource-manager.md
[Azure 資源 Cmdlet]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure 資源管理員的概觀]: ../azure-resource-manager/resource-group-overview.md
[使用標籤以組織 Azure 資源]: ../resource-group-using-tags.md
[了解 Azure 帳單]: ../billing/billing-understand-your-bill.md
[獲得深入見解到消耗 Microsoft Azure 資源]: ../billing-usage-rate-card-overview.md
