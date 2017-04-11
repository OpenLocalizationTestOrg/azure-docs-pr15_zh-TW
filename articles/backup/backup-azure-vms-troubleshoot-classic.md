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

## <a name="discovery"></a>探索

| 備份作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 探索 | 無法探索新的項目-Microsoft Azure 備份發生內部錯誤。 等待幾分鐘，然後再試一次。 | 在 15 分鐘後重新探索程序。
| 探索 | 無法找到新的項目 – 探索其他操作已在進行中。 請在目前的探索作業完成，等候。 | 無 |

## <a name="register"></a>註冊
| 備份作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 註冊 | 虛擬機器資料磁碟數目超出支援的上限-請卸離此虛擬機器上的一些資料磁碟，然後再試。 Azure 備份支援到 16 的資料磁碟連接到備份 Azure 虛擬機器 | 無 |
| 註冊 | Microsoft Azure 備份發生內部錯誤-等待幾分鐘的時間並再試一次。 如果問題持續發生，請連絡 Microsoft 支援服務。 | 您可以取得此錯誤，因為其中一項動作不受支援的 VM 設定進階版 LRS 上。 <br> 進階版儲存 Vm 備份可以使用復原服務保存庫。 [深入瞭解](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| 註冊 | 註冊失敗，安裝代理程式作業逾時 | 核取 [是否支援虛擬機器的作業系統版本]。 |
| 註冊 | Command 執行失敗-另一項作業正在進行此項目。 請前一個作業完成後，等候 | 無 |
| 註冊 | 無法儲存在進階版儲存空間的虛擬硬碟的虛擬機器不支援的備份 | 無 |
| 註冊 | 虛擬機器代理程式未顯示在虛擬機器上-請安裝必要的必要條件，VM 代理程式並重新啟動作業。 | [閱讀更多](#vm-agent)VM 代理程式的安裝，以及如何驗證 VM 代理程式安裝的相關資訊。 |

## <a name="backup"></a>備份

| 備份作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 備份 | 無法通訊與 VM 代理程式的快照集狀態。 快照 VM 子任務逾時。 -請請參閱如何解決這個問題的疑難排解指南。 | 此錯誤會擲回 VM 代理程式的問題，或網路存取 Azure 基礎結構遭到封鎖方式。 進一步瞭解[偵錯 VM 快照設定問題](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)。 <br> 如果 VM 代理程式不會導致任何問題，然後重新啟動 VM。 有些時候不正確的 VM 狀態可能會導致問題，並重新啟動 VM 重設此 「 問題 」 |
| 備份 | 備份失敗，發生內部錯誤-請試幾分鐘的時間。 如果問題持續發生，請連絡 Microsoft 支援服務 | 請檢查存取 VM 儲存空間是否的暫時性問題。 請檢查有任何相關計算/儲存/網路區域中的進行問題的[Azure 狀態](https://azure.microsoft.com/en-us/status/)。 請降低重試備份張貼問題。 |
| 備份 | 為 VM 不存在，則無法執行的作業。 | 無法執行備份，因為 VM 設定備份已被刪除。 請移至 [受保護的項目] 檢視，請停止其他備份選取受保護的項目，然後按一下 [停止保護。 選取 [保留備份資料選項，您可以保留資料。 針對此虛擬機器，即可在設定登錄項目] 檢視中的 [保護，您可以稍後恢復保護|
| 備份 | 無法安裝 Azure 修復服務上選取的項目-VM 代理程式的副檔名是 Azure 復原服務延伸的必要條件。 請安裝 Azure VM 代理程式並重新啟動的註冊作業 | <ol> <li>檢查 VM 代理程式安裝是否正確。 <li>確認 VM 設定的旗標設定正確。</ol> [閱讀更多](#validating-vm-agent-installation)VM 代理程式的安裝，以及如何驗證 VM 代理程式安裝的相關資訊。 |
| 備份 | Command 執行失敗-另一項作業目前正在進行此項目上。 請等候前一個作業完成後，然後再試一次 | 在現有的備份或還原工作 vm 正在執行，並執行現有的工作時，就無法啟動新的工作。 |
| 備份 | 副檔名安裝失敗，發生錯誤 」 COM + 無法與 Microsoft 分散式交易協調器 | 通常這表示 COM + 服務不在執行。 如需修正此問題的說明，請連絡 Microsoft 支援服務。 |
| 備份 | 快照作業失敗，VSS 操作錯誤 」 的磁碟機 BitLocker 加密已鎖定此磁碟機。 您必須解除鎖定此磁碟機，從 [控制台]。 | 關閉所有的磁碟機上 VM BitLocker 並觀察是否 VSS 問題已解決 |
| 備份 | 無法儲存在進階版儲存空間的虛擬硬碟的虛擬機器不支援的備份 | 無 |
| 備份 | 找不到 azure 虛擬機器。 | 會發生這種情況時的主要 VM 會刪除，但是備份的原則仍會繼續執行備份 VM 尋找。 若要修正此錯誤︰ <ol><li>重新建立相同的名稱和相同的資源群組名稱 [雲端服務名稱] 的虛擬機器 <br>（或） <li> 停用此 VM 的保護，所以不取得觸發後續的備份。 </ol> |
| 備份 | 虛擬機器代理程式未顯示在虛擬機器上-請安裝必要的必要條件，VM 代理程式並重新啟動作業。 | [閱讀更多](#vm-agent)VM 代理程式的安裝，以及如何驗證 VM 代理程式安裝的相關資訊。 |

## <a name="jobs"></a>工作
| 作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 取消工作 | 此工作類型不支援取消-請等到完成作業。 | 無 |
| 取消工作 | 工作沒有可取消] 狀態-請等到完成作業。 <br>OR<br> 所選取的工作可取消狀態不-請稍待完成的工作。| 幾乎都完成工作;請等到完成作業 |
| 取消工作 | 無法取消工作，因為它不是在進行-取消，才支援在進行中的工作。 請嘗試取消進行中的工作。 | 這是由暫時性的狀態。 等待幾分鐘，然後再試一次取消作業 |
| 取消工作 | 無法取消工作-工作完成，請等候。 | 無 |


## <a name="restore"></a>還原
| 作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 還原 | 還原失敗雲端內部錯誤 | <ol><li>您嘗試要還原的雲端服務設定 DNS 設定。 您可以檢查 <br>$deployment = 取得 AzureDeployment ServiceName 」 ServiceName 」 的位置 」 實際執行 「 取得 AzureDns-DnsSettings $deployment。DnsSettings<br>如果沒有設定的地址，這表示，設定 DNS 設定。<br> <li>ReservedIP 設定要還原的雲端服務，與現有 Vm 雲端服務中的已停用狀態。<br>您可以檢查在雲端服務有一些 IP 保留使用下列 powershell 指令程式︰<br>$deployment = 取得 AzureDeployment ServiceName 」 servicename 」-位置 「 產品 」 $ 相依性ReservedIPName <br><li>您嘗試將虛擬機器中的下列特殊的網路設定還原為相同的雲端服務。 <br>的負載平衡器設定 （內部和外部）] 底下虛擬機器<br>-使用多個保留 IPs 虛擬機器<br>-使用多個 Nic 虛擬機器<br>請選取 [使用者介面中的 [新的雲端服務，或請參閱[還原考量](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations)的 Vm 用於特殊網路設定</ol> |
| 還原 | 選取的 DNS 名稱已經存在-請指定不同的 DNS 名稱，然後再試一次。 | DNS 名稱參照到雲端服務名稱 (通常以。 cloudapp.net)。 必須是唯一的。 如果發生此錯誤，您需要還原時選擇不同的 VM 名稱。 <br><br> Azure 入口網站的使用者只會顯示這個錯誤。 透過 PowerShell 還原作業成功，因為它只會還原磁碟並不會建立 VM。 磁碟還原作業之後，您明確建立 VM 時，會遇到錯誤。 |
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





