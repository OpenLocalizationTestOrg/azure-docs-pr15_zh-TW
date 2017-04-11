<properties
    pageTitle="Microsoft Azure 堆疊疑難排解 |Microsoft Azure"
    description="Azure 堆疊疑難排解。"
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure 堆疊疑難排解

這份文件提供 Azure 堆疊的一般疑難排解的資訊。

獲得最佳體驗，請確定您的部署環境之前安裝所有[需求](azure-stack-deploy.md)與[準備工作](azure-stack-run-powershell-script.md)都符合。 

本節所述的問題的疑難排解的建議所衍生的多個來源和可能或可能無法解決您的特定問題。 如果您遇到問題並未記錄，請務必[Azure 堆疊 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)，進一步支援與其他資訊。

程式碼範例提供現狀，預期的結果不保證。 本節受到常用的編輯和更新實作產品改進的。

  

## <a name="known-issues"></a>已知的問題

 - 您可能會看到下列非終止錯誤部署期間，並會影響部署成功︰
     - 「 'C:\WinRM\Start-Logging.ps1' 無法辨識的字詞 」
     - 「 呼叫 EceAction︰ 無法在 null 陣列中的索引 」 
     - 「 InvokeEceAction︰ 無法參數 [訊息] 連結引數，因為它是空字串。 」
 - 您可能會看到部署失敗步驟 60.61.93 發生錯誤 」 應用程式識別碼 」 URI 「 找不到。 」 這是因為 Azure Active Directory 中登錄應用程式的方式。  如果您收到此錯誤，請繼續執行[重新執行安裝指令碼](azure-stack-rerun-deploy.md)的步驟 60.61.93 部署完成後，才。
 - 您會看到**virtualMachine ARM** ] 類別底下顯示**可用性設定**中的資源服務商場，– 這個外觀是只個問題。
 - 在入口網站中**的基本概念**] 步驟中，建立新的虛擬機器時儲存空間] 選項預設為 SSD。  HDD 或 VM 部署**大小**步驟，就必須變更此設定，您就不會看到可選取並繼續部署 VM 大小。 
 - 您會看到預設 MAS CON01 VM 上不會再安裝 AzureRM PowerShell 模組 （在 TP1 這稱為 ClientVM）。 這是根據設計，因為沒有[安裝這些模組](azure-stack-connect-powershell.md)和連線的替代方法。  
 - 您會看到**Microsoft.Insights**資源提供者不會自動註冊的租用戶訂閱。 如果您想要查看監視 vm 部署為租用戶的資料，請從 PowerShell 執行下列命令，（之後您[安裝並連線](azure-stack-connect-powershell.md)為租用戶）︰ 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - 您會看到的資源群組]，匯出在入口網站的功能，但不文字時顯示，並且可以匯出。      
 - 您可以開始部署的儲存空間資源大於可用的配額。  此部署失敗，並將擱置帳戶資源。  有兩種補救選項︰
     - 服務管理員可以增加配額] 中，變更會不會立即生效，而且經常最多可能需要一小時，才會傳播。
     - 服務管理員可以建立租用戶的學生可以新增訂閱的其他配額計劃的附加元件。
 - 建立 Vm 'Azure-china （中國) 」 中的身分識別與 Azure 堆疊環境中使用入口網站，您就不會看到 VM 大小可用的 VM 部署的 [**大小**] 步驟中選取，然後將無法繼續部署。
 - VM 實際已成功部署時，您可能會看到入口網站的部署失敗。
 - 當您刪除的計劃、 提供的服務或訂閱時，可能不會刪除 Vm。
 - 您會看到市場 VM 副檔名。
 - 您無法部署 VM 從儲存的 VM 圖像。
 - 租用戶，可能會看到不會包含在其訂閱服務。  租用戶嘗試部署這些資源時，會收到錯誤。  範例︰ 租用戶訂閱只包含儲存資源。  租用戶會看到 [建立其他 Vm 之類的資源的選項。  在此案例中，當租用戶嘗試部署 VM，他們會收到訊息，指出 VM 無法建立。 
 - 安裝 TP2，您應該啟動主應用程式中提供的位置執行 Azure 堆疊安裝指令碼，或您可能會收到錯誤訊息，說明 Windows VHD OS 即將到期。


## <a name="deployment"></a>部署

### <a name="deployment-failure"></a>部署失敗
如果您在安裝時遇到失敗，Azure 堆疊安裝程式可讓您繼續安裝失敗，按照[重新部署步驟執行](azure-stack-rerun-deploy.md)。

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>部署結尾 PowerShell 工作階段仍開啟，而不會顯示任何輸出

已選取時，此行為是只在 PowerShell 命令視窗中的預設行為的結果。 實際完成 POC 部署，但選取視窗時暫停指令碼。 您可以驗證這是 word 尋找 「 選取 」 中的 [命令] 視窗標題列的大小寫。  按 ESC 鍵，即可取消選取，並之後，要顯示完成的訊息。

## <a name="templates"></a>範本

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure 範本無法部署至 Azure 堆疊

請確認︰

- 範本必須使用已使用，或在預覽中 Azure 堆疊的 Microsoft Azure 服務。
- 特定資源所用的 Api 都支援本機 Azure 堆疊執行個體，且您的目標是有效的位置 （「 本機 」 中 Azure 堆疊技術預覽 (TP) 2，與 「 東亞美國 」 或 「 南美洲印度 」 Azure 中）。
- 您檢閱[這份文件](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)，瞭解測試 AzureRmResourceGroupDeployment cmdlet，其中掌握 Azure 資源管理員語法小差異。

您也可以使用[GitHub 存放庫](http://aka.ms/AzureStackGitHub/)中已提供 Azure 堆疊範本可協助您開始使用。

## <a name="virtual-machines"></a>虛擬機器

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>啟動我的 Microsoft Azure 堆疊 POC 主機之後, 所有我的租用戶 Vm 從 HYPER-V 管理員消失而回來自動於有點等候嗎？

當系統恢復設定儲存子系統及每秒要求數量需要判斷一致性。 所需的時間硬體而定，及規格使用，但可能會後重新啟動電腦的租用戶回頭辨識 Vm 主機一些時間。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>我已經刪除部分的虛擬機器，但仍然可看見 [磁碟上的 VHD 檔案。 這種行為預期嗎？

是的這是預期的行為。 因為設計這種方式︰

- 當您刪除 VM 時，不會刪除 Vhd。 磁碟的 [資源] 群組中的個別資源。
- 儲存帳戶取得刪除時，刪除顯示立即透過 Azure 資源管理員 （入口網站，PowerShell），但它可能包含的磁碟仍保存在儲存之前回收執行。

如果您看到 「 字串 「 Vhd，很重要事項如果是已遭刪除的儲存空間帳戶的資料夾的一部分。 如果未刪除儲存的帳戶，這是標準有依然在那裡。

您可以閱讀更多關於設定中[管理儲存空間帳戶](azure-stack-manage-storage-accounts.md)的保留閥值。

## <a name="installation-steps"></a>安裝步驟
可能適合疑難排解 Azure 堆疊安裝步驟的下列資訊︰  

| 索引 | 名稱 | 描述|
| ----- | ----- | -----|
|0.11 | (DEP)驗證的實體機器 | 驗證硬體與作業系統上實體節點的設定。 |
| 0.12 | (DEP)設定 POC 的實體機器網路 | 設定虛擬網路的切換參數與介面。 |
| 0.14 | (DEP)部署網域 | 部署 Active Directory 虛擬機器上。 |
| 0.15 | (DEP)設定網域伺服器 | 使用安全性群組等來設定網域伺服器。 |
| 將 0.16 | (DEP)設定實體機器 | 設定網路、 加入網域及設定本機系統管理員。 |
| 0.18 | (STO)設定儲存叢集 | 建立儲存叢集，建立儲存集區和檔案伺服器。 |
| 0.19 | (CPI)設定布料的轉印圖樣基礎結構 | 設定布料的轉印圖樣部署的先決條件。 |
| 0.21 | （網路）設定 BGP 及 NAT | 安裝 BGP 及 NAT-需要僅適用於一個節點。 |
| 0.22 | （網路）設定 NAT 與時間伺服器 | 同步處理的時間伺服器，並設定 NAT 項目。 |
| 40.41 | (CPI)建立來賓 Vm | 建立管理 Vm。 |
| 40.42 | （FBI 機關）設定 PowerShell JEA | 設定 PowerShell JEA 所有角色。 |
| 40.43 | （FBI 機關）設定 Azure 堆疊憑證授權單位 | 安裝 Azure 堆疊憑證授權單位。 |
| 40.44 | （FBI 機關）設定 Azure 堆疊憑證授權單位 | 設定 Azure 堆疊憑證授權單位。 |
| 40.45 | （網路）設定上 Vm NC | 安裝 NC 來賓 Vm 上 |
| 40.46 | （網路）設定 Vm NC | 設定來賓 Vm NC |
| 40.47 | （網路）設定來賓 Vm | 設定 NC Acl 管理 Vm。 |
| 60.61.81 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打服務-FabricRing 必要條件 | 建立 FabricRing 的 Vip |
| 60.61.82 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打服務-部署布料的轉印圖樣撥打叢集 | 安裝和設定 Azure 堆疊布料的轉印圖樣撥打叢集。 |
| 60.61.83 | （FBI 機關）部署資源提供者的管理員副檔名 | 在針對各個資源安裝管理員擴充功能 |
| 60.61.84 | (ACS)在節點層級設定 Azure 一致的儲存空間。 | 安裝和設定 Azure 一致的儲存空間中節點層級。 |
| 60.61.85 | (ACS)在 [叢集層級設定 Azure 一致的儲存空間。 | 安裝和設定叢集層級中的 Azure 一致的儲存空間。 |
| 60.61.86 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | InfraServiceController 的先決條件 |
| 60.61.87 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | CPI 的先決條件 |
| 60.61.88 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | ASAppGateway 的先決條件 |
| 60.61.89 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | 儲存控制站的先決條件 |
| 60.61.90 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | HealthMonitoring 的先決條件 |
| 60.61.91 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | ECE 的先決條件 |
| 60.61.92 | （FBI 機關）部署 Azure 堆疊布料的轉印圖樣撥打控制器服務-必要條件 | PMM 的先決條件 |
| 60.61.93 | (Katal)建立 AzureStack 服務原則 | 在 AAD 建立 Azure 圖形應用程式與服務原則。 |
| 60.61.94 | （網路）設定 GW Vm | 安裝 GW 來賓 Vm 上。 |
| 60.61.95 | （網路）設定 GW Vm | 設定來賓 Vm GW。 |
| 60.61.96 | （網路）部署 Idn 主機上 | 部署 Idn 基礎結構主機上 |
| 60.61.97 | （網路）設定 Idn | 設定 Idn 角色 |
| 60.61.98 | （FBI 機關）設定 WSUS Vm | 安裝 WSUS 伺服器上來賓 Vm。 |
| 60.61.99 | （FBI 機關）設定 WSUS Vm | 設定來賓 Vm WSUS 伺服器。 |
| 60.61.100 | （FBI 機關）設定 SQL Azure Vm | 安裝 Azure SQL server，內部來賓 Vm |
| 60.61.101 | (Katal)設定已 Vm 的必要條件。 | 設定 Microsoft Azure 來賓 Vm 堆疊的先決條件。 |
| 60.61.102 | (Katal)安裝程式 Vm | 安裝 Microsoft Azure 堆疊來賓 Vm 上。 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.121 | （FBI 機關）部署資源提供者和控制站 | 安裝資源提供者和控制站 |
| 60.120.122 | （FBI 機關）控制站設定 | 設定控制站 |
| 60.120.123 | (Katal)設定 WAS Vm | 設定 Microsoft Azure 堆疊來賓 Vm。 |
| 60.120.124 | (Katal)Azure 堆疊 AAD 設定。 | Azure AD 設定 Azure 堆疊。 |
| 60.120.125 | (Katal)安裝 ADFS | 安裝 Active Directory Federation Services (ADFS) |
| 60.120.126 | (Katal)安裝 ADFS/圖表 | 安裝 Azure 堆疊圖形 |
| 60.120.127 | (Katal)設定 ADFS | 設定 Active Directory Federation Services (ADFS) |
| 60.140.141 | （FBI 機關）設定 SRP | 設定儲存資源提供者 |
| 60.140.142 | (ACS)設定 Azure 一致的儲存空間。 | 設定 Azure 一致的儲存空間。 |
| 60.140.143 | （FBI 機關）建立儲存帳戶 | 建立不同的提供者所使用的所有儲存帳戶。 |
| 60.140.144 | （FBI 機關）註冊 SRP 的使用方式 | 註冊儲存提供者的使用方式。 |
| 60.140.145 | (CPI)將建立的 Vm、 主機及叢集移轉到 CPI | CPI 要移轉的建立 Vm、 主機及叢集的物件 |
| 60.140.146 | （FBI 機關）設定 Windows Defender | 設定 Windows Defender |
| 60.160.161 | （週一）設定監視代理程式 | 設定監控代理程式 |
| 60.160.162 | （FBI 機關）NRP 必要條件 | 安裝 NRP 先決條件 |
| 60.160.163 | （FBI 機關）NRP 部署 | 安裝 NRP  |
| 60.160.164 | （FBI 機關）NRP 設定 | 設定 NRP |
| 60.160.165 | （FBI 機關）CRP 必要條件 | 安裝 CRP 先決條件 |
| 60.160.166 | （FBI 機關）CRP 部署 | 安裝 CRP |
| 60.160.167 | （FBI 機關）CRP 設定 | 設定 CRP |
| 60.160.168 | （FBI 機關）FRP 必要條件 | 安裝 FRP 先決條件 |
| 60.160.169 | （FBI 機關）FRP 部署 | 安裝 FRP  |
| 60.160.170 | （FBI 機關）FRP 設定 | 設定 FRP |
| 60.160.174 | （FBI 機關）URP 必要 | 安裝 URP 先決條件 |
| 60.160.175 | （FBI 機關）URP 部署 | 安裝 URP  |
| 60.160.176 | （FBI 機關）URP 設定 | 設定 URP |
| 60.160.171 | （FBI 機關）HRP 必要條件 | 安裝 HRP 先決條件 |
| 60.160.172 | （FBI 機關）HRP 部署 | 安裝 HRP  |
| 60.160.173 | （FBI 機關）HRP 設定 | 設定 HRP |
| 60.160.177 | (KV)KeyVault 必要條件 | 安裝 KeyVault 先決條件 |
| 60.160.178 | (KV)KeyVault 部署 | 安裝 KeyVault  |
| 60.160.179 | (KV)KeyVault 設定 | 設定 KeyVault | 
| 60.190.191 | （FBI 機關）設定樣式庫 | 設定樣式庫 |
| 60.190.192 | （FBI 機關）設定布料的轉印圖樣撥打服務 | 設定布料的轉印圖樣撥打服務 |
| 60.221 | （FBI 機關）設定主控台 Vm | 安裝主控台來賓 Vm 伺服器。 |
| 60.222 | （FBI 機關）設定主控台 Vm | 將 DVM 內容移到主控台 VM。 |
| 251 | 準備的未來主機重新啟動 | 設定重新開機原則 |


## <a name="next-steps"></a>後續步驟

[常見問題集](azure-stack-FAQ.md)
