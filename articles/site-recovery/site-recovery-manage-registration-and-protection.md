<properties
    pageTitle="移除伺服器，並停用保護 |Microsoft Azure" 
    description="本文將說明如何移除註冊從網站復原保存庫的伺服器，以及如何停用虛擬機器及實體伺服器的保護。" 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>移除伺服器，並停用保護

Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)

## <a name="overview"></a>概觀

本文將說明如何從網站復原保存庫的伺服器及如何停用的虛擬機器網站復原受保護的內容。 

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="unregister-a-vmm-server"></a>移除註冊 VMM 伺服器

您可以移除從保存庫 VMM 伺服器註冊刪除 Azure 網站復原入口網站中的 [**伺服器**] 索引標籤上的伺服器。 請注意︰

-  **連線 VMM 伺服器**︰ 我們建議您將其連線至 Azure 時移除註冊 VMM 伺服器。 如此一來，可確保內部部署 VMM 伺服器，以及與其相關聯 （包含對應到您想要刪除的伺服器上的雲朵的雲朵 VMM 伺服器） VMM 伺服器上設定的適當清除。 我們建議您只移除並未連線的伺服器連線永久問題時。
- **未連線 VMM 伺服器**︰ 如果 VMM 伺服器無法連線時您將它刪除您必須執行指令碼手動執行清除。 使用[Microsoft 圖庫](http://aka.ms/asr-cleanup-script-vmm)中的指令碼。 若要手動清除程序完成，請注意 VMM ID 的伺服器。
- **叢集 VMM 伺服器**︰ 如果您要移除註冊部署叢集 VMM 伺服器執行下列動作︰

    - 如果伺服器的連線，請刪除連線的 VMM 伺服器上的 [**伺服器**] 索引標籤。 若要解除安裝在伺服器上的提供者，請登入的每個叢集節點並從 [控制台] 解除安裝。 執行叢集刪除登錄項目中的所有被動式節點的前一個區段中參照的清理指令碼。
    - 如果沒有連線伺服器，必須執行所有叢集節點清理指令碼。

### <a name="unregister-an-unconnected-vmm-server"></a>移除註冊並未連線的 VMM 伺服器

VMM 伺服器上您要移除︰

1. 移除註冊 VMM 伺服器從 Azure 入口網站。
2. 在 VMM 伺服器上，請下載清除指令碼。
3. 若要變更預設 (LocalMachine) 範圍的執行原則管理員選項會開啟並執行 PowerShell。
4. 請遵循指令碼中的指示進行。 

在有與您要移除的伺服器上的雲朵成對出現的雲朵 VMM 伺服器︰

1. 執行清理指令碼，然後遵循步驟 2 到 4。
2. 指定已登錄 VMM 伺服器的 VMM ID。 
3. 這個指令碼會移除 VMM 伺服器和雲端搭配資訊的註冊資訊。


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>移除登錄 HYPER-V 伺服器 HYPER-V 網站中

Azure 網站復原部署保護 HYPER-V 網站中的 HYPER-V 伺服器 （含沒有 VMM 伺服器） 上的虛擬機器時就可取消從保存庫 HYPER-V 伺服器，如下所示︰

1. 停用虛擬機器 HYPER-V 伺服器上的保護。
2. Azure 網站復原入口網站的 [**伺服器**] 索引標籤上選取 [伺服器 > 刪除。 當您執行此連線到 Azure 沒有伺服器。
3. 執行下列指令碼，清除 [在伺服器上的設定，並取消登錄，從保存庫。 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>停止保護 HYPER-V 虛擬機器

如果您想要停止保護 HYPER-V 虛擬機器必須移除保護。 根據您要如何移除保護您可能需要先清理保護設定，以手動方式在電腦上。 

### <a name="remove-protection"></a>移除保護

1. 在雲端的屬性的**虛擬機器**索引標籤上，選取 [的虛擬機器 >**移除**。
2. 在 [**確認移除的虛擬機器**頁面上，您會有幾個選項︰

    - **停用保護**，如果您啟用並儲存此選項將不再網站復原保護虛擬機器。 虛擬機器保護設定將會自動清除。
    - **從保存庫移除**，如果您選取這個選項，將只從網站復原保存庫移除虛擬機器。 虛擬機器內部部署保護設定不會受影響。 您必須以手動方式來移除保護設定，從 Azure 的訂閱移除虛擬機器並移除保護設定，必須先清理他們使用下方的指示，以手動方式設定清除。

如果您選取要刪除虛擬機器及其硬碟，他們會從目標位置。

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>清理保護設定，以手動方式 （之間 VMM 網站）

如果您選擇**停止管理虛擬機器**，手動清除設定︰

1. 主要伺服器上執行這個指令碼從 VMM 主控台清理主要的虛擬機器的設定。 VMM 主控台中按一下 [PowerShell] 按鈕以開啟 [VMM PowerShell 主控台。 SQLVM1 換成您的虛擬機器中的名稱。

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. 次要 VMM 伺服器上執行這個指令碼清理次要虛擬機器的設定︰

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. 在第二個 VMM 伺服器上，執行指令碼之後重新整理 HYPER-V 主機伺服器上的虛擬機器以便次要虛擬機器重新取得偵測 VMM 主控台中檢視。
4. 上述步驟會清除複寫設定只 VMM 伺服器。 如果您想要移除的虛擬機器的虛擬機器複製。 您將需要執行下列兩個主要步驟和次要虛擬機器。 執行指令碼來移除複製，並以您的虛擬機器名稱取代 SQLVM1 下方。

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>清理保護設定，以手動方式 （內部部署 VMM 網站和之間 Azure）

1. 在來源 VMM 伺服器上執行這個指令碼清理主要的虛擬機器的設定︰

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. 上述步驟會清除複寫設定只 VMM 伺服器。 一旦您已移除 VMM 伺服器複寫確保若要移除這個指令碼 HYPER-V 主機伺服器上執行的虛擬機器複寫。 SQLVM1 名稱取代您的虛擬機器和 host01.contoso.com HYPER-V 主機伺服器的名稱。

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>清理保護設定，以手動方式 （之間 HYPER-V 網站和 Azure）

1. 在來源 HYPER-V host （主機） 伺服器上，若要移除的虛擬機器複寫使用這個指令碼。 SQLVM1 換成您的虛擬機器中的名稱。

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>停止保護 VMware 虛擬機器或實體的伺服器

如果您想要停止保護 VMware 虛擬機器或實體的伺服器必須移除保護。 根據您要如何移除保護您可能需要先清理保護設定，以手動方式在電腦上。 

### <a name="remove-protection"></a>移除保護

1. 在雲端的屬性的**虛擬機器**索引標籤上，選取 [的虛擬機器 >**移除**。
2. **移除虛擬機器**上選取其中一個選項︰

    - **停用保護 （用於復原向下切入及音量調整大小）**，您只會看到，如果您已啟用此選項︰
        - **Resized 虛擬機器音量**，當您調整的音量，虛擬機器進入嚴重狀態。 如果這是選取此選項。 停用時保留復原點 Azure 中的保護。 當您重新啟用保護電腦調整大量的資料會傳送至 Azure。
        - **執行容錯移轉**— 您已執行從內部部署 VMware 虛擬機器或實體伺服器的容錯移轉至 Azure 測試您的環境之後，請選取這個選項，即可開始再次保護您的內部部署虛擬機器。 此選項會停用各虛擬機器，則您必須先重新啟用這些的保護。 請注意︰
            - 停用這項設定的虛擬機器並不會影響複本中 Azure 虛擬機器。
            - 從虛擬機器我解除安裝行動服務。
    
    - **停用保護**，如果您啟用並儲存此選項將不再網站復原保護電腦。 保護電腦的設定將會自動清除。
    - **從保存庫移除**，如果您選取這個選項，將只從網站復原保存庫移除電腦。 電腦的內部部署保護設定不會受影響。 若要移除的電腦上的設定，若要移除 Azure 虛擬機器的訂閱，您必須解除安裝行動服務，即可清除設定。
    
        ![移除選項](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















