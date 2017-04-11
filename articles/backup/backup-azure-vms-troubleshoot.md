<properties
    pageTitle="疑難排解 Azure 虛擬機器備份 |Microsoft Azure"
    description="疑難排解備份與還原 Azure 虛擬機器"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>疑難排解 Azure 虛擬機器備份

> [AZURE.SELECTOR]
- [復原服務保存庫](backup-azure-vms-troubleshoot.md)
- [備份保存庫](backup-azure-vms-troubleshoot-classic.md)

您可以疑難排解 Azure 備份使用資訊下表列出時發生的錯誤。

## <a name="backup"></a>備份

| 備份作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
|備份|    為 VM 不存在，則無法執行的作業。 -停止保護虛擬機器，但不刪除備份資料。 在 http://go.microsoft.com/fwlink/?LinkId=808124 的更多詳細資料   |會發生這種情況時的主要 VM 會刪除，但是備份的原則仍會繼續執行備份 VM 尋找。 若要修正此錯誤︰ <ol><li> 重新建立相同的名稱和相同的資源群組名稱 [雲端服務名稱] 的虛擬機器<br>（或）</li><li> [停止保護虛擬機器包含或不含刪除備份資料。 [更多詳細資料](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|備份|無法通訊與 VM 代理程式的快照集狀態。 -確認 VM 具有存取網際網路。 此外，在 http://go.microsoft.com/fwlink/?LinkId=800034 疑難排解指南所述，更新 VM 代理程式 |此錯誤會擲回 VM 代理程式的問題，或網路存取 Azure 基礎結構遭到封鎖方式。 瞭解更多關於設定 VM 偵錯快照集問題。<br> 如果 VM 代理程式不會導致任何問題，然後重新啟動 VM。 有些時候不正確的 VM 狀態可能會導致問題，並重新啟動 VM 重設此 「 問題 」|
|備份|    復原服務副檔名作業失敗。 -請請務必最新的虛擬機器代理程式虛擬機器出現，並執行代理程式服務。 請再試一次備份作業，然後如果失敗，請連絡 Microsoft 支援服務。|   此錯誤會擲回 VM 代理程式已過期。 請參考下列更新 VM 代理程式的 「 更新 VM 代理程式 」 一節。|
|備份 |虛擬機器不存在。 -請確認該虛擬機器存在，或選取不同的虛擬機器。 | 會發生這種情況時的主要 VM 會刪除，但是備份的原則仍會繼續執行備份 VM 尋找。 若要修正此錯誤︰ <ol><li> 重新建立相同的名稱和相同的資源群組名稱 [雲端服務名稱] 的虛擬機器<br>（或）<br></li><li>[停止保護虛擬機器，但不刪除備份資料。 [更多詳細資料](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|備份 |無法執行命令。 -另一項作業正在進行此項目。 請等候前一個作業完成後，然後再試一次 |在現有的備份或還原工作 vm 正在執行，並執行現有的工作時，就無法啟動新的工作。|
| 備份 | 複製 Vhd 從備份保存庫逾-請試一次在幾分鐘。 如果問題持續發生，請連絡 Microsoft 支援服務。 | 會發生這種情況時有太多要複製的資料。 請檢查您是否小於 16 資料磁碟。 |
| 備份 | 備份失敗，發生內部錯誤-請試幾分鐘的時間。 如果問題持續發生，請連絡 Microsoft 支援服務 | 2 的原因，您可以取得這個錯誤︰ <ol><li> 存取 VM 存放裝置有的暫時性問題。 請檢查有任何相關計算/儲存/網路區域中的進行問題的[Azure 狀態](https://azure.microsoft.com/en-us/status/)。 請降低重試備份張貼問題。 <li>原始 VM 刪除的郵件，因此無法取得備份。 若要刪除 vm 保留備份資料，但停止備份錯誤，取消保護 VM 並選擇要保留的資料的選項。 這會停止備份的排程，同時週期性的錯誤訊息。 |
| 備份 | 無法安裝 Azure 修復服務上選取的項目-VM 代理程式的副檔名是 Azure 復原服務延伸的必要條件。 請安裝 Azure VM 代理程式並重新啟動的註冊作業 | <ol> <li>檢查 VM 代理程式安裝是否正確。 <li>確認 VM 設定的旗標設定正確。</ol> [閱讀更多](#validating-vm-agent-installation)VM 代理程式的安裝，以及如何驗證 VM 代理程式安裝的相關資訊。 |
| 備份 | 副檔名安裝失敗，發生錯誤 」 COM + 無法與 Microsoft 分散式交易協調器 | 通常這表示 COM + 服務不在執行。 如需修正此問題的說明，請連絡 Microsoft 支援服務。 |
| 備份 | 快照作業失敗，VSS 操作錯誤 」 的磁碟機 BitLocker 加密已鎖定此磁碟機。 您必須解除鎖定此磁碟機，從 [控制台]。 | 關閉所有的磁碟機上 VM BitLocker 並觀察是否 VSS 問題已解決 |
| 備份 | 無法儲存在進階版儲存空間的虛擬硬碟的虛擬機器不支援的備份 | 無 |
| 備份 | 找不到 azure 虛擬機器。 | 會發生這種情況時的主要 VM 會刪除，但是備份的原則仍會繼續執行備份 VM 尋找。 若要修正此錯誤︰ <ol><li>重新建立相同的名稱和相同的資源群組名稱 [雲端服務名稱] 的虛擬機器 <br>（或） <li> 停用此 VM 的保護，以便將不會建立備份工作 </ol> |
| 備份 | 虛擬機器代理程式未顯示在虛擬機器上-請安裝必要的必要條件，VM 代理程式並重新啟動作業。 | [閱讀更多](#vm-agent)VM 代理程式的安裝，以及如何驗證 VM 代理程式安裝的相關資訊。 |

## <a name="jobs"></a>工作

| 作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 取消工作 | 此工作類型不支援取消-請等到完成作業。 | 無 |
| 取消工作 | 工作沒有可取消] 狀態-請等到完成作業。 <br>OR<br> 所選取的工作可取消狀態不-請稍待完成的工作。| 幾乎都完成工作;請等到完成作業 |
| 取消工作 | 無法取消工作，因為它不是在進行-取消，才支援在進行中的工作。 請嘗試取消進行中的工作。 | 這是由暫時性的狀態。 等待幾分鐘，然後再試一次取消作業 |
| 取消工作 | 無法取消工作-請先等工作完成。 | 無 |


## <a name="restore"></a>還原
| 作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 還原 | 還原失敗雲端內部錯誤 | <ol><li>您嘗試要還原的雲端服務設定 DNS 設定。 您可以檢查 <br>$deployment = 取得 AzureDeployment ServiceName 」 ServiceName 」 的位置 」 實際執行 「 取得 AzureDns-DnsSettings $deployment。DnsSettings<br>如果沒有設定的地址，這表示，設定 DNS 設定。<br> <li>ReservedIP 設定要還原的雲端服務，與現有 Vm 雲端服務中的已停用狀態。<br>您可以檢查在雲端服務有一些 IP 保留使用下列 powershell 指令程式︰<br>$deployment = 取得 AzureDeployment ServiceName 」 servicename 」-位置 「 產品 」 $ 相依性ReservedIPName <br><li>您嘗試將虛擬機器中的下列特殊的網路設定還原為相同的雲端服務。 <br>的負載平衡器設定 （內部和外部）] 底下虛擬機器<br>-使用多個保留 IPs 虛擬機器<br>-使用多個 Nic 虛擬機器<br>請選取 [使用者介面中的 [新的雲端服務，或請參閱[還原考量](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations)的 Vm 用於特殊網路設定</ol> |
| 還原 | 選取的 DNS 名稱已經存在-請指定不同的 DNS 名稱，然後再試一次。 | DNS 名稱參照到雲端服務名稱 (通常以。 cloudapp.net)。 必須是唯一的。 如果發生此錯誤，您需要還原時選擇不同的 VM 名稱。 <br><br> 請注意，這項錯誤只顯示 Azure 入口網站的使用者。 透過 PowerShell 還原操作會失敗，因為它只會還原磁碟並不會建立 VM。 磁碟還原作業之後，您明確建立 VM 時，會遇到錯誤。 |
| 還原 | 指定的虛擬網路設定不正確-請指定不同的虛擬網路設定並再試一次。 | 無 |
| 還原 | 指定的雲端服務使用的保留的 IP 不符合要還原的虛擬機器設定-請指定不同的雲端服務的不使用保留的 IP，或選擇另一個復原點從還原。 | 無 |
| 還原 | 雲端服務已達到輸入結束點的數目的限制-試一次指定不同的雲端服務，或使用現有的端點。 | 無 |
| 還原 | 備份保存庫和目標儲存帳戶在兩個不同的地區-確保還原作業中指定的儲存空間帳戶是在相同的 Azure 地區為備份保存庫。 | 無 |
| 還原 | 還原作業未指定的儲存空間帳戶支援-僅 Basic/標準儲存帳戶與本機多餘或地理重複試驗設定受支援。 請選取 [支援的儲存的帳戶 | 無 |
| 還原 | 還原作業指定的儲存空間帳戶類型不在線上-務必還原作業中指定的儲存空間帳戶處於線上狀態 | 由於在 Azure 儲存空間，或因為中斷的暫時性錯誤可能造成此問題。 請選擇其他儲存的帳戶。 |
| 還原 | 資源群組配額達到-請刪除 Azure 入口網站中的一些資源群組，或連絡 Azure 支援，以增加限制。 | 無 |
| 還原 | 選取子網路不存在-請選取 [已存在的子網路 | 無 |


## <a name="policy"></a>原則
| 作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 建立原則 | 無法建立原則，請減少保留選項繼續進行原則設定。 | 無 |


## <a name="vm-agent"></a>VM 代理程式

### <a name="setting-up-the-vm-agent"></a>設定 VM 代理程式
一般而言，VM 代理程式中已經有從 Azure] 庫中建立的 Vm。 不過，從內部部署資料中心移轉的虛擬機器沒有安裝 VM 代理程式。 這類 Vm VM 代理程式需要明確安裝。 進一步瞭解[安裝在現有的 VM VM 代理程式](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)。

針對 Windows Vm:

- 下載並安裝[代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您將需要系統管理員權限，以完成安裝。
- [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)，表示代理程式已安裝。

針對 Linux Vm:

- 從 github 安裝最新[Linux 代理程式](https://github.com/Azure/WALinuxAgent)。
- [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)，表示代理程式已安裝。


### <a name="updating-the-vm-agent"></a>更新 VM 代理程式
針對 Windows Vm:

- 更新 VM 代理程式是非常簡單，重新安裝[VM 代理程式的二進位檔案](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 不過，您必須確定沒有任何備份作業正在執行更新 VM 代理程式時。

針對 Linux Vm:

- 依[更新 Linux VM 代理程式](../virtual-machines/virtual-machines-linux-update-agent.md)的指示。


### <a name="validating-vm-agent-installation"></a>驗證 VM 代理程式的安裝
如何檢查 Windows Vm VM 代理程式版本︰

1. 登入 Azure 虛擬機器並瀏覽至資料夾*C:\WindowsAzure\Packages*。 您應該找到簡報 WaAppAgent.exe 檔案。
2. 以滑鼠右鍵按一下檔案，移至 [**屬性**]，然後選取 [**詳細資料**] 索引標籤。 產品版本] 欄位應與 2.6.1198.718 或更新版本

## <a name="troubleshoot-vm-snapshot-issues"></a>VM 快照問題進行疑難排解
快照] 命令發給基礎儲存依賴 VM 備份。 不需要快照任務執行中的 [儲存空間或延遲的存取權可能會失敗備份。 下列可能會導致快照工作失敗。

1. 使用 NSG 封鎖網路存取儲存空間<br>
   瞭解如何[啟用網路存取](backup-azure-vms-prepare.md#2-network-connectivity)來使用任一 IPs 家的儲存空間，或透過 proxy 伺服器的詳細資訊。
2.  使用 Sql Server 備份設定 Vm 可能會導致快照工作延遲 <br>
    依預設 VM 備份上 Windows Vm 問題 VSS 完整備份。 在 Vm 會執行 Sql Server 和 Sql Server 設定備份，可能會發生延遲快照集執行。 請如果您遇到備份失敗由於快照問題，設定下列登錄機碼。

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  VM 狀態報告錯誤，因為 VM 是關機 RDP 中。  <br>
    如果您已關閉虛擬機器中 RDP，請檢查入口網站中回 VM 狀態會反應正確。 請關閉 VM 在入口網站 VM 儀表板中使用 「 關機 」 的選項如果沒有的話，請關閉 VM 在入口網站 VM 儀表板中使用 「 關機 」 的選項。
4.  如果超過四個 VM 共用相同雲端服務，來設定多個階段備份的時間，因此可以不超過四個 VM 備份一次啟動的備份原則。 請試著散佈備份的開始時間向外張開之間原則小時。 
5.  VM 正在執行，大量 CPU/記憶體。<br>
    如果在高 CPU 執行的虛擬機器 usage(>90%) 或記憶體快照工作是佇列、 延遲和最後會取得逾時。 請嘗試在這種情況下指定備份。

<br>

## <a name="networking"></a>網路
所有的擴充功能，例如備份副檔名需要存取公用網際網路工作。 無法存取公用網際網路表現本身各種不同的方式︰

- 副檔名安裝可能失敗
- （例如磁碟快照） 的備份作業可能會失敗
- 顯示備份作業的狀態就會失敗

已事情需要解決公用網際網路地址[這裡](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)。 您需要檢查 VNET 的 DNS 設定，並確保 Azure Uri 可以解決。

一旦名稱解析正確完成後，存取 Azure IPs 也必須提供。 若要解除存取 Azure 基礎結構，請遵循下列步驟︰

1. WhiteList Azure 資料中心 IP 範圍。
    - 取得要 whitelisted [Azure 資料中心 Ip](https://www.microsoft.com/download/details.aspx?id=41653)的清單。
    - 解除封鎖 Ip 使用[新增 NetRoute](https://technet.microsoft.com/library/hh826148.aspx)指令程式。 提高權限 （以系統管理員身分執行） PowerShell 視窗中執行的 Azure VM，內這個指令程式。
    - 新增規則到 NSG （如果您有一個位置中） 允許 IPs 存取。
2. 建立路徑以 HTTP 的流量
    - 如果您有一些網路限制之後 （網路安全性群組，例如） 部署若要將流量路由傳送 HTTP proxy 伺服器。 您可以找到部署 HTTP Proxy 伺服器的步驟，[在這裡](backup-azure-vms-prepare.md#2-network-connectivity)。
    - 新增規則到 NSG （如果您有一個位置） 若要允許從 HTTP Proxy 存取網際網路。

>[AZURE.NOTE] 您必須啟用 DHCP 內 IaaS VM 備份工作來賓。  如果您需要靜態私人 ip 位址，您應該透過平台來進行設定。 應該左啟用 VM 內部 DHCP 選項。
檢視[設定靜態內部私人 ip 位址](../virtual-network/virtual-networks-reserved-private-ip.md)相關資訊。
