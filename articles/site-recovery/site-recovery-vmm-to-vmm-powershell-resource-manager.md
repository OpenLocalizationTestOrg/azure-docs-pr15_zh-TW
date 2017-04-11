<properties
    pageTitle="使用 PowerShell （資源管理員） 的第二個 VMM 網站複製 HYPER-V 虛擬機器中 VMM 雲朵 |Microsoft Azure"
    description="說明如何部署 Azure 網站復原協調複寫、 錯誤移轉及復原 HYPER-V Vm VMM 雲朵中的次要 VMM 網站使用 PowerShell （資源管理員）"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>複製 HYPER-V 虛擬機器中 VMM 雲朵次要 VMM 網站使用 PowerShell （資源管理員）

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-vmm.md)
- [傳統的入口網站](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell-資源管理員](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

歡迎使用 Azure 網站復原 ！ 如果您想要複製內部部署 HYPER-V 虛擬機器這份文件管理系統管理中心的虛擬機器管理員 (VMM) 雲朵次要網站中使用。 

本文將示範如何使用 PowerShell 來自動化您需要執行 Azure 網站復原設定 HYPER-V 虛擬機器中系統管理中心 VMM 雲朵複寫次要網站中的系統管理中心 VMM 雲朵時的常見工作。

本文包含先決條件案例中，並顯示 

- 如何設定復原服務保存庫
- 來源 VMM 伺服器與目標 VMM 伺服器上安裝 Azure 網站修復提供者
- 在保存庫註冊 VMM 伺服器
- 設定 VMM 雲端複寫原則。 原則的複寫設定將會套用至所有受保護的虛擬機器 
- 啟用虛擬機器的保護。 
- 個別或復原計劃以確保一切運作正常的一部分，請測試 Vm 的容錯移轉。
- 執行計劃或意外的容錯移轉的 Vm 個別或屬於復原計劃以確保一切運作正常。

如果您遇到這種情況所設定的問題時，請[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼您的問題。

> [AZURE.NOTE] Azure 有兩種不同的[部署模型](../resource-manager-deployment-model.md)建立及使用的資源︰ Azure 資源管理員] 及 [傳統。 Azure 也會有兩個入口網站 – 支援傳統部署模型，Azure 傳統入口網站和支援兩種部署模型 Azure 入口網站。 本文涵蓋資源管理員部署模型。



## <a name="on-premises-prerequisites"></a>內部部署的先決條件

以下是您需要在內部部署的主要和次要網站中部署這種情況︰

**必要條件** | **詳細資料** 
--- | ---
**VMM** | 我們建議您部署主站台 VMM 伺服器及 VMM 伺服器中的次要網站。<br/><br/> 您也可以[複製之間雲朵單一 VMM 伺服器上](site-recovery-single-vmm.md)。 若要這麼做，您必須至少為兩個雲朵 VMM 伺服器上設定。<br/><br/> VMM 伺服器應該至少執行系統管理中心 2012 SP1 最新的更新。<br/><br/> 每個 VMM 伺服器必須在一或多個雲朵設定和所有雲朵必須設定 HYPER-V 容量設定檔。 <br/><br/>雲朵必須包含一個或多個 VMM 主機群組。<br/><br/>深入瞭解設定中[設定 VMM 雲端布料的轉印圖樣](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)VMM 雲朵和[逐步解說︰ 使用系統管理中心 2012 SP1 VMM 建立私人的雲朵](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。<br/><br/> VMM 伺服器應該能存取網際網路。 
**Hyper-v** | 必須至少執行 HYPER-V 伺服器 HYPER-V 角色與 Windows Server 2012，安裝最新的更新。<br/><br/> HYPER-V 伺服器應該包含一或多個 Vm。<br/><br/>  HYPER-V 主機伺服器應該位於主要和次要 VMM 雲朵主機群組。<br/><br/> 如果您 HYPER-V 集中在執行 Windows Server 2012 R2 您應該安裝[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果您 HYPER-V 叢集在執行 Windows Server 2012 記事的叢集代理人不會自動建立如果您有靜態 IP 位址基礎叢集。 您必須以手動方式設定叢集代理人。 [閱讀更多](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。
**提供者** | 在網站復原部署時，請在 VMM 伺服器上安裝 Azure 網站修復提供者。 透過協調複寫 HTTPS 443，網站復原進行通訊的提供者。 資料複寫發生之間的主要和次要 HYPER-V 伺服器 LAN 或 VPN 連線。<br/><br/> VMM 伺服器上執行的提供者需要存取這些 Url: *。 hypervrecoverymanager.windowsazure.com; *。accesscontrol.windows.net;*。 backup.windowsazure.com; *。blob.core.windows.net;*。 store.core.windows.net。<br/><br/> 此外允許[Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)從 VMM 伺服器防火牆通訊，並且允許 HTTPS (443) 通訊協定。

### <a name="network-mapping-prerequisites"></a>網路對應的先決條件
在 [主要和次要 VMM 伺服器上 VMM VM 網路之間的網路對應地圖︰

- 以最佳方式置於複本 Vm 次要 HYPER-V 主機後移轉後。
- 將複本 Vm 連線到適當的 VM 網路。
- 如果您沒有設定 Vm 無法連線至網路後移轉後的網路對應複本。
- 如果您想要設定的網路對應網站復原期間部署以下是您需要︰

    - 請確定來源 HYPER-V 主機伺服器上的 Vm 連線到 VMM VM 網路。 該網路應該邏輯網路雲端與相關聯的連結。
    - 請確認您可以使用復原次要雲端對應 VM 網路設定。 VM 網路應該邏輯網路與次要雲端相關聯的連結。


深入瞭解設定 VMM 網路下方的文件

- [如何設定在 VMM 中的邏輯網路](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [如何設定 VMM VM 網路及閘道器](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[瞭解更多](site-recovery-network-mapping.md)有關網路對應的運作方式。

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

1. 如果您沒有，建立 Azure 資源管理員資源群組

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. 新的修復服務保存庫中建立與儲存建立的 ASR 保存庫物件 （將會用於更新版本） 的變數。 您也可以擷取 ASR 保存庫物件文章建立使用取得 AzureRMRecoveryServicesVault 指令程式:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>步驟 3︰ 設定復原服務保存庫內容

1.  如果您已經建立保存庫，請執行以下命令，以取得保存庫。

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  設定保存庫內容執行以下命令。

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


## <a name="step-5-create-and-associate-a-replication-policy"></a>步驟 5︰ 建立，並建立關聯複寫原則

1.  建立 HYPER-V 2012 R2 複寫原則執行下列命令︰

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] VMM 雲端可包含 HYPER-V 主機執行不同版本的 Windows Server （如下所述 HYPER-V 先決條件），但複寫原則有特定的作業系統版本。 如果您有不同的主機在不同作業系統版本上執行，然後建立每一種作業系統版本的個別複寫的原則。 針對例如︰ 如果您有 Windows 伺服器 2012年在三個 Windows Server 2012 R2 上執行的五個主機，建立兩個原則複寫 – 針對每一種作業系統版本並列文字格式。

2.  取得主要保護容器 （主要 VMM 雲端） 及復原保護容器 （復原 VMM 雲端） 來執行下列命令︰
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  擷取您在步驟 1 使用原則的易記的名稱建立的原則

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  開始複寫原則 （VMM 雲端） 上的 [保護] 容器的關聯︰

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  等待原則關聯工作完成。 您可以檢查工作是否已完成使用下列 PowerShell 程式碼片段。
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    工作已完成處理之後，請執行下列命令︰

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


若要查看完成作業，請依照下列[活動監視器](#monitor)] 中的步驟。

## <a name="step-5-configure-network-mapping"></a>步驟 5︰ 設定網路對應

1. 第一個命令會取得目前 Azure 網站復原保存庫中的伺服器。 命令會將 Microsoft Azure 網站復原伺服器儲存 $Servers 陣列的變數。

        $Servers = Get-AzureRmSiteRecoveryServer

2. 下列命令以取得網站復原網路來源 VMM 伺服器及目標 VMM 伺服器。

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] 第一個或第二個伺服器陣列中的，可以是來源 VMM 伺服器。 檢查 VMM 伺服器的名稱，並適當地取得網路


4. 最終的指令程式所建立的主要的網路和修復網路之間的對應。 Cmdlet 指定主要網路 $PrimaryNetworks 和修復網路 $RecoveryNetworks 的第一個項目為第一個項目。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>步驟 6︰ 設定儲存對應

1. 以下命令會的儲存空間分類到 $storageclassifications 變數清單。

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. 下列命令取得來源分類到 $SourceClassificaion 變數和目標分類到 $TargetClassification 變數。 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] 來源及目標分類可以是陣列中的任何項目。 參照的輸出以下命令，以瞭解 $storageclassifications 陣列中的來源及目標分類的索引。 
    
    > 取得 AzureRmSiteRecoveryStorageClassification |選取物件的屬性 FriendlyName、 識別碼 |格式化表格


3. 下列 cmdlet 建立來源分類和目標分類之間的對應。 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>步驟 7︰ 啟用保護虛擬機器

伺服器、 雲朵和網路設定正確之後，您可以啟用虛擬機器雲端中的保護。 


  1. 若要啟用保護，請執行下列命令以取得保護容器︰
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. 取得保護實體 (VM) 來執行下列命令︰
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. 執行下列命令以啟用 vm 複寫︰

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>測試您的部署

若要測試您的部署，您可以執行測試容錯移轉單一虛擬機器，或建立包含多個虛擬機器復原計劃，並執行測試容錯移轉計劃。 測試容錯移轉模擬您隔離的網路中的錯誤移轉及復原機制。 

> [AZURE.NOTE] 您可以建立您的應用程式的復原計劃 Azure 入口網站中。

若要查看完成作業，請依照下列[活動監視器](#monitor)] 中的步驟。


### <a name="run-a-test-failover"></a>執行測試容錯移轉

1.  執行下列 cmdlet，以取得您要測試容錯移轉 VM 網路您 Vm 與。

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  執行 VM 測試容錯移轉，執行下列動作︰
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  執行復原計劃測試容錯移轉，執行下列動作︰
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>執行計劃的容錯移轉

1. 執行 VM 計劃容錯移轉，執行下列動作︰
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. 執行復原計劃的計劃容錯移轉，執行下列動作︰
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>執行意外的容錯移轉

1. 執行 VM 意外的容錯的移轉，執行下列動作︰
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2.執行復原計劃的意外的容錯移轉執行下列動作︰
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
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

