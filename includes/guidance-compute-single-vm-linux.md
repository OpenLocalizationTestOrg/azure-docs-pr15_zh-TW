本文概述證明作法執行 Linux 虛擬機器 (VM) 一組上 Azure 注意延展性、 可用性、 管理能力及安全性。 Azure 支援執行各種常用的 Linux 散佈，包括 CentOS Debian、 紅色角色企業、 Ubuntu，與 FreeBSD。 如需詳細資訊，請參閱[Azure 和 Linux][azure-linux]。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 本文所用的資源管理員，Microsoft 建議新的部署。

不建議使用生產負載單一 VM 因為單一 Vm Azure 上沒有向上時間服務等級協定 (SLA)。 若要取得 SLA，您必須部署[可用性設定]中的多個 Vm[availability-set]。 如需詳細資訊，請參閱[執行多個 Vm 上 Azure][multi-vm]。 

## <a name="architecture-diagram"></a>架構圖表

佈建 VM Azure 中的包含多變動比只 VM 本身。 有您需要考慮的計算、 網路連線和儲存的項目。

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是在 「 計算-單一 VM 」] 頁面。

![[0]][0]

- **資源群組。** [_資源群組_][resource-manager-overview]會保留相關的資源的容器。 建立資源群組，為此 VM 保留資源。

- **VM**. 您可以提供 VM 清單中的已發佈的圖像，或從您上傳至 Azure blob 儲存體虛擬硬碟 (VHD) 檔案。

- **OS 磁碟。** OS 磁碟是儲存在[Azure 儲存體]VHD[azure-storage]。 這表示它保存即使主機機器當機。 OS 磁碟`/dev/sda1`。

- **暫時磁碟。** 建立 VM 暫存的磁碟。 此磁碟會儲存在主機上實體磁碟機上。 則_不_儲存在 Azure 儲存空間，而且可能會消失期間重新啟動和其他 VM 生命週期的事件。 使用這個磁碟僅適用於暫時性的資料，例如頁面或交換檔案。 暫時磁碟`/dev/sdb1`並且裝載在`/mnt/resource`或`/mnt`。

- **資料磁碟。** [資料磁碟][data-disk]是常設 VHD 用於應用程式的資料。 資料磁碟會儲存在 Azure 儲存空間，例如 OS 磁碟。

- **虛擬網路 (VNet) 和子網路。** Azure 中的每個 VM 部署到 (VNet)，然後再分成子網路。

- **公用 IP 位址。** 公用 IP 位址，需要進行通訊的 VM&mdash;，例如透過 SSH。

- **網路介面 (NIC)**。 在 NIC 可讓使用者進行通訊的虛擬網路 VM。

- **網路安全性群組 (NSG)**。 [NSG] [nsg]用來允許/拒絕子網路流量。 您可以在個別的 NIC 或子網路關聯 NSG。 如果您將它與關聯子網路，NSG 規則套用至子網路中的所有 Vm。
 
- **診斷。** 診斷記錄是管理及疑難排解 VM 很重要。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇，建立您自己參考架構，您應該遵循上述建議，除非您有建議不符合特定需求。 

### <a name="vm-recommendations"></a>VM 建議

我們建議您 DS 和 GS 數列，因為這些機器大小支援[進階版儲存][premium-storage]。 除非您有特殊的工作量，例如高效能運算，請選取這些機器大小的其中一個選項。 如需詳細資訊，請參閱[虛擬機器大小][virtual-machine-sizes]。 將現有的工作量移至 Azure 時，開頭是最符合您的內部部署伺服器虛擬記憶體大小。 然後測量您指派 CPU 的實際工作負載的效能、 記憶體和磁碟輸入輸出秒 (IOPS)，作業，如有需要請調整大小。 此外，如果您需要多個 Nic，請注意的每個大小的 NIC 限制。  

當您佈建 VM 及其他資源時，您必須指定的位置。 一般而言，選擇您的內部的使用者或客戶最接近的位置。 不過，並非所有 VM 大小可能都會出現在所有的位置。 如需詳細資訊，請參閱[依地區的服務][services-by-region]。 若要可用 VM 大小在特定位置] 清單中，執行下列 Azure 命令列介面 (CLI) 命令︰

```
    azure vm sizes --location <location>
```

選擇發佈的 VM 圖像的相關資訊，請參閱[瀏覽並選取 Azure 虛擬機器圖像][select-vm-image]。

### <a name="disk-and-storage-recommendations"></a>磁碟，儲存空間的建議

為獲得最佳的磁碟 I/O 效能，建議您[進階版儲存][premium-storage]，其中 solid-state 磁碟機 (SSDs) 儲存資料。 成本為基礎能夠磁碟的大小。 IOPS 和處理量 （也就是資料傳輸工資率） 也相依磁碟大小，因此當您佈建光碟，請考慮 （容量、 IOPS，以及處理量） 的所有三個因素。 

儲存帳戶可支援 1 到 20 Vm。

新增一或多個資料磁碟。 當您建立 VHD 時，則格式化。 若要設定格式的磁碟 vm 登入。 資料磁碟顯示為`/dev/sdc`， `/dev/sdd`，依此類推。 您可以執行`lsblk`清單封鎖裝置，包括磁碟。 若要使用資料的磁碟分割區與檔案系統，並裝載磁碟。 例如︰

```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
```

如果您有許多資料磁碟，請注意的總 I/O 限制的儲存空間帳戶。 如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。

當您新增資料磁碟時，邏輯單位數字 (LUN) 識別碼指派到磁碟。 或者，您可以在其中指定 LUN 識別碼&mdash;比方說，如果您要取代磁碟，並且想要保留相同的 LUN 識別碼，或您有應用程式來尋找特定的 LUN 識別碼。 不過，請記得 LUN 識別碼必須是唯一的每個磁碟。

若要變更 I/O 排程器，若要最佳化效能 solid-state 磁碟機上 (SSDs) （使用進階版儲存）。 常見的建議使用 SSDs NOOP 排程器，但您應該使用的工具，例如[iostat]來監控您的特定工作負載的磁碟 I/O 效能。

- 為獲得最佳效能，建立個別的儲存空間帳戶儲存診斷記錄。 標準的本機多餘的儲存空間 (LRS) 帳戶不足，診斷記錄。


### <a name="network-recommendations"></a>網路建議

動態或靜態，可使用的公用 IP 位址。 預設為動態。

- 保留的[靜態 IP 位址][static-ip]如果您需要的固定的 IP 位址的並不會變更&mdash;比方說，如果您需要在 [DNS] 建立 A 記錄或需要的 IP 位址是 whitelisted。

- 您也可以建立的 IP 位址的完整的網域名稱 (FQDN)。 然後，您可以註冊[CNAME 記錄][ cname-record] dns，指向 [FQDN。 如需詳細資訊，請參閱[建立完整網域名稱 Azure 入口網站中][fqdn]。

所有 NSGs 都包含一組[預設規則][nsg-default-rules]，包括封鎖所有輸入的網際網路流量的規則。 您無法刪除預設的規則，但其他規則可以覆寫。 若要啟用網際網路流量，建立規則，允許特定的連接埠輸入的流量&mdash;，例如 http 連接埠 80。  

若要啟用 SSH，可讓輸入的流量的 TCP 連接埠 22 NSG 新增規則。

## <a name="scalability-considerations"></a>延展性考量

您可以藉由[變更虛擬記憶體大小]調整 VM 向上或向下[vm-resize]。 

水平擴充，請將兩個或多個 Vm 放可用性後方負載平衡器設定。 如需詳細資訊，請參閱[執行多個 Vm 上 Azure][multi-vm]。

## <a name="availability-considerations"></a>可用性考量

如上所述，有單一 vm 沒有 SLA。 若要取得 SLA，您必須將多個 Vm 部署到可用性設定。

可能會影響您 VM[預定進行的維修作業][planned-maintenance]或[非預期的維護][manage-vm-availability]。 您可以使用[VM 重新啟動記錄][reboot-logs]來決定是否預定進行的維護所造成 VM 重新啟動電腦。

[Azure]儲存備份 Vhd[azure-storage]，其中複寫持續性及可用性。

若要防止意外的資料遺失標準作業時 （例如，因為使用者錯誤），您也應該實作時間點備份，使用[blob 快照][blob-snapshot]或其他工具。

## <a name="manageability-considerations"></a>管理能力考量

**資源群組。** 將放到相同的[資源群組]共用相同的生命週期的緊密結合的資源[resource-manager-overview]。 資源群組可讓您部署和監視資源] 群組中，為縮合計費成本依資源群組。 您也可以刪除資源的集合，這是測試部署非常有用。 授與資源有意義的名稱。 可輕鬆找出特定的資源，並了解角色。 請參閱[Azure 資源的建議的命名慣例][naming conventions]。

**SSH**。 建立 Linux VM 之前，產生 2048年位元 RSA 公用私人組。 當您建立 VM 時，請使用公開金鑰檔案。 如需詳細資訊，請參閱[如何使用 SSH Linux 與 Mac 上 Azure][ssh-linux]。

**VM 診斷。** 啟用監控和診斷，包括基本的健康情況的指標、 診斷基礎結構的記錄及[開機診斷][boot-diagnostics]。 啟動診斷可協助您診斷開機失敗，如果您 VM 進入非開機的狀態。 如需詳細資訊，請參閱[啟用監控和診斷][enable-monitoring]。  

下列 CLI 命令可讓診斷︰

```
    azure vm enable-diag <resource-group> <vm-name>
```

**停止 VM。** Azure 可讓您區別 「 已停止 」 和 「 Deallocated 」 狀態。 您會時 VM 狀態 「 已停止 」。 您不會時 VM 解除配置。

使用下列 CLI 命令解除 VM:

```
    azure vm deallocate <resource-group> <vm-name>
```

- Azure 入口網站中的 [**停止**] 按鈕也解除配置 VM。 不過，如果您關閉透過 OS 時登入，VM 已停止，但_不是_會解除配置，因此您仍需要付費。

**刪除 VM。** 如果您刪除 VM，不會刪除 Vhd。 這表示您安全地可以刪除 VM，而不遺失資料。 不過，您仍然需要付費儲存空間。 若要刪除 VHD，請從[blob 儲存體]中刪除檔案[blob-storage]。

若要防止不慎刪除，請使用 [[資源鎖定][resource-lock]鎖定整個資源群組] 或 [鎖定個別資源，例如 VM。 

## <a name="security-considerations"></a>安全性考量

藉由使用[OSPatching] VM 副檔名自動化 OS 更新。 當您佈建 VM 時，請安裝至此分機]。 您可以指定要多久安裝修補程式，以及是否要重新啟動後修補。

使用[角色型存取控制][ rbac] (RBAC) 來控制您部署 Azure 資源的存取權。 RBAC 可讓您將授權角色指派給您 DevOps 的小組成員。 例如讀取者角色可以檢視 Azure 資源，但不是建立、 管理，或將它們刪除。 某些角色是針對特定 Azure 的資源類型。 例如的虛擬機器參與者角色可以重新啟動或解除 VM、 重設管理員密碼、 建立 VM，等等。 其他[內建 RBAC 角色][rbac-roles]此參照架構包含[DevTest 實驗室使用者]可能會有幫助[rbac-devtest]和[網路參與者][rbac-network]。 

使用者可以指派給多個的角色，而您可以建立自訂的 [更多微調權限的角色。

> [AZURE.NOTE] RBAC 就不會限制 VM 登入的使用者可以執行的動作。 取決於這些權限 OS 來賓帳戶類型。   

使用 [[稽核記錄][audit-logs]若要查看佈建動作及其他 VM 事件。

- 請考慮[Azure 磁碟加密][disk-encryption]如果您需要加密 OS 和資料磁碟。 

## <a name="solution-deployment"></a>解決方案部署

[部署][github-folder]參照示範這些最佳做法的架構，則會出現。 此參考架構包含虛擬網路 (VNet)、 網路安全性群組 (NSG) 及單一虛擬機器 (VM)。

有數種方式可部署此參照架構。 最簡單的方法是依照下列步驟︰ 

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」。
[![部署至 Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. 一旦連結已開啟 Azure 入口網站中，您必須輸入值的部分設定︰ 
    - **資源群組**名稱已經定義參數檔案中，因此**建立新檔案**，然後選取輸入`ra-single-vm-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 從下拉式清單方塊， **linux**選取**Os 類型**。
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

4. 參數檔案包含的硬式編碼管理員的使用者名稱和密碼，然後強烈建議您立即變更兩者。 按一下名為 VM `ra-single-vm0 `Azure 入口網站中。 然後按一下 [**支援 + 疑難排解**] 區段中的 [**重設密碼**。 在 [**模式**] 下拉式清單方塊中，選取 [**重設密碼**，然後選取新的**使用者名稱**和**密碼**。 按一下 [**更新**] 按鈕，將新的使用者名稱和密碼。

部署此參照架構的其他方式的資訊，請參閱在[指南-單一-vm]的讀我檔案[github-folder]Github 資料夾。

## <a name="customize-the-deployment"></a>自訂部署

若要變更以符合您需求的部署，請依照下列[指示-單一-vm]中的指示進行[github-folder]頁面。

## <a name="next-steps"></a>後續步驟

[SLA 虛擬機器]中[vm-sla]若要套用，您必須部署中的可用性設定的兩個或多個執行個體。 如需詳細資訊，請參閱[執行多個 Vm 上 Azure][multi-vm]。

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure 中的單一 Linux VM 架構"

