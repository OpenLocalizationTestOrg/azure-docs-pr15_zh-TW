<properties 
    pageTitle="保護 SQL Server Azure 網站復原與 SQL Server 損毀修復 |Microsoft Azure" 
    description="本文將說明如何複製 SQL Server 使用 Azure 網站復原的 SQL Server 損毀的功能。" 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>使用 SQL Server 損毀修復和 Azure 網站復原保護 SQL Server 


Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)。

 本文將說明如何保護使用 SQL Server BCDR 技術和 Azure 網站復原的組合的應用程式的 SQL Server 後端。 您應該瞭解的 SQL Server 損毀修復功能 (容錯，AlwaysOn 可用性群組，資料庫鏡像記錄傳送) 和 Azure 網站修復，在部署本文所述的案例之前。



## <a name="overview"></a>概觀

許多工作負載使用 SQL Server 的基礎。 應用程式，例如 SharePoint、 Dynamics 和 SAP 使用 SQL Server 執行資料服務。  應用程式部署 SQL Server 幾種不同的方式︰

- **獨立的 SQL Server**: SQL Server 及所有資料庫裝載在單一電腦 （實體或虛擬）。 虛擬化，當主機叢集可用於本機的可用性。 實作沒有來賓層級的可用性。
- **SQL Server 容錯移轉叢集執行個體 (永遠在 FCI)**: Windows 容錯移轉叢集設定兩個或多個共用磁碟 SQL server 執行個體節點。 如果叢集執行個體的任何中斷，叢集可能無法在 SQL Server 另一個執行個體。 此設定，通常是針對 HA 主要網站上。 它不會防止失敗或共用的儲存空間圖層中的資料。 可以使用 ISCSI 光纖通道或共用 VHDx 實作共用磁碟。
- **SQL 永遠在可用性群組**︰ 在此設定，兩個節點是在執行任何動作叢集使用 SQL Server 資料庫設定同步複寫與自動容錯移轉的可用性] 群組中的共用設定。

在企業版，SQL Server 也提供原生損毀復原技術，遠端資料庫復原。 本文中，我們將會運用，使用這些原生 SQL 損毀復原技術整合︰ 

- SQL 永遠在可用性群組損毀修復 SQL Server 2012 或 2014年企業版。
- SQL 資料庫鏡像 SQL Server 標準版 （任何版本），或 SQL Server 2008 R2 的高度安全模式中。


網站復原可以保護 SQL Server，如下表將摘要。

 |**內部部署至內部部署** | **內部部署至 Azure** 
---|---|---
**Hyper-v** | [是] | [是]
**VMware** | [是] | [是] 
**實體伺服器** | [是] | [是]


## <a name="support-and-integration"></a>支援與整合

SQL Server 版本支援的本文中的案例︰


- SQL Server 2014 企業版和標準
- SQL Server 2012 企業版和標準
- SQL Server 2008 R2 企業版和標準


網站復原可以整合與原生摘要下表中，以提供損壞修復解決方案的 SQL Server BCDR 技術。

**功能** |**詳細資料** | **SQL Server 版本** 
---|---|---
**AlwaysOn 可用性] 群組** | 多個獨立執行個體的 SQL Server 每個執行中具有多個節點的容錯移轉叢集。<br/><br/>資料庫可分為可以複製 （左右對稱） 在 SQL Server 執行個體，因此不共用的儲存空間會需要的容錯移轉群組。<br/><br/>提供主要網站與一個或多個次要網站之間的損毀修復。 兩個節點可以設定中共用叢集使用 SQL Server 資料庫設定同步複寫與自動容錯移轉的可用性] 群組中的任何動作。 | SQL Server 2014 與 2012年企業版
**容錯 (AlwaysOn FCI)** | SQL Server 會使用 Windows 容錯的內部部署的 SQL Server 工作負載的可用性。<br/><br/>容錯移轉叢集中設定共用磁碟執行 SQL Server 執行個體的節點。 如果執行個體是向下叢集無法移到不同的項目。<br/><br/>叢集不防止失敗或中斷中共用的儲存空間。 共用的磁碟可實作與 iSCSI，光纖通道，或共用 VHDXs。 | SQL Server 企業版<br/><br/>（有限制只有兩個節點） 的 SQL Server 標準版
**資料庫鏡像 （高的安全模式）** | 保護單一資料庫單一次要複本。 提供兩個高的安全性 （同步） 及高效能 （非同步） 複寫模式。 不需要容錯移轉叢集。 | SQL Server 2008 R2<br/><br/>SQL Server 企業所有版本
**獨立的 SQL Server** | SQL Server 與資料庫被裝載在單一伺服器 （實體或虛擬）。 如果虛擬伺服器主機叢集用於可用性。 沒有來賓層級可用性。 | 企業版或標準版

## <a name="deployment-recommendations"></a>部署建議


下表摘要列出建議網站復原整合 SQL Server BCDR 技術。

**版本** |**版本** | **部署** | **內部部署至內部部署** | **內部部署至 Azure** 
---|---|---|---|---
SQL Server 2014 或 2012 | 企業版 | 容錯移轉叢集執行個體 | AlwaysOn 可用性群組 | AlwaysOn 可用性群組
 | 企業版 | 高可用性 AlwaysOn 可用性群組 | AlwaysOn 可用性群組 | AlwaysOn 可用性群組
 | 標準 | 容錯移轉叢集執行個體 (FCI) | 網站與本機的左右對稱的復原複寫 | 網站與本機的左右對稱的復原複寫
 | 企業版或標準 | 獨立版 | 網站復原複寫 | 網站復原複寫
SQL Server 2008 R2 | 企業版或標準 | 容錯移轉叢集執行個體 (FCI) | 網站與本機的左右對稱的復原複寫 | 網站與本機的左右對稱的復原複寫
 | 企業版或標準 | 獨立版 | 網站復原複寫 | 網站復原複寫
SQL Server （任何版本） | 企業版或標準 | 容錯移轉叢集執行個體-DTC 應用程式 | 網站復原複寫 | 不支援


## <a name="deployment-prerequisites"></a>部署先決條件

以下是您需要之前開始︰

- 內部部署的 SQL Server 部署執行的是受支援的 SQL Server 版本。 通常您也必須 Active Directory SQL server。
- 您想要部署案例的先決條件。 每個部署文件中找的先決條件。 在[網站修復概觀](site-recovery-overview.md)會提供這些連結。
- 如果您想要設定 Azure 中的 [復原，必須執行[Azure 虛擬機器整備評估](http://www.microsoft.com/download/details.aspx?id=40898)工具，請確定已相容於 Azure 及網站復原您 SQL Server 虛擬機器上。


## <a name="set-up-active-directory"></a>設定 Active Directory

您必須正確地執行 SQL Server 次要復原網站上的 Active Directory。 有幾個選項︰

- **小型企業版**，如果您有幾個應用程式與內部部署網站的單一網域控制站，且您想要在整個網站失敗，我們建議您複製的網域控制站次要資料中心或 Azure 使用網站復原 repication。

- **為大型企業版中**，如果您有大量的應用程式，您執行的在 Active Directory 樹系，且您想要移動的應用程式或工作量失敗，我們建議您設定其他網域控制站或 Azure 中第二個資料中心。 請注意，是否您要還原至遠端網站使用 AlwaysOn 可用性群組我們建議您將另一個其他網域控制站次要網站或 Azure 設定為用於復原的 SQL Server 執行個體。

這份文件中的指示進行假設此網域控制站可在第二個位置。 [閱讀更多](site-recovery-active-directory.md)有關保護網站復原的 Active Directory。

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>整合 SQL Server Always-On （內部部署至 Azure） 的保護


網站復原原本即支援 SQL AlwaysOn。 如果您已經建立 SQL 可用性群組，然後您可以使用復原網站來管理可用性群組的容錯移轉，將設定為 「 次要' Azure 虛擬機器使用。 

>[AZURE.NOTE] 此功能是目前預覽中，且可供主要的資料中心 HYPER-V 主機伺服器 VMM 雲朵中管理及 VMware 設定會管理[設定伺服器](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)。 現在這項功能不適用於新 Azure 入口網站，以滑鼠右鍵。

#### <a name="prerequisites"></a>必要條件

以下是您需要 SQL AlwaysOn 整合網站修復︰

- 內部部署 SQL Server （獨立的伺服器或容錯移轉叢集）。
- 一或多個 Azure 虛擬機器安裝 SQL server
- SQL 可用性設定群組之間內部部署的 SQL Server 和 SQL Server Azure 中執行
- 應在內部部署的 SQL Server 電腦上啟用遠端 PowerShell。 VMM 伺服器或設定應會撥打到 SQL Server 的遠端 PowerShell 通話。
- 應在內部部署的 SQL Server，至少這些權限與這些 SQL 使用者群組中新增使用者帳戶︰
    - 變更可用群組︰ 權限在[這裡](https://msdn.microsoft.com/library/hh231018.aspx)，與[這裡](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER 資料庫的權限[以下](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- 應該 VMM 伺服器上建立 RunAs 帳戶，或上一個步驟所述的使用者使用 CSPSConfigtool.exe 設定伺服器上建立帳戶 
- 在 SQL Server 執行於內部部署，和 Azure 虛擬機器應該安裝 SQL PS 模組
- 應該安裝的 Azure 上執行的虛擬機器 VM 代理程式。
- NTAUTHORITY\System 應有追蹤中 Azure 虛擬機器上執行的 SQL Server 上的 [權限︰
    - 變更可用性群組的權限在[這裡](https://msdn.microsoft.com/library/hh231018.aspx)，與[這裡](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER 資料庫的權限[以下](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>步驟 1︰ 新增 SQL Server


1. 按一下 [**新增 SQL**新增新的 SQL Server]。 

    ![新增 SQL](./media/site-recovery-sql/add-sql.png)

2. 在 [**設定 SQL 設定** > **名稱**提供參照到 SQL Server 好記的名稱。
3. **在 SQL Server (FQDN)**指定來源您想要新增的 SQL Server 的 FQDN。 萬一容錯移轉叢集已安裝 SQL Server，然後提供叢集的並不屬於任何叢集節點的 FQDN。  
4. 在**SQL Server 執行個體**中選擇預設的執行個體，或提供自訂的執行個體的名稱。
5. 在 [**管理伺服器**上，選取 [VMM 伺服器或設定伺服器註冊網站復原保存庫中。 網站修復使用此管理伺服器通訊與 SQL Server。
6. 在**執行為帳戶**提供的指定 VMM 伺服器建立的 RunAs 帳戶或帳戶 Configuraaaon 伺服器上建立的名稱。 此帳戶用來存取 SQL Server，並在 SQL Server 的電腦上的顯示狀態群組應有讀取及容錯移轉權限。

    ![新增 [SQL] 對話方塊](./media/site-recovery-sql/add-sql-dialog.png)

新增 SQL Server 之後，會出現在 [**伺服器**] 索引標籤。 

![SQL Server 清單](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>步驟 2︰ 新增 SQL 可用性群組

1. SQL Server 的電腦後的下一個步驟是將可用性群組加入至網站復原。 若要執行這項作業，向下切入在上一個步驟中新增 SQL Server 內，按一下 [新增 SQL 可用性群組。 

    ![新增 SQL AG](./media/site-recovery-sql/add-sqlag.png)

2. SQL 可用性群組可以複製到一或多個中 Azure 虛擬機器中。 當新增 sql 可用性群組，您必須提供的名稱及您要的顯示狀態群組會無法透過網站復原 Azure 虛擬機器的訂閱。

    ![新增 SQL AG] 對話方塊](./media/site-recovery-sql/add-sqlag-dialog.png)

3. 在上述範例中的可用性群組 DB1 AG 會變成主要容錯移轉上執行訂閱 DevTesting2 內的虛擬機器 SQLAGVM2 上。 

>[AZURE.NOTE] 只會在上述步驟新增 SQL Server 上的主要可用性群組可新增至網站復原。 如果您所做的可用性群組主要在 SQL Server 或新增之後，您已 SQL Server 上新增更多的可用群組，請重新整理 SQL Server 上使用可用的 [重新整理] 選項。

#### <a name="step-3-create-a-recovery-plan"></a>步驟 3︰ 建立一個修復計劃

下一步是建立復原計劃使用虛擬機器和 [可用性] 群組。 選取 [步驟 1 中的 [相同 VMM 伺服器或您使用的設定伺服器]，為來源和目標為 Microsoft Azure。

![建立復原計劃](./media/site-recovery-sql/create-rp1.png)

![建立復原計劃](./media/site-recovery-sql/create-rp2.png)

範例中的 Sharepoint 應用程式所組成的 SQL 可用性群組作為其後端 3 虛擬機器。 我們無法選取兩個可用性群組以及這個復原計劃中虛擬機器的構成應用程式。 

您可以將虛擬機器移至不同的容錯移轉群組排列順序的容錯移轉順序，進一步自訂復原計劃。 可用性群組一律會在第一個失敗，因為想做的任何應用程式後端。 

![自訂復原計劃](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>步驟 4︰ 容錯

可用性群組已新增至復原計劃，就可以使用不同的容錯移轉選項。

容錯移轉 | 詳細資料
--- | ---
**計劃的容錯移轉** | 計劃的容錯移轉表示沒有資料遺失容錯移轉。 若要達到 SQL 可用性] 群組中的可用性模式第一次設為 [同步，並提供可用性群組加入網站復原時的虛擬機器將可用性群組主要然後觸發容錯移轉。 容錯移轉完成後，可用性模式已設為相同的值，是前觸發計劃的容錯移轉。
**意外的容錯移轉** | 將資料遺失，可能會意外的容錯移轉。 同時觸發意外的容錯移轉不變更 [可用性] 群組中的可用性模式和 it 由主要哪可用性群組加入網站復原時所提供的虛擬機器。 一旦意外的容錯移轉完畢，並執行 SQL Server 內部部署伺服器時，即可，反向複寫有要觸發在可用性] 群組。 請注意，這個動作無法復原計劃使用 SQL Server] 索引標籤下可以採取 SQL 可用性群組
**測試容錯移轉** | 不支援的 SQL 可用性] 群組中的測試容錯移轉。 如果觸發包含 SQL 可用性群組復原計劃 」 的測試容錯移轉時，則會略過容錯移轉可用性群組。


請考慮下列容錯移轉選項。

選項 | 詳細資料
--- | ---
**選項 1** | 1.執行測試容錯移轉應用程式和前端層。<br/><br/>2.更新應用程式層存取在唯讀模式中，複本，並執行應用程式的唯讀測試。
**選項 2** | 1.建立複本 SQL Server 虛擬機器執行個體 （VMM 複製至網站或使用 Azure 備份） 的複本，並將其開啟測試網路中<br/><br/> 2.執行測試容錯移轉使用復原計劃。

步驟 5︰ 回失敗

如果您要再次可用性群組主要在內部部署的 SQL Server 然後您可以執行觸發計劃上復原計劃的容錯移轉選擇並方向從 Microsoft Azure 內部部署 VMM 伺服器。

>[AZURE.NOTE] 意外的容錯移轉後反向複寫有要觸發在可用性群組，若要繼續複寫。 這是直到複寫仍會保留已擱置。



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>保護電腦沒有 VMM 伺服器] 或 [設定伺服器

不受 VMM 伺服器或設定伺服器的環境，Azure 自動化 Runbooks 可以用於設定 SQL 可用性群組的指令碼錯誤後移轉。 以下是設定的步驟︰

1.  建立容錯可用性] 群組中的指令碼的本機檔案。 此範例指令碼 Azure 複本上指定 [可用性] 群組中的路徑，且透過無法該複本執行個體。 這個指令碼會執行 SQL 在伺服器上傳的複本虛擬機器自訂指令碼副檔名。

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  將指令碼的上傳至 blob Azure 儲存體帳戶。 使用此範例中︰

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  建立叫用的指令碼 SQL Server 複本虛擬機器中 Azure Azure 自動化 runbook。 您可以使用這個範例指令碼，請執行下列動作。 [深入](site-recovery-runbook-automation.md)瞭解如何使用自動化 runbooks 復原方案。 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  當您建立的應用程式修復計劃新增 「 預先群組 1 開機 」 指令碼的步驟叫用自動化 runbook，在可用性群組失敗。

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>整合 SQL AlwaysOn （內部部署至內部部署） 的保護

如果使用 SQL Server，高可用性，可用性群組或容錯移轉叢集執行個體，建議的復原網站上使用可用性群組。 請注意，本指南不使用的分散式的交易的應用程式。

1. [設定資料庫](https://msdn.microsoft.com/library/hh213078.aspx)可用性群組。
2. 在 [次要網站上建立新的虛擬網路。
3. 設定新的虛擬網路之間的主要網站至網站 VPN。
4. 復原網站上建立虛擬機器，並在其上安裝 SQL Server。
5. 延伸至新的 SQL Server 虛擬機器現有的 AlwaysOn 可用性群組。 設定此 SQL Server 執行個體為非同步的複本。
6. 建立可用性群組接聽程式，或更新現有的接聽程式，包括非同步複本虛擬機器。
7. 請確定應用程式伺服器陣列設定使用接聽程式。 如果您是使用資料庫伺服器名稱的設定，請更新以使用接聽程式，因此您不需要後移轉後重新設定。

應用程式使用的分散式的交易我們建議您使用[舊複寫網站復原](site-recovery-vmm-san.md)或[VMWare/實體伺服器網站間複製](site-recovery-vmware-to-vmware.md)。

### <a name="recovery-plan-considerations"></a>復原規劃考量

1. 將這個範例指令碼新增至主要和次要網站上的 VMM 文件庫中。

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. 當您建立的應用程式修復計劃新增 「 預先群組 1 開機 」 指令碼的步驟叫用的指令碼容錯可用性群組。



## <a name="protect-a-standalone-sql-server"></a>保護獨立的 SQL Server

在此組態中，我們建議您使用網站復原複寫來保護的 SQL Server 的電腦。 確切步驟取決於是否 SQL Server 設定為虛擬機器或實體的伺服器，和您想要複製到 Azure 還是次要內部部署網站。 [網站修復概觀](site-recovery-overview.md)中取得所有部署案例的指示。


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>保護 SQL Server 叢集 (標準或 2008 R2)

執行 SQL Server 標準版或 SQL Server 2008 R2 叢集我們建議您使用網站復原複寫來保護 SQL Server。

### <a name="on-premises-to-on-premises"></a>內部部署至內部部署

- 如果應用程式的使用的分散式的交易建議 VMware 環境部署[網站復原舊複寫](site-recovery-vmm-san.md)HYPER-V 環境與[VMware VMware/實體伺服器](site-recovery-vmware-to-vmware.md)。

- 非 DTC 應用程式，利用上述的方法來復原叢集為獨立伺服器充分使用本機高度安全性 DB 左右對稱。

### <a name="on-premises-to-azure"></a>內部部署至 Azure

網站復原複寫到 Azure 時，不支援來賓叢集支援。 SQL Server 也不會提供低成本損壞修復解決方案標準版。 我們建議您保護內部部署的 SQL Server 叢集，獨立 SQL Server 和 Azure 中復原。


1. 設定內部部署網站上的其他獨立 SQL Server 執行個體。
2. 設定要做為需要保護資料庫的左右對稱此執行個體。 設定高的安全模式中鏡像。
3.  設定 [根據環境 （[HYPER-V](site-recovery-hyper-v-site-to-azure.md)或[VMware/實體伺服器](site-recovery-vmware-to-azure-classic.md)內部部署網站上 [網站復原。
4.  若要將新的 SQL Server 執行個體複寫到 Azure 使用網站復原複寫。 高度安全性鏡像複製及與主要叢集，因此會同步處理但它會複製到 Azure 使用網站復原複寫。

下圖說明這項設定。

![標準叢集](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>回復考量

SQL 標準叢集，回復意外的容錯移轉後將需要 SQL 備份，並從鏡像執行個體還原到原始叢集和重新建立鏡像。

## <a name="next-steps"></a>後續步驟
[瞭解更多](site-recovery-best-practices.md)有關準備好要部署網站復原。










 