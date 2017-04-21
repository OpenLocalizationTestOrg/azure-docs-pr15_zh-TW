<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>使用 PowerShell 的 Azure [工作]

本文將示範如何將 [工作]，使用 [從 Azure 模組和 Azure 資源管理員模組的命令。 這是可協助您瞭解新的命令，以及將現有的指令碼移轉至新的命令。

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>必要︰ 安裝最新版的 Azure PowerShell

如果您尚未這麼做，至少有 [版本號碼] 上安裝新版 PowerShell 的 Azure 您的本機電腦。 如果您使用的舊版，就不會有本文所述的 Azure 資源管理員指令程式。 如需詳細資訊，請參閱︰
 
- 如需 PowerShell 的 Azure 設定指示[如何安裝和設定 PowerShell 的 Azure](install-configure-powershell.md) 。
- [使用 Windows PowerShell 與資源管理員](powershell-azure-resource-manager.md)for 使用資源管理員的基本概念。

> [AZURE.NOTE] 大部分的工作要求您使用系統管理員層級 PowerShell 的 Azure 命令提示字元。

## <a name="command-comparison"></a>命令比較

此 [表格 | 區段] 會顯示命令語法。

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

以下命令範例會使用下列變數︰

$FriendlyName 」<Describe value>」

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in Github or published to Sandbox.-->

服務管理 | 資源管理員
---|----
`syntax` | `syntax`


<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->
  
[有關的指令的簡短簡介句子。 如果沒有顯示，省略。 若使用這種方法，但管線，說明的]:

    [command string]

## <a name="script-examples"></a>指令碼範例

以下是範例使用 [cmdlet 名稱）] 的 [工作]。 包括命令的︰

- [簡短動詞，用途，具有、 是，等]
- [下一步簡短動詞] 

<!--include this statement if it uses variables that weren't introduced earlier-->包括下列變數︰

- [變數 1]
- [變數 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
    
    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"
    
    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4
    
    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching
    
    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>其他資源
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[管理可用性](virtual-machines-windows-manage-availability.md)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[虛擬機器文件](https://msdn.microsoft.com/library/azure/jj156003.aspx)

