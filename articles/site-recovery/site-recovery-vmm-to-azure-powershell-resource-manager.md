<properties
    pageTitle="複製 HYPER-V 虛擬機器中使用 Azure 網站復原和 PowerShell （資源管理員） 的 VMM 雲朵 |Microsoft Azure"
    description="複製 HYPER-V 虛擬機器中使用 Azure 網站復原和 PowerShell VMM 雲朵"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>將 HYPER-V 虛擬機器中 VMM 雲朵複寫到 Azure 使用 PowerShell 和 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-azure.md)
- [PowerShell-資源管理員](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [傳統的入口網站](site-recovery-vmm-to-azure-classic.md)
- [PowerShell-傳統](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>概觀

Azure 網站復原佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器中部署案例的數字。 部署案例的完整清單，請參閱[Azure 網站復原的概觀](site-recovery-overview.md)。

本文將示範如何使用 PowerShell 來自動化您需要執行 Azure 網站復原設定 HYPER-V 虛擬機器中系統管理中心 VMM 雲朵複製至 Azure 儲存體時的常見工作。

本文包含先決條件案例中，並顯示

- 如何設定復原服務保存庫
- 在 [來源 VMM 伺服器上安裝 Azure 網站修復提供者
- 註冊保存庫伺服器、 新增 Azure 儲存體帳戶
- 在 HYPER-V 主機伺服器上安裝 Azure 修復服務代理程式
- 設定將會套用至所有受保護的虛擬機器 VMM 雲朵保護設定
- 啟用這些虛擬機器中的保護。
- 測試容錯移轉確認一切運作正常。

如果您遇到這種情況所設定的問題時，請[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼您的問題。

> [AZURE.NOTE] Azure 有兩種不同的部署模型建立及使用的資源︰[資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。 本文涵蓋使用資源管理員部署模型。

## <a name="before-you-start"></a>在您開始之前

請確定您有下列先決條件位置︰

### <a name="azure-prerequisites"></a>Azure 的先決條件

- 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 如果您沒有帳戶，開始使用[免費的帳戶](https://azure.microsoft.com/free)。 此外，您可以閱讀[Azure 網站復原管理員價格](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 如果您所試用複寫 CSP 訂閱情況，您將需要 CSP 訂閱。 進一步瞭解如何[加入 CSP 計畫](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx)CSP 程式。
- 您必須以儲存資料複製到 Azure Azure v2 儲存空間 （資源管理員） 帳戶。 帳戶必須啟用地理複寫。 它應該位於 Azure 網站修復服務，為相同的區域，並會與同一份訂閱或 CSP 訂閱相關聯。 若要深入瞭解設定 Azure 儲存空間，請參閱[Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)供參考。
- 您需要，請確定您要保護的虛擬機器遵守[Azure 虛擬機器的先決條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。

> [AZURE.NOTE] 目前，只 VM 層級的作業可能會出現透過 Powershell。 將會立即進行支援復原計劃層級的作業。  現在，您所執行失敗群組只能在 [受保護 VM 」 的資料點，而非復原計劃的層級。

### <a name="vmm-prerequisites"></a>VMM 先決條件
- 您必須在系統管理中心 2012 R2 上執行的 VMM 伺服器。
- 包含您要保護的虛擬機器任何 VMM 伺服器必須執行 Azure 網站修復提供者。 這被安裝期間 Azure 網站復原部署。
- 您必須至少有一個您要保護 VMM 伺服器上的雲端。 雲端應該包含︰
    - 一或多個 VMM 主機群組。
    - 一或多個 HYPER-V 主機伺服器或叢集的每個主機] 群組中。
    - 一或多個虛擬機器來源 HYPER-V 伺服器上。
- 深入瞭解設定 VMM 雲朵︰
    - 閱讀更多關於私人 VMM 雲朵在[系統管理中心 2012 R2 VMM 私人雲端的新增功能](http://go.microsoft.com/fwlink/?LinkId=324952)及[VMM 2012 和雲朵](http://go.microsoft.com/fwlink/?LinkId=324956)。
    - 深入了解[設定 VMM 雲端布料的轉印圖樣](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - 您的雲端布料的轉印圖樣項目已就緒後瞭解如何建立私人的雲朵建立[VMM 中的私人雲端](http://go.microsoft.com/fwlink/p/?LinkId=324953)以及在[逐步解說︰ 使用系統管理中心 2012 SP1 VMM 建立私人的雲朵](http://go.microsoft.com/fwlink/p/?LinkId=324954)。


### <a name="hyper-v-prerequisites"></a>HYPER-V 先決條件

- Host （主機） HYPER-V 伺服器必須至少執行**Windows Server 2012** HYPER-V 角色或**Microsoft HYPER-V Server 2012** ，安裝最新的更新。
- 如果您執行的 HYPER-V 叢集記事中的叢集代理人不會自動建立如果您有靜態 IP 位址基礎叢集。 您必須以手動方式設定叢集代理人。 為
- 如需相關指示請參閱[如何設定 HYPER-V 複本代理人](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx)。
- VMM 雲端必須包含任何 HYPER-V 主機伺服器或您要管理保護。

### <a name="network-mapping-prerequisites"></a>網路對應的先決條件
當您要保護虛擬機器中 Azure 虛擬機器網路來源 VMM 伺服器的對應網路地圖，並以啟用下列 Azure 網路為目標︰

- 在相同的網路上失敗的所有電腦可以都連線至彼此，它們是在不管修復您的方案資料。
- 如果網路閘道器的目標 Azure 網路上的設定，虛擬機器可以連線至其他內部部署虛擬機器中。
- 如果您沒有設定網路對應，只在相同的復原計劃容錯虛擬機器無法連線至其他容錯移轉至 Azure 之後。

如果您想要部署網路對應必須下列動作︰

- 您想要保護來源 VMM 伺服器上的虛擬機器應該連線到 VM 網路。 該網路應該邏輯網路雲端與相關聯的連結。
- 要複製的虛擬機器可以連線後會失敗暫留在上一個 Azure 網路。 您將會失敗暫留在上時選取這個網路。 網路應該 Azure 網站復原訂閱為相同的區域。

進一步瞭解網路中的對應

- [如何設定在 VMM 中的邏輯網路](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [如何設定 VMM VM 網路及閘道器](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [如何設定和監視 Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)


###<a name="powershell-prerequisites"></a>PowerShell 先決條件
請確定您已準備好 Azure PowerShell。 如果您已經在使用 PowerShell，您會需要升級至新版 0.8.10 或更新版本。 如需 PowerShell 設定的詳細資訊，請參閱[安裝及設定 PowerShell 的 Azure 指南](../powershell-install-configure.md)。 一旦您有安裝並設定為 PowerShell，您可以檢視所有服務的可用 cmdlet[以下](https://msdn.microsoft.com/library/dn850420.aspx)。

若要深入瞭解的秘訣可協助您使用的指令程式，例如參數值]、 輸入和輸出的通常是處理方式在 PowerShell 的 Azure，請參閱[Azure Cmdlet 快速入門指南](https://msdn.microsoft.com/library/azure/jj554332.aspx)。

## <a name="step-1-set-the-subscription"></a>步驟 1︰ 設定訂閱

1. 從 Azure powershell 的 Azure 帳戶登入︰ 使用下列 cmdlet

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred


2. 取得您的訂閱的清單。 這也會列出 subscriptionIDs 每個訂閱。 記下您要在其中建立復原服務保存庫的訂閱 subscriptionID

        Get-AzureRmSubscription

3. 設定復原服務保存庫可建立提及的訂閱識別碼的訂閱

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>步驟 2︰ 建立修復服務保存庫

1. 如果您沒有，建立資源群組 Azure 資源管理員中的商務連絡人

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. 新的修復服務保存庫中建立與儲存建立的 ASR 保存庫物件 （將會用於更新版本） 的變數。 您也可以擷取 ASR 保存庫物件文章建立使用取得 AzureRMRecoveryServicesVault 指令程式:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>步驟 3︰ 設定復原服務保存庫內容

1.  設定保存庫內容執行以下命令。

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>步驟 4︰ 安裝 Azure 網站修復提供者

1.  VMM 在電腦上，請執行下列命令以建立目錄︰

        New-Item c:\ASR -type directory

2.  擷取執行下列命令以使用下載的提供者的檔案

        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q


3.  安裝的提供者，請使用下列命令︰

        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    安裝完成，請等候。

4.  登錄伺服器中使用下列命令保存庫︰

        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-an-azure-storage-account"></a>步驟 5︰ 建立 Azure 儲存體帳戶

1. 如果您沒有安裝 Azure 儲存體帳戶，請為保存庫相同的地理中建立地理複寫啟用帳戶，藉由執行下列命令︰

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

請注意，儲存帳戶必須在同一個地區為 Azure 網站修復服務，會與同一份訂閱相關聯。

## <a name="step-6-install-the-azure-recovery-services-agent"></a>步驟 6︰ 安裝 Azure 復原服務代理程式

1. 下載[http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) Azure 修復服務代理程式 」，並將其安裝在您要保護 VMM 雲朵中每個 HYPER-V 主機伺服器上。

2. 在所有 VMM 主機上執行下列命令︰

    marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>步驟 7︰ 設定雲端保護設定

1.  建立複寫原則，Azure 以執行下列命令︰


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  取得保護容器藉由執行下列命令︰

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  取得原則的詳細資訊，此變數使用所建立的工作，並提及的易記的原則名稱︰

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  開始複寫原則保護容器的關聯︰

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  完成工作之後，請執行下列命令︰

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  工作已完成處理之後，請執行下列命令︰

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

若要查看完成作業，請依照下列[活動監視器](#monitor)] 中的步驟。

## <a name="step-8-configure-network-mapping"></a>步驟 8︰ 設定網路對應

在您開始之前的網路對應確認來源 VMM 伺服器上的虛擬機器連線到 VM 網路。 此外，建立一或多個 Azure 虛擬網路。

進一步瞭解如何建立虛擬網路中[建立與使用 Azure 資源管理員] 及 [PowerShell 網站-VPN 連線虛擬網路](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)使用 Azure 資源管理員與 PowerShell

請注意，多個虛擬機器網路對應到單一 Azure 網路。 如果目標網路都有多個子網路且這些子網路的其中一個子網路來源虛擬機器位於相同的名稱，然後複本虛擬機器會連線到目標該子之後容錯移轉。 如果不有任何名稱相符的目標子網路，請在虛擬機器會連線到網路中的第一個子網路。

1. 第一個命令會取得目前 Azure 網站復原保存庫中的伺服器。 命令會將 Microsoft Azure 網站復原伺服器儲存 $Servers 陣列變數。

        $Servers = Get-AzureRmSiteRecoveryServer

2. 第二個命令取得 $Servers 陣列中的第一個伺服器的網站復原網路。 命令 $Networks 變數中儲存的網路。


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. 第三個] 命令會取得 Azure 虛擬網路，然後按一下 [該值 $AzureVmNetworks 變數中。

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. 最終的指令程式所建立的主要的網路和 Azure 虛擬機器網路之間的對應。 Cmdlet 的主要網路指定 $Networks 的第一個項目。 Cmdlet 指定虛擬機器網路 $AzureVmNetworks 的第一個項目。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>步驟 9︰ 啟用保護虛擬機器

伺服器、 雲朵和網路設定正確之後，您可以啟用虛擬機器雲端中的保護。

 請注意下列事項︰

 - 虛擬機器必須符合 Azure 需求。 檢查下列項目中[的先決條件及支援](site-recovery-best-practices.md)規劃指南。

 - 若要啟用保護、 作業系統與作業系統磁碟屬性必須設定虛擬機器。 當您建立虛擬機器 VMM 使用虛擬機器範本中時，您可以設定的屬性。 您也可以設定的虛擬機器屬性的 [**一般**] 及 [**硬體設定**] 索引標籤上的現有的虛擬機器這些屬性。 如果您未設定這些屬性 VMM 中您可以設定這些 Azure 網站復原入口網站中。


  1. 若要啟用保護，請執行下列命令以取得保護容器︰

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. 取得保護實體 (VM) 來執行下列命令︰

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. 啟用 vm DR 藉由執行下列命令︰

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>測試您的部署

若要測試您的部署，您可以執行測試失敗暫留在上一個虛擬機器，或建立包含多個虛擬機器復原計劃，並執行測試容錯移轉計劃。 測試容錯移轉模擬您隔離的網路中的容錯移轉及修復機制。 請注意︰

- 如果您想要在使用遠端桌面容錯移轉後的 Azure 虛擬機器連線時，啟用遠端桌面連線虛擬機器上執行測試容錯移轉之前。
- 之後容錯移轉，您會連線到在使用遠端桌面 Azure 虛擬機器使用的公用 IP 位址。 如果您想要執行此動作，請確定您沒有任何網域原則，無法連線至虛擬機器使用公用的地址。

若要查看完成作業，請依照下列[活動監視器](#monitor)] 中的步驟。


### <a name="run-a-test-failover"></a>執行測試容錯移轉

1.  先測試容錯移轉執行下列命令︰

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>執行計劃的容錯移轉

1. 執行下列命令以開始計劃的容錯移轉︰

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>執行意外的容錯移轉

1. 執行下列命令以啟動意外的容錯移轉︰

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


## <a name=monitor></a>監視器活動

您可以使用下列命令來監視活動。 請注意，您必須等候之間處理完成的工作。

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>後續步驟

[閱讀更多](https://msdn.microsoft.com/library/azure/mt637930.aspx)關於 Azure 網站修復透過 Azure 資源管理員 PowerShell cmdlet。
