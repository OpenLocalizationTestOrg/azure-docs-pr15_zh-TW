<properties
    pageTitle="使用 Azure 自動化 DSC 網站復原 Azure 複寫 VMware 虛擬機器 |Microsoft Azure"
    description="說明如何使用 Azure 自動化 DSC 自動部署 Azure Azure 網站復原行動服務及 Azure 虛擬/實體電腦的代理程式。"
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>使用 Azure 自動化 DSC 網站復原 Azure 複寫 VMware 虛擬機器

在作業管理套件，我們會提供完整的備份與損毀復原解決方案，您可以使用您的業務連續性計劃的一部分。

我們開始此路與 HYPER-V 使用 HYPER-V 複本。 但是我們已展開，以支援異質性的設定，因為客戶其雲朵中有多個 hypervisors 與平台。

如果您正在執行 VMware 負載及/或實體伺服器今天，管理伺服器執行所有 Azure 網站復原元件時 Azure 目的地處理通訊與資料與複寫 Azure 環境中。

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>使用自動化 DSC 部署網站復原行動服務
現在就讓我們開始藉此管理伺服器功能的快速分析。

管理伺服器執行許多伺服器角色。 這些角色的內容之一是]*設定*，來協調通訊和管理資料複寫及修復程序。

此外，*程序*角色作為複寫閘道器。 此角色接收複寫資料從受保護的來源電腦最佳化，使用快取、 壓縮和加密，然後將其傳送至 Azure 儲存體帳戶。 其中一個程序角色函數也會安裝行動服務發送給受保護的電腦，並執行 VMware Vm 的自動探索。

從 Azure 回復時，*主要目標*角色會處理複寫資料屬於此作業。

受保護的電腦，我們基於*行動服務*。 此元件已部署至您想要複製到 Azure 每個電腦 （VMware VM 或實體伺服器）。 它會擷取資料寫入在電腦上的，並轉寄給管理伺服器 （程序角色）。

當您正在處理業務連續性時，請務必瞭解您的工作量、 您基礎結構，以及相關的元件。 然後，您可以符合您復原時間目標 (RTO) 和復原點目標 (RPO) 的需求。 在此情況下，行動服務會是金鑰以確保您的工作量受到您所預期。

如何可以最佳化的方式，確保您擁有可靠的受保護的設定，從部分作業管理套件元件的協助？

本文提供如何使用 Azure 自動化所需的狀態設定 (DSC)，與網站修復，以確保的範例︰

- 在 Windows 電腦上執行您想要保護的部署行動性服務及 Azure VM 代理程式。
- Azure 複寫目標時，一律正在執行行動服務及 Azure VM 代理程式。

## <a name="prerequisites"></a>必要條件

- 若要儲存所需的設定存放庫
- 若要儲存與管理伺服器註冊所需的複雜密碼存放庫

 > [AZURE.NOTE] 為每個管理伺服器產生唯一的複雜密碼。 如果您要部署多個管理伺服器，必須以確保正確的複雜密碼儲存在 passphrase.txt 檔案。

- Windows 管理 Framework (WMF) 5.0 您想要啟用保護 （自動化 DSC 需求） 的電腦上安裝

 > [AZURE.NOTE] 如果您想要使用 WMF 4.0 安裝 DSC Windows 的電腦，請參閱[在中斷連線的環境中使用的 DSC](#Use DSC in disconnected environments)。

行動服務可以安裝透過命令列，並接受多個引數。 這就是為什麼您需要有二進位檔案 （後擷取其從您的安裝程式），並將它們儲存在以 DSC 設定擷取它們的地方。

## <a name="step-1-extract-binaries"></a>步驟 1︰ 解壓縮的二進位檔案

1. 若要擷取您需要針對此設定的檔案，瀏覽至下列目錄管理伺服器上︰

    **\Microsoft azure 網站 Recovery\home\svsystems\pushinstallsvc\repository**

    在這個資料夾中，您應該會看到名為 MSI 檔案︰

    **Microsoft ASR_UA_version_Windows_GA_date_Release.exe**

    擷取安裝程式，使用下列命令︰

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. 選取所有檔案，然後傳送給壓縮壓縮的資料夾。

現在，您可以使用自動化 DSC 自動化行動服務的設定您需要的二進位檔案。

### <a name="passphrase"></a>複雜密碼

接下來，必須先決定要將此壓縮的資料夾。 您可以使用 [Azure 儲存體帳戶，如下圖所示以上版本，才能儲存設定，您需要複雜密碼。 代理程式，然後將登錄與管理伺服器程序的一部分。

部署管理伺服器時，您有複雜密碼可以至文字檔案儲存為 passphrase.txt。

請將壓縮的資料夾，然後輸入複雜密碼的專用的容器中的 Azure 儲存體帳戶。

![資料夾的位置](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

如果您想要保留在網路上共用這些檔案，您可以這麼做。 只要有權存取您稍後使用 DSC 資源，且可以取得設定及複雜密碼。

## <a name="step-2-create-the-dsc-configuration"></a>步驟 2︰ 建立 DSC 設定

設定而定 WMF 5.0。 若要順利套用到自動化 DSC 設定電腦，WMF 5.0 需要存在於。

環境會使用下列範例 DSC 設定︰

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
設定將會執行下列動作︰

- 變數會告訴設定可在何處取得二進位行動服務和 Azure VM 代理程式，可在何處取得複雜密碼，並將輸出儲存的位置。
- 設定會匯入 xPSDesiredStateConfiguration DSC 資源，讓您可以使用`xRemoteFile`從存放庫下載檔案。
- 設定將會建立的目錄，您要儲存檔案的位置。
- 封存資源將會擷取檔案從壓縮的資料夾。
- 封裝安裝資源將會從 UNIFIEDAGENT 安裝行動服務。以特定的引數執行檔安裝程式。 （建構引數的變數需要變更，以反映您的環境）。
- 封裝 AzureAgent 資源將會安裝 Azure VM 代理程式，建議您在執行 Azure 中每個 VM。 Azure VM 代理程式也可讓您可以加入 VM 後移轉後副檔名。
- 服務資源，可確保相關的行動服務和 Azure 服務一律執行。

為**ASRMobilityService**儲存設定。

>[AZURE.NOTE] 請記得要取代您的組態 CSIP，以反映實際管理伺服器，使代理程式會正確連接，並會使用正確的複雜密碼。

## <a name="step-3-upload-to-automation-dsc"></a>步驟 3︰ 上傳至自動化 DSC

因為您所做的 DSC 設定會匯入所需的 DSC 資源模組 (xPSDesiredStateConfiguration)，您需要您上傳 DSC 設定之前，匯入中自動化的模組。

登入您的自動化帳戶，瀏覽至**資產** > **模組**中，按一下 [**瀏覽] 圖庫**。

您可以在這裡搜尋模組然後匯入至您的帳戶。

![匯入模組](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

當您完成此時，請移至您的電腦必須已安裝的 Azure 資源管理員模組的位置，然後繼續進行匯入新建立的 DSC 設定檔。

### <a name="import-cmdlets"></a>匯入 cmdlet

PowerShell 的 Azure 訂閱登入。 修改 cmdlet，以反映您的環境，擷取您在變數中的自動化帳戶資訊︰

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

設定上載至自動化 DSC，使用下列 cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>編譯自動化 DSC 中的設定

接下來，您需要編譯自動化 DSC 中的設定，好讓您可以開始註冊其節點。 您達到，執行下列 cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

這可能需要幾分鐘，，因為您基本上要部署代管 DSC 擷取服務的設定。

編譯設定之後，您就可以擷取工作資訊，使用 PowerShell (取得 AzureRmAutomationDscCompilationJob)，或使用[Azure 入口網站](https://portal.azure.com/)。

![擷取工作](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

您現在已成功發佈並將上傳至自動化 DSC 的 DSC 設定。

## <a name="step-4-onboard-machines-to-automation-dsc"></a>步驟 4︰ 內建自動化 DSC 機器
>[AZURE.NOTE] 完成這種情況的必要條件是您的 Windows 電腦更新 WMF 的最新版本。 您可以下載及安裝您的平台[下載中心](https://www.microsoft.com/download/details.aspx?id=50395)正確的版本。

現在，您就會建立 metaconfig 的 DSC，您會套用至您的節點。 若要成功工作，您必須擷取端點 URL 和您所選的自動化帳戶 Azure 中的主索引鍵。 您可以找到自動化帳戶的**所有設定**刀**機碼**之下這些值。

![關鍵值](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

在此範例中，您可以有您想要使用網站復原保護的 Windows Server 2012 R2 實體伺服器。

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>檢查有任何擱置在登錄中的檔案重新命名作業

啟動與自動化 DSC 端點伺服器之前，建議您檢查有任何擱置在登錄中的檔案重新命名作業。 他們可能會禁止安裝完成，因為擱置重新啟動電腦。

執行下列 cmdlet 來確認是在伺服器上的沒有擱置重新啟動︰

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
這會顯示空白的如果您是 [確定] 以繼續進行。 如果沒有出現，您應該解決這個問題，重新啟動期間進行的維修作業] 視窗的伺服器。

若要在伺服器上套用設定，開始 PowerShell 整合式指令碼環境 （ise [以系統），然後執行下列指令碼。 這是基本上 DSC 本機組態會指示本機組態管理員引擎登錄的自動化 DSC 服務，並可擷取特定設定 (ASRMobilityService.localhost)。

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

此設定會導致本機組態管理員引擎登錄自動化 DSC。 它也會決定引擎應該操作方式與它應該做什麼設定飄移 (ApplyAndAutoCorrect)，是否應該如何進行設定如果需要重新啟動電腦。

執行這個指令碼之後，節點應該能如常自動化 DSC 註冊。

![進行中的節點註冊](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

如果您回復 Azure 入口網站中，您可以看到新註冊的節點具有現在出現在入口網站。

![在入口網站註冊的節點](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

在伺服器上，您可以執行下列 PowerShell cmdlet 來確認節點具有正確登錄︰

```powershell
Get-DscLocalConfigurationManager
```

已取出及套用到伺服器設定之後，您可以驗證，請執行下列 cmdlet:

```powershell
Get-DscConfigurationStatus
```

輸出顯示伺服器已成功提取設定︰

![輸出](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

此外，行動服務設定會有您可以在*系統磁碟機*\ProgramData\ASRSetupLogs 找到的專屬記錄。

就是它。 您現在已成功部署和將登錄行動服務，在您想要使用網站復原保護的電腦上。 DSC 會確定一律執行所需的服務。

![成功部署](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

管理伺服器偵測到部署成功後，您可以設定保護，並啟用複寫在電腦上的使用網站復原。

## <a name="use-dsc-in-disconnected-environments"></a>中斷連線的環境中使用 DSC

如果您的電腦未連線到網際網路，您仍然可以依賴 DSC 部署，並設定您想要保護的工作負載行動服務。

您可以在您的環境基本上提供相同的能力從自動化 DSC 取得的初始化自己 DSC 提取伺服器。 就是用戶端將會提取 DSC 端點 （之後已註冊） 設定。 不過，另一個選項是手動本機或遠端發送給您的電腦，DSC 設定。

請注意，在此範例中，新增的參數的電腦名稱。 遠端檔案現在位於遠端共用應存取由想要保護的電腦。 指令碼結尾實行潛在設定，便會開始 DSC 設定套用到目標電腦上。

### <a name="prerequisites"></a>必要條件

請確定已安裝 xPSDesiredStateConfiguration PowerShell 模組。 安裝 WMF 5.0 Windows 電腦，您可以安裝 xPSDesiredStateConfiguration 模組目標電腦上執行下列 cmdlet:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

您也可以下載並儲存模組，您需要將其發送給有 WMF 4.0 的 Windows 電腦。 PowerShellGet (WMF 5.0) 在哪裡簡報在電腦上執行這個指令程式︰

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

也 WMF 4.0，請確定[Windows 8.1 更新 KB2883200](https://www.microsoft.com/download/details.aspx?id=40749)已安裝在電腦上。

可在 Windows 電腦上執行，其中的 WMF 5.0 和 WMF 4.0 推入下列設定︰

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

如果您想要產生來模仿功能，您可以從自動化 DSC 取得公司網路上的您自己 DSC 提取伺服器，請參閱[DSC 網頁擷取伺服器設定](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396)。

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>可省略︰ 使用 Azure 資源管理員範本部署 DSC 設定

本文的重點您如何建立您自己的 DSC 設定自動部署行動性服務和 Azure VM 代理程式，並確保其在您要保護的電腦上執行。 我們也可以將新的或現有 Azure 自動化帳戶部署此 DSC 設定 Azure 資源管理員範本。 範本會使用輸入的參數來建立包含為您的環境變數自動化資產。

部署範本後，您可以直接參照至本指南將內建的步驟 4 您的電腦。

範本會執行下列動作︰

1. 使用現有的自動化帳戶或建立新的範本
2. 讓輸入的參數︰
    - ASRRemoteFile-行動性服務設定的儲存位置的位置
    - ASRPassphrase-passphrase.txt 檔案的儲存位置的位置
    - ASRCSEndpoint-管理伺服器的 IP 位址
3. 匯入 xPSDesiredStateConfiguration PowerShell 模組
4. 建立及編譯 DSC 設定

好讓您可以開始登入保護的電腦，會發生順序正確，所有先前的步驟。

範本，以供部署，指示位於[GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC)。

使用 PowerShell 部署範本︰

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>後續步驟

部署行動性服務代理程式之後，您可以[啟用複寫](site-recovery-vmware-to-azure.md#step-6-replicate-applications)虛擬機器。
