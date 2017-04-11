<properties
    pageTitle="使用 PowerShell 的 Azure 與 Azure 資源管理員來保護伺服器 Azure |Microsoft Azure"
    description="使用 PowerShell 和 Azure 資源管理員自動化 Azure Azure 網站復原伺服器的保護。"
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>內部部署 HYPER-V 虛擬機器和 Azure 之間複製使用 PowerShell 和 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell-資源管理員](site-recovery-deploy-with-powershell-resource-manager.md)
- [傳統的入口網站](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>概觀

Azure 網站復原佔業務連續性和損壞復原策略來協調複寫、 錯誤移轉及復原的虛擬機器中部署案例的數字。 部署案例的完整清單，請參閱[Azure 網站復原的概觀](site-recovery-overview.md)。

Azure PowerShell 是一個模組，可提供 cmdlet 來管理透過 Windows PowerShell 的 Azure。 可以使用兩種類型的模組︰ Azure 設定檔模組或 Azure 資源管理員模組。

網站復原 PowerShell cmdlet，用於 Azure PowerShell 的 Azure 資源管理員中，可協助您保護並復原您 Azure 中的伺服器。

本文將說明如何使用 Windows PowerShell 與 Azure 資源管理員部署網站復原設定，以及安排 Azure 伺服器保護功能。 本文所用的範例為您示範如何保護、 失敗，以及使用 PowerShell 的 Azure 與 Azure 資源管理員來復原上 HYPER-V 主機 Azure 虛擬機器。

> [AZURE.NOTE] 目前網站復原 PowerShell cmdlet 可讓您進行下列設定︰ 到另一個虛擬機器管理員網站、 Azure 虛擬機器管理員網站和 Azure HYPER-V 網站。

您不需要為專家 PowerShell 來使用本文中，但您需要瞭解的基本概念，例如模組、 cmdlet 和工作階段。 如需有關 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx)。

您也可以閱讀更多關於[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。

> [AZURE.NOTE] 屬於雲端解決方案提供者 (CSP) 程式的 Microsoft 合作夥伴可以設定及管理客戶的伺服器客戶分別 CSP 訂閱 （租用戶訂閱） 的保護。

## <a name="before-you-start"></a>在您開始之前

請確定您有下列先決條件位置︰

- [Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。 此外，您可以閱讀[Azure 網站復原管理員價格](https://azure.microsoft.com/pricing/details/site-recovery/)。
- Azure PowerShell 1.0。 此版本，以及如何將其安裝的相關資訊，請參閱[Azure PowerShell 1.0。](https://azure.microsoft.com/)
- [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/)和[AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/)模組。 您可以從[PowerShell 圖庫](https://www.powershellgallery.com/)中取得這些模組的最新版本

本文說明如何使用 Powershell 的 Azure 與 Azure 資源管理員來設定和管理您的伺服器的保護。 本文所用的範例為您示範如何保護虛擬機器，HYPER-V 主機，Azure 上執行。 下列先決條件專屬於此範例。 更完整的各種不同的網站修復案例的需求，請參閱相關的案例的文件。

- 執行 Windows Server 2012 R2 或 Microsoft HYPER-V Server 2012 R2 包含一或多個虛擬機器 HYPER-V 主機。
- HYPER-V 伺服器連線至網際網路，直接或透過 proxy。
- [虛擬機器先決條件](site-recovery-best-practices.md#virtual-machines)應符合您想要保護的虛擬機器。


## <a name="step-1-sign-in-to-your-azure-account"></a>步驟 1︰ 登入您的 Azure 帳戶


1. 開啟 PowerShell 主控台並執行此命令 Azure 帳戶登入。 指令程式會開啟一個網頁，會提示您輸入您的帳戶認證。

        Login-AzureRmAccount

    您也可以為參數，來包括您的帳戶認證的或者，`Login-AzureRmAccount`指令程式，使用`-Credential`參數。

    如果您是使用代表租用戶 CSP 合作夥伴，指定為租用戶，客戶使用其 tenantID 或租用戶的主要網域名稱。

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. 帳戶可以有多個訂閱，因此您應該將您想要使用的帳戶的訂閱相關聯。

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  確認您的訂閱已登錄修復服務及網站復原的 Azure 提供者使用，請使用下列命令︰

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    在這些命令輸出中，如果**RegistrationState**設定為 [**已註冊**，您可以繼續進行步驟 2。 如果沒有出現，您應該在您的訂閱註冊遺失的提供者。

    若要註冊 Azure 提供者網站修復與修復服務，請執行下列命令︰

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    驗證成功註冊提供者︰ 使用下列命令︰`Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`和`Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`。



## <a name="step-2-set-up-the-recovery-services-vault"></a>步驟 2︰ 設定修復服務保存庫

1. 建立 Azure 資源管理員資源群組中，您會建立保存庫，或使用現有的資源群組。 您可以使用下列命令，以建立新的資源群組︰

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    在 [$ResourceGroupName 變數會包含您想要建立資源群組的名稱及位置 $Geo 變數包含 Azure 區域來建立資源群組 （例如，「 巴西南部 」）。

    您也可以使用您的訂閱中取得資源群組清單`Get-AzureRmResourceGroup`指令程式。

2. 建立新的 Azure 修復服務保存庫如下所示︰

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    您可以使用來擷取的現有保存庫清單`Get-AzureRmRecoveryServicesVault`指令程式。

> [AZURE.NOTE] 如果您想要在建立使用傳統的入口網站或 Azure 服務管理 PowerShell 模組的網站復原保存庫上執行作業，您可以使用擷取的這類保存庫清單`Get-AzureRmSiteRecoveryVault`指令程式。 您應該建立新的修復服務保存庫所有新的作業。 將您先前建立的網站復原保存庫受到支援，但沒有最新功能。

## <a name="step-3-set-the-recovery-services-vault-context"></a>步驟 3︰ 設定修復服務保存庫內容

1.  執行下列命令以設定保存庫內容︰

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>步驟 4︰ 建立 HYPER-V 網站，並產生新的保存庫登錄機碼，網站。

1. 建立新的 HYPER-V 網站如下所示︰

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    這個 cmdlet 開始建立網站，網站復原工作，並傳回網站復原工作物件。 等到完成，並確認工作已順利完成的工作。

    您可以擷取工作物件，並使用取得 AzureRmSiteRecoveryJob cmdlet，藉此檢查目前狀態的工作。
2. 產生和下載網站，登錄機碼，如下所示︰

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    複製下載的鍵 HYPER-V 主機。 您需要金鑰來註冊 HYPER-V 主機的網站。

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>步驟 5︰ 安裝您 HYPER-V 主機上的 Azure 網站修復提供者和 Azure 復原服務代理程式

1. 從[Microsoft](https://aka.ms/downloaddra)下載最新版的提供者的安裝程式。

2. 執行安裝程式在您的 HYPER-V 主機和結尾的安裝繼續註冊步驟。

3. 出現提示時，提供下載的網站登錄機碼，並完成註冊網站的 HYPER-V 主機。

4. 確認 HYPER-V 主機網站註冊使用下列命令︰

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>步驟 6︰ 建立複寫原則，並將它與保護容器關聯

1. 建立複寫原則如下所示︰

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    檢查以確保複寫原則建立成功傳回的工作。

    >[AZURE.IMPORTANT] 指定的儲存空間帳戶應該在相同的 Azure 地區，為您修復服務保存庫中，而應有地理複寫啟用。
    >
    > - 如果指定的復原儲存帳戶類型 Azure 儲存體 （傳統） 的受保護的電腦的容錯移轉復原 Azure IaaS （傳統） 的電腦。
    > - 如果指定的復原儲存帳戶類型 Azure 儲存體 （Azure 資源管理員），受保護的電腦的容錯移轉復原電腦以 Azure IaaS （Azure 資源管理員）。

2. 取得保護容器對應至網站，，如下所示︰

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  啟動保護容器的關聯與複寫原則，如下所示︰

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    關聯作業，完成時，請等候，並確保已順利完成。

##<a name="step-7-enable-protection-for-virtual-machines"></a>步驟 7︰ 啟用保護虛擬機器

1. 取得保護實體對應至 VM 您想要保護，如下所示︰

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. 開始保護虛擬機器，，如下所示︰

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] 指定的儲存空間帳戶應該在相同的 Azure 地區，為您修復服務保存庫中，而應有地理複寫啟用。
    >
    > - 如果指定的復原儲存帳戶類型 Azure 儲存體 （傳統） 的受保護的電腦的容錯移轉復原 Azure IaaS （傳統） 的電腦。
    > - 如果指定的復原儲存帳戶類型 Azure 儲存體 （Azure 資源管理員），受保護的電腦的容錯移轉復原電腦以 Azure IaaS （Azure 資源管理員）。

    > 如果您要保護的 VM 有多個附加的磁碟，請使用*OSDiskName*參數指定作業系統磁碟。

3. 虛擬機器初始複寫之後達到受保護的狀態，請等候。 這可能需要一段時間，例如要複製的資料量和 Azure 的可用上游頻寬因素。 工作狀態和 StateDescription，則會在 VM 送達受保護的狀態時，如下所示，更新。

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. 更新復原屬性，例如虛擬記憶體角色大小和 Azure 虛擬機器的網路介面卡，以在容錯移轉時，將網路。

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>步驟 8︰ 執行測試容錯移轉

1. 執行測試容錯移轉工作，如下所示︰

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. 確認測試 VM 已經建立 Azure 中。 （測試容錯移轉作業暫停，Azure 中建立測試 VM 之後。 「 工作 」 完成清理時繼續工作，建立 artefacts 中下一個步驟所示。）

3. 完成測試容錯移轉時，如下所示︰

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>後續步驟

[閱讀更多](https://msdn.microsoft.com/library/azure/mt637930.aspx)關於 Azure 網站修復透過 Azure 資源管理員 PowerShell cmdlet。
