<properties
    pageTitle="支援的平台 IaaS 資源傳統至 Azure 資源管理員移轉 |Microsoft Azure"
    description="本文將引導平台支援資源的移轉，從傳統至 Azure 資源管理員"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>支援的平台的資源的移轉 IaaS 從傳統至 Azure 資源管理員

在本文中，我們會說明如何我們啟用傳統服務 (IaaS) 資源到資源管理員部署模型為基礎結構的移轉。 您可以閱讀更多關於[Azure 資源管理員功能與優點](../azure-resource-manager/resource-group-overview.md)。 我們詳細說明如何從您的訂閱中存在使用虛擬網路網站-閘道器的兩種部署模型連線資源。 

## <a name="goal-for-migration"></a>移轉的目標

資源管理員可讓部署複雜的應用程式，透過範本、 使用 VM 延伸設定虛擬機器和整合存取管理及標記。 Azure 資源管理員包含可調整、 平行部署虛擬機器成可用性組。 新的部署模型也獨立提供計算、 網路和儲存空間的生命週期的管理。 最後，預設的虛擬機器中虛擬網路強制啟用安全性有焦點。

從傳統部署模型執行幾乎所有的功能支援的計算、 網路和 Azure 資源 Manager 下的儲存空間。 若要可受益 Azure 資源管理員] 中的新功能，您可以將現有的部署移轉傳統部署模型。

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>變更您的自動化與移轉後的工具

資源管理員部署模型，從 [傳統部署模型升級您的資源的一部分，您必須更新您的現有自動化或工具，以確保其繼續移轉後工作。

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>移轉至資源管理員的 IaaS 資源從傳統的意義

我們向下切入詳細資料之前，請讓我們來看看資料-平面與管理平面 IaaS 資源作業之間的差異。

- *管理平面*說明進入管理平面或修改資源的 API 的來電。 例如，建立 VM、 重新啟動 VM，及使用新的子網路更新虛擬網路等作業管理執行的資源。 它們直接不會影響連線至執行個體。
- *資料平面*（應用程式） 說明的應用程式本身執行階段，並包含沒有幫助 Azure API 的執行個體的互動。 存取您的網站或抽取資料執行 SQL Server 執行個體或 MongoDB 伺服器會被視為資料平面或應用程式的互動。 複製 blob 儲存體帳戶和存取的公用 IP 位址 RDP 或 SSH 到虛擬機器也是資料平面。 這些作業保留執行計算、 網路連線和儲存過的應用程式。

>[AZURE.NOTE] 在某些移轉情況下，Azure 的平台停駐點，解除配置，並重新啟動您的虛擬機器。 這會因為簡短的資料平面停機時間。

## <a name="supported-scopes-of-migration"></a>支援的移轉的範圍

有三個主要目標計算、 網路和儲存空間的移轉範圍。 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>遷移虛擬機器 （不在虛擬網路）

資源管理員部署模型中的安全性會強制執行應用程式的預設。 所有 Vm 都必須虛擬網路資源管理員模型中。 Azure 平台重新啟動 (`Stop`， `Deallocate`，及`Start`) Vm 移轉的一部分。 您有虛擬網路的兩個選項︰

- 您可以要求建立新的虛擬網路，並將虛擬機器移轉至新的虛擬網路的平台。
- 您可以將虛擬機器移轉至現有的虛擬網路資源管理員] 中。

>[AZURE.NOTE] 在此移轉範圍內，同時管理平面作業和資料平面作業可能不允許一段時間移轉過程。

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>遷移虛擬機器 （在虛擬網路）

大部分的 VM 設定，為傳統與資源管理員部署模型之間移轉的中繼資料。 基礎 Vm 正在使用相同的硬體，相同的網路，並使用相同的儲存空間。 管理平面作業可能不允許的某一段時間移轉過程。 不過，資料平面仍會繼續執行。 就是您執行 Vm （傳統） 的頂端的應用程式不會造成停機時間移轉過程。

目前不支援下列設定。 如果支援新增以後，在某些 Vm 此設定可能會造成停機時間 （移到停駐點，解除配置，並重新啟動 VM 作業）。

-   您有多個設定單一雲端服務中的可用性。
-   您有一個或多個可用性集以及 Vm 未設定單一雲端服務中的可用性。

>[AZURE.NOTE] 在此移轉範圍內，管理平面可能不允許一段時間移轉過程。 某些設定先前所述，資料平面停機時間發生。

### <a name="storage-accounts-migration"></a>儲存帳戶移轉

若要允許順暢的移轉，您可以部署資源管理員 Vm 傳統的儲存空間帳戶。 這項功能，計算和網路資源可以與應該移轉亦儲存帳戶。 當您移至您的虛擬機器和虛擬網路移轉時，您需要移轉完成移轉程序您儲存帳戶。 

>[AZURE.NOTE] 資源管理員部署模型沒有傳統圖像和磁碟的概念。 當儲存帳戶移轉、 傳統的圖像和磁碟不會顯示在資源管理員堆疊但支援 Vhd 會保留在儲存帳戶。 

## <a name="unsupported-features-and-configurations"></a>不支援的功能和設定

我們目前不支援某些功能和設定。 下列各節說明我們建議。

### <a name="unsupported-features"></a>不支援的功能

目前不支援下列功能。 您可以選擇性地移除這些設定，移轉 Vm，並重新啟用資源管理員部署模型中的設定。

資源提供者 | 功能
---------- | ------------
計算 | 未關聯的虛擬機器磁碟。
計算 | 虛擬機器圖像。
網路 | 結束點 Acl。
網路 | 虛擬網路閘道器 （網站，Azure ExpressRoute，應用程式閘道指向網站）。
網路 | 使用 VNet 對等的虛擬網路。 （將 VNet 移轉到 ARM，然後對等）進一步瞭解 [VNet 對等] (.../virtual-network/virtual-network-peering-overview.md)。
網路 | 流量管理員設定檔。

### <a name="unsupported-configurations"></a>不支援的設定

目前不支援下列設定。

服務 | 設定 | 建議
---------- | ------------ | ------------
資源管理員 | 角色基礎存取控制 (RBAC) 的傳統的資源 | 因為資源的 URI 修改移轉後，建議您計劃 RBAC 原則需要的更新，在移轉之後發生。
計算 | 多個子 VM 相關聯的網路 | 更新參考只子網路的子網路設定。
計算 | 屬於虛擬網路，但是並沒有明確的子網路，指定的虛擬機器 | 您也可以刪除 VM。
計算 | 有通知]，[自動調整大小原則的虛擬機器 | 移轉經歷，這些設定會遭到捨棄。 強烈建議您在移轉前請先評估您的環境。 或者，您可以重新設定通知設定移轉完成後。
計算 | XML VM 副檔名 （BGInfo 1.*、 Visual Studio 偵錯工具、 Web 部署及遠端偵錯） | 不支援此功能。 建議繼續移轉虛擬機器中移除這些擴充功能，或其移轉程序時將會自動刪除。
計算 | 啟動診斷與進階版儲存空間 | 停用 Vm 的開機診斷功能，再繼續進行移轉。 移轉完成後，您可以重新啟用開機診斷資源管理員堆疊中。 此外，讓您不再會針對這些 blob 應刪除 blob 正在使用的螢幕擷取畫面與循序記錄。
計算 | 包含網頁/工作者角色的雲端服務 | 這是目前不支援。
網路 | 虛擬網路包含虛擬機器和 web/工作者角色 |  這是目前不支援。
Azure 應用程式服務 | 虛擬網路包含應用程式服務環境 | 這是目前不支援。
Azure HDInsight | 虛擬包含 HDInsight 服務的網路 | 這是目前不支援。
Microsoft Dynamics 生命週期服務 | 包含虛擬機器 Dynamics 生命週期服務所管理的虛擬網路 | 這是目前不支援。
計算 | 使用有 VPN 閘道器或內部部署的 DNS 伺服器增閘道 VNET 的 azure 資訊安全中心副檔名 | Azure 資訊安全中心會自動安裝在您的虛擬機器監控其安全性和發出警告的副檔名。 這些延伸通常取得自動安裝如果 Azure 資訊安全中心原則已啟用訂閱。 目前不支援閘道器移轉和閘道器必須先刪除之前先測試移轉、 刪除閘道器時網際網路存取 VM 儲存帳戶會遺失。 移轉時，不會繼續發生這種情況時無法填入來賓代理程式狀態 blob。 建議您停用訂閱的 Azure 資訊安全中心原則進行移轉前 3 個小時。

## <a name="the-migration-experience"></a>移轉體驗

開始移轉體驗之前，下列建議︰

- 確保您想要移轉的資源不使用任何設定或不受支援的功能。 通常在平台偵測到這些問題並產生錯誤。
- 如果您有沒有虛擬網路的 Vm，他們會停止並取消配置一部分的準備作業。 如果您不想遺失的公用 IP 位址，查詢前的準備作業的觸發預約的 IP 位址。 不過，如果 Vm 虛擬網路，請他們不停止，解除配置。
- 規劃移轉在非企業時間以納入其間的任何非預期的失敗，可能會發生這種移轉。
- 下載您的 Vm 的目前設定，使其更容易驗證準備步驟完成後，使用 PowerShell、 命令列介面 (CLI) 命令或 REST Api。
- 更新您的自動化/operationalization 指令碼處理資源管理員部署模型，然後才能開始移轉。 您也可以執行準備好狀態資源時，取得作業。
- 評估的 RBAC 原則設定傳統的 IaaS 資源，並完成移轉後的計劃。

移轉工作流程如下

![螢幕擷取畫面顯示的移轉工作流程](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] 下列各節所述的所有運算都的冪。 如果您有不支援的功能或設定錯誤的問題，建議您重試準備，中止] 或 [確認作業。 Azure 平台嘗試執行動作。

### <a name="validate"></a>驗證

驗證作業是移轉程序的第一個步驟。 此步驟的目標是要分析移轉資源的背景中的資料，並傳回成功/失敗，如果簡訊移轉的資源。

您選取虛擬網路或裝載的服務 （如果尚未虛擬網路） 您要驗證移轉。

* 如果不可以移轉的資源，Azure 的平台便會列出所有的原因為何不支援移轉。

### <a name="prepare"></a>準備

準備作業是移轉程序的第二個步驟。 此步驟的目標是模擬的資源管理員資源傳統 IaaS 資源轉換並顯示此並排顯示您以視覺化方式呈現。

您選取虛擬網路或裝載的服務 （如果尚未虛擬網路） 您想要準備移轉。

* 如果不可以移轉的資源，Azure 的平台移轉程序停駐點，並列出的準備作業失敗的原因。
* 如果資源可以移轉，Azure 平台的第一個鎖定向下移轉資源管理平面作業。 例如，您無法與移轉 VM 新增資料磁碟。

Azure 平台便會開始移轉的中繼資料從傳統到資源管理員移轉的資源。

準備作業完成後，您可以選擇視覺化中兩個傳統的資源與資源管理員。 在傳統的部署模型中每個雲端服務的 Azure 平台建立圖樣的資源群組名稱`cloud-service-name>-migrated`。

>[AZURE.NOTE] 在 [傳統的虛擬網路中的虛擬機器停止解除配置此階段的移轉。

### <a name="check-manual-or-scripted"></a>核取 （手動或指令碼）

在核取步驟中，您也可以使用您先前已下載的設定驗證移轉看起來正確。 或者，您可以登入入口網站及特別核取摘要資訊與驗證中繼資料移轉看起來不錯的資源。

如果您要移轉的虛擬網路，不會重新啟動的虛擬機器大部分的設定。 這些 Vm 上的應用程式，您可以驗證應用程式是仍和使用。

您可以測試您的自動化監控和操作的指令碼，以查看 Vm 預期般運作，且您更新的指令碼正常運作。 準備好狀態資源時支援唯一取得作業。

沒有設定時間視窗之前，您需要確認移轉。 您可以採取有足夠的時間，視需要在此狀態。 不過，管理平面已被鎖定這些資源，直到您中止或確認。

如果您看到的任何問題，您永遠可以中止移轉，並返回標準部署模型。 您回來後，Azure 的平台會開啟資源管理平面作業，好讓您可以繼續在這些 Vm 傳統部署模型中正常運作。

### <a name="abort"></a>中止

中止是選擇性的步驟，您可以用來還原至傳統部署模型變更停止移轉。

>[AZURE.NOTE] 您有觸發認可作業後，就無法執行這項作業。  

### <a name="commit"></a>確認

在您完成驗證後，您可以認可移轉。 資源不會顯示留在傳統和只適用於資源管理員部署模型。 只在新的入口網站中可以管理移轉的資源。

>[AZURE.NOTE] 這是冪等作業。 如果失敗，建議您重試作業。 如果還是失敗，建立支援票證，或與 ClassicIaaSMigration 標記我們[VM 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows)上建立論壇文章。

## <a name="frequently-asked-questions"></a>常見問題集

**這個移轉計劃影響我現有的服務或應用程式的 Azure 虛擬機器上執行下列任一？**

[否]。 （傳統） Vm 是開放中完全支援的服務。 您可以繼續使用這些資源，展開您所需的 Microsoft Azure 上。

**會發生什麼情況至我 Vm 如果我不打算在未來升級？**

我們不會廢棄的現有傳統的 Api 及資源模型。 我們想要輕鬆移轉，考慮進階的功能，可在資源管理員部署模型中。 我們強烈建議您檢閱[改進的部分](virtual-machines-windows-compare-deployment-models.md)IaaS 資源 Manager 下的一部分。

**對意味著什麼這個移轉計劃我現有的工具？**

資源管理員部署模型更新您的工具是一種最重要的變更，您必須在您的移轉計劃中的帳戶。

**管理平面停機時間在時間？**

資源是移轉數量而定。 對於較小的部署 （Vm 幾個數十），整個移轉應該採取小於小時。 為大型部署 （數百 Vm），移轉可能需要幾個小時。

**我可以復原後我移轉的資源皆已在資源管理員嗎？**

資源位於準備好狀態時，您可以中止移轉。 資源已成功移轉到認可作業後，不支援復原。

**是否能復原我移轉如果認可作業失敗？**

如果 [認可] 作業失敗，您無法中止移轉。 所有的移轉作業，包括 [認可] 作業會冪。 讓我們建議您重試短時間後的作業。 如果您仍然面對錯誤，建立支援票證，或建立論壇，在文章上[VM 論壇，在](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows)我們的 ClassicIaaSMigration 標記。

**有購買其他快速傳送電路，如果我有使用 IaaS 下資源管理員嗎？**

[否]。 我們最近啟用[移動至資源管理員部署模型傳統的 ExpressRoute 電路](../expressroute/expressroute-move.md)。 您不需要購買新的 ExpressRoute 電路，如果您已經有。

**如果我已設定的角色型存取控制原則我傳統 IaaS 資源的嗎？**

在移轉期間，資源轉換從傳統到資源管理員。 因此我們建議您計劃 RBAC 原則需要的更新，在移轉之後發生。

**如果我使用的 Azure 網站復原或 Azure 備份今天？**

若要移轉您的備份，請參閱[啟用時的虛擬機器我已經備份備份保存庫在我傳統 Vm。現在我要移轉到資源管理員模式我 Vm 傳統模式。如何可以我備份其中復原服務保存庫？](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**可以驗證我的訂閱或資源，請參閱是否已移轉的簡訊？**

[是]。 在支援的平台的移轉選項，準備移轉的第一個步驟是移轉的驗證資源的簡訊。 以避免驗證失敗，您必須完成移轉後的所有原因收到的郵件。

**如果我遇到的配額錯誤移轉準備 IaaS 資源時，會發生什麼情況？**

我們建議您中止移轉，然後登入增加您要在其中移轉 Vm 地區配額的支援要求。 核准配額要求之後，就可以開始一次執行移轉步驟。

**我要如何報告問題？**

我們[VM 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows)，以關鍵字 ClassicIaaSMigration 張貼您的問題和移轉的相關問題。 我們建議您在此論壇上張貼您的問題。 如果您有支援合約時，您已登入的支援票證歡迎使用。

**如果不喜歡平台選擇移轉中的資源的名稱？**

您明確地提供的名稱，在傳統的部署模型中的所有資源會都保留在移轉過程中。 在某些情況下，會建立新的資源。 例如︰ 為每個 VM 建立網路介面。 我們目前不支援的功能來控制移轉時建立這些新資源的名稱。 登入此功能的投票，在[Azure 意見反應](http://feedback.azure.com)。

**我收到一則訊息*」 VM 報告整體代理程式的狀態為不好。因此，您無法移轉 VM。確保 VM 代理程式報告整體代理程式的狀態，準備為 「*或*「 VM 包含的副檔名其狀態不從 VM 的報告。因此，此 VM 無法移轉。 「***

VM 沒有輸出連線到網際網路時，會收到這則訊息。 VM 代理程式會使用傳出連線連 Azure 儲存體帳戶更新代理程式狀態每隔五分鐘。


## <a name="next-steps"></a>後續步驟
現在您瞭解傳統 IaaS 資源至資源管理員移轉時，您可以開始移轉資源。

- [技術深度剖析在平台支援移轉從傳統至 Azure 資源管理員](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [使用 PowerShell 來從傳統至 Azure 資源管理員移轉 IaaS 資源](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [使用 CLI 從傳統至 Azure 資源管理員移轉 IaaS 資源](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [使用社群 PowerShell 指令碼複製傳統的虛擬機器至 Azure 資源管理員](virtual-machines-windows-migration-scripts.md)
