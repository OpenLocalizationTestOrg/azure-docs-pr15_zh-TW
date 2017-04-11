<properties
    pageTitle="複製 HYPER-V 虛擬機器中使用 Azure 網站復原和 PowerShell VMM 雲朵 |Microsoft Azure"
    description="瞭解如何將自動化 HYPER-V 虛擬機器中使用網站復原和 PowerShell VMM 雲朵複寫。"
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>使用 Powershell-傳統的 Azure 複寫 HYPER-V 虛擬機器中 VMM 雲朵

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-vmm-to-azure.md)
- [PowerShell-資源管理員](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [傳統的入口網站](site-recovery-vmm-to-azure-classic.md)
- [PowerShell-傳統](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>概觀

Azure 網站復原佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器中部署案例的數字。 部署案例的完整清單，請參閱[Azure 網站復原的概觀](site-recovery-overview.md)。

本文將示範如何使用 PowerShell 來自動化您需要執行 Azure 網站復原設定 HYPER-V 虛擬機器中系統管理中心 VMM 雲朵複製至 Azure 儲存體時的常見工作。

本文包含先決條件案例中，並顯示如何設定網站復原保存庫、 來源 VMM 伺服器上安裝 Azure 網站修復提供者，註冊此伺服器保存庫、 新增 Azure 儲存體帳戶、 HYPER-V host （主機） 伺服器上安裝 Azure 修復服務代理程式，設定會套用至所有受保護的虛擬機器 VMM 雲朵保護設定然後啟用這些虛擬機器中的保護。 藉由測試以確保一切運作正常容錯移轉完成。

如果您遇到這種情況所設定的問題時，請[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼您的問題。


> [AZURE.NOTE] Azure 有兩種不同的部署模型建立及使用的資源︰[資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。 本文涵蓋使用傳統部署模型。



## <a name="before-you-start"></a>在您開始之前

請確定您有下列先決條件位置︰

### <a name="azure-prerequisites"></a>Azure 的先決條件

- 您必須以[Microsoft Azure](https://azure.microsoft.com/)帳戶。 您可以開始使用[免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。
- 您必須 Azure 儲存體帳戶儲存複製的資料。 帳戶必須啟用地理複寫。 它應該位於 Azure 網站復原保存庫為相同的區域，並會與同一份訂閱相關聯。 [深入瞭解 Azure 儲存空間](../storage/storage-introduction.md)。
- 您需要，請確定您要保護的虛擬機器遵守[Azure 虛擬機器的先決條件](site-recovery-best-practices.md#virtual-machines)。

### <a name="vmm-prerequisites"></a>VMM 先決條件
- 您必須在系統管理中心 2012 R2 上執行的 VMM 伺服器。
- 您必須至少有一個您要保護 VMM 伺服器上的雲端。 雲端應該包含︰
    - 一或多個 VMM 主機群組。
    - 一或多個 HYPER-V 主機伺服器或叢集的每個主機] 群組中。
    - 一或多個虛擬機器來源 HYPER-V 伺服器上。

### <a name="hyper-v-prerequisites"></a>HYPER-V 先決條件

- Host （主機） HYPER-V 伺服器必須至少執行**Windows Server 2012** HYPER-V 角色或**Microsoft HYPER-V Server 2012** ，安裝最新的更新。
- 如果您執行的 HYPER-V 叢集記事中的叢集代理人不會自動建立如果您有靜態 IP 位址基礎叢集。 您必須以手動方式設定叢集代理人。 若要這麼做，請在 [伺服器管理員 > 容錯移轉叢集管理員，連線到叢集**設定的角色**，然後按一下 [高可用性精靈**選取角色**畫面中選取**HYPER-V 複本代理人**。
- VMM 雲端必須包含任何 HYPER-V 主機伺服器或您要管理保護。

### <a name="network-mapping-prerequisites"></a>網路對應的先決條件
當您在保護 Azure 網路對應地圖來源 VMM 伺服器上的 VM 網路之間的虛擬機器及目標 Azure 的網路啟用下列動作︰

- 在相同的網路上失敗的所有電腦可以都連線至彼此，它們是在不管修復您的方案資料。
- 如果網路閘道器的目標 Azure 網路上的設定，虛擬機器可以連線至其他內部部署虛擬機器中。
- 如果您沒有設定網路對應只虛擬機器的容錯相同的復原計劃都能連線至其他 Azure 失敗之後。

如果您想要部署網路對應必須下列動作︰

- 您想要保護來源 VMM 伺服器上的虛擬機器應該連線到 VM 網路。 該網路應該邏輯網路雲端與相關聯的連結。
- 要複製的虛擬機器可以連線後移轉後 Azure 網路。 在容錯移轉時，會選取這個網路。 網路應該 Azure 網站復原訂閱為相同的區域。
- [瞭解更多](site-recovery-network-mapping.md)有關網路對應︰

###<a name="powershell-prerequisites"></a>PowerShell 先決條件
請確定您已準備好 Azure PowerShell。 如果您已經在使用 PowerShell，您會需要升級至新版 0.8.10 或更新版本。 如需 PowerShell 設定的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 一旦您有安裝並設定為 PowerShell，您可以檢視所有服務的可用 cmdlet[以下](https://msdn.microsoft.com/library/dn850420.aspx)。

若要深入瞭解的秘訣可協助您使用的指令程式，例如參數值]、 輸入和輸出的通常是處理方式在 PowerShell 的 Azure，請參閱[開始使用 Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx)。

## <a name="step-1-set-the-subscription"></a>步驟 1︰ 設定訂閱

在 PowerShell 中執行下列 cmdlet:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

取代您的特定資訊 」 < > 」 內的項目。

## <a name="step-2-create-a-site-recovery-vault"></a>步驟 2︰ 建立網站復原保存庫

PowerShell 中 「 < > 」 內的項目取代您的特定資訊，並執行下列命令︰

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>步驟 3︰ 產生保存庫登錄機碼

產生保存庫登錄機碼。 下載 Azure 網站修復提供者，並將其安裝於 VMM 伺服器之後，您會使用此按鍵在保存庫註冊 VMM 伺服器。

1.  取得保存庫設定檔，並設定內容︰

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  執行下列命令以設定保存庫內容︰

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>步驟 4︰ 安裝 Azure 網站修復提供者

1.  VMM 在電腦上，請執行下列命令以建立目錄︰

    ```

    pushd C:\ASR\

    ```

2.  擷取執行下列命令以使用下載的提供者的檔案

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  安裝的提供者，請使用下列命令︰

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    安裝完成，請等候。

4.  登錄伺服器中使用下列命令保存庫︰

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>步驟 5︰ 建立 Azure 儲存體帳戶

如果您沒有安裝 Azure 儲存體帳戶，請執行下列命令以建立地理複寫啟用帳戶︰

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

請注意，儲存帳戶必須在同一個地區為 Azure 網站修復服務，會與同一份訂閱相關聯。


## <a name="step-6-install-the-azure-recovery-services-agent"></a>步驟 6︰ 安裝 Azure 復原服務代理程式

從 Azure 入口網站，安裝 Azure 修復服務代理程式位於您想要保護 VMM 雲朵每個 HYPER-V 主機伺服器上。

在所有 VMM 主機上執行下列命令︰

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>步驟 7︰ 設定雲端保護設定

1.  執行下列命令，以建立 Azure 的雲端保護設定檔︰

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  取得保護容器藉由執行下列命令︰

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  開始雲端保護容器的關聯︰

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  完成工作之後，請執行下列命令︰


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  工作已完成處理之後，請執行下列命令︰


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



若要查看完成作業，請依照下列[活動監視器](#monitor)] 中的步驟。

## <a name="step-8-configure-network-mapping"></a>步驟 8︰ 設定網路對應
在您開始之前的網路對應確認來源 VMM 伺服器上的虛擬機器連線到 VM 網路。 此外，建立一或多個 Azure 虛擬網路。 請注意，多個 VM 網路對應到單一 Azure 網路。

請注意，如果目標網路有多個子網路，而且這些子網路的其中一個有相同的來源虛擬機器有位置，然後複本虛擬機器會連線到目標該子後移轉後的子網路名稱。 如果不是目標子網路名稱相符，虛擬機器會連線到網路中的第一個子網路。

第一個命令會取得目前 Azure 網站復原保存庫中的伺服器。 命令會將 Microsoft Azure 網站復原伺服器儲存 $Servers 陣列變數。

    $Servers = Get-AzureSiteRecoveryServer


第二個命令取得 $Servers 陣列中的第一個伺服器的網站復原網路。 命令 $Networks 變數中儲存的網路。


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

第三個命令使用取得 AzureSubscription 指令程式取得您 Azure 的訂閱，然後將該值儲存到 $Subscriptions 變數。

    $Subscriptions = Get-AzureSubscription



第四個命令取得 Azure 虛擬網路使用取得 AzureVNetSite cmdlet，然後按一下 [該值 $AzureVmNetworks 變數中。


    $AzureVmNetworks = Get-AzureVNetSite



最終的指令程式所建立的主要的網路和 Azure 虛擬機器網路之間的對應。 Cmdlet 的主要網路指定 $Networks 的第一個項目。 Cmdlet 指定虛擬機器網路 $AzureVmNetworks 的第一個項目使用其識別碼。 命令包含您 Azure 訂閱 id。


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>步驟 9︰ 啟用保護虛擬機器

伺服器、 雲朵和網路設定正確之後，您可以啟用虛擬機器雲端中的保護。 請注意下列事項︰

虛擬機器必須符合[Azure 虛擬機器的先決條件](site-recovery-best-practices.md#virtual-machines)。

若要啟用保護的作業系統和作業系統磁碟屬性必須設定虛擬機器。 當您建立虛擬機器 VMM 使用虛擬機器範本中時，您可以設定的屬性。 您也可以設定的虛擬機器屬性的 [**一般**] 及 [**硬體設定**] 索引標籤上的現有的虛擬機器這些屬性。 如果您未設定這些屬性 VMM 中您可以設定這些 Azure 網站復原入口網站中。



1.  若要啟用保護，請執行下列命令以取得保護容器︰

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. 取得保護實體 (VM) 來執行下列命令︰


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. 啟用 vm DR 藉由執行下列命令︰



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>測試您的部署

若要測試您的部署，您可以執行測試容錯移轉單一虛擬機器，或建立包含多個虛擬機器復原計劃，並執行測試容錯移轉計劃。 測試容錯移轉模擬您隔離的網路中的錯誤移轉及復原機制。 請注意︰

- 如果您想要在使用遠端桌面後移轉後的 Azure 虛擬機器連線時，啟用遠端桌面連線虛擬機器上執行測試容錯移轉之前。
- 後移轉後，您會在使用遠端桌面 Azure 虛擬機器連線使用的公用 IP 位址。 如果您想要執行此動作，請確定您沒有任何網域原則，無法連線至虛擬機器使用公用的地址。

若要查看完成作業，請依照下列[活動監視器](#monitor)] 中的步驟。

### <a name="create-a-recovery-plan"></a>建立一個修復計劃

1. 建立.xml 檔案另存為範本的修復計劃使用的資料，並將其儲存為 「 C:\RPTemplatePath.xml 」。
2. 變更 RecoveryPlan 節點識別碼、 名稱、 PrimaryServerId，以及 SecondaryServerId。
3. 變更 ProtectionEntity 節點 PrimaryProtectionEntityId (從 VMM vmid)。
4. 您可以新增更多 Vm 新增更多 ProtectionEntity 節點。



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. 填入範本中的資料︰


        $TemplatePath = "C:\RPTemplatePath.xml";



5. 建立 RecoveryPlan:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>執行測試容錯移轉

1.  執行下列命令以取得 RecoveryPlan 物件︰

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  先測試容錯移轉執行下列命令︰


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[閱讀更多](https://msdn.microsoft.com/library/dn850420.aspx)Azure 網站復原 PowerShell 指令程式的相關資訊。 </a>.
