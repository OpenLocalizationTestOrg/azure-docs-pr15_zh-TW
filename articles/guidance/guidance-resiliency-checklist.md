<properties
   pageTitle="恢復檢查清單 |Microsoft Azure"
   description="提供的指導恢復疑慮在設計期間的檢查清單。"
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Azure 恢復指南︰ 恢復檢查清單

設計您的應用程式，恢復需要規劃以及減輕各種不同的可能發生失敗模式。 檢閱您的應用程式的設計，使其更有彈性針對這份檢查清單中的項目。

## <a name="requirements"></a>需求

- **定義您的客戶可用性需求。** 客戶必須元件的可用性需求應用程式中，這會影響應用程式的設計。 從您的客戶合約取得可用性目標應用程式的每一段的否則您的設計可能不符合客戶的期望。 如需詳細資訊，請參閱[定義您恢復需求](guidance-resiliency-overview.md#defining-your-resiliency-requirements)節的[設計的 Azure 性應用程式](guidance-resiliency-overview.md)的文件。

## <a name="failure-mode-analysis"></a>分析失敗模式

- **應用程式的執行失敗模式分析 (FMA)。** FMA 是建置程序，恢復成在設計階段應用程式。 FMA 目標包括︰  

    - 找出的應用程式可能會發生何種類型的失敗次數。 
    - 擷取潛在的效果和應用程式上失敗的每個類型的影響。
    - 找出修復策略。 

    如需詳細資訊，請參閱[設計的 Azure 性應用程式︰ 失敗模式分析][fma]。  

## <a name="application"></a>應用程式

- **部署 services 的多個執行的個體。** 服務會難免失敗，而如果您的應用程式而定服務的單一例項，會難免失敗也。 [Azure 應用程式](../app-service/app-service-value-prop-what-is.md)服務，提供多個執行個體，請選取 [[應用程式服務計劃](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)提供多個執行個體]。 如需 Azure 雲端服務，設定每個您要使用[多個執行個體](../cloud-services/cloud-services-choose-me.md#scaling-and-management)的角色。 [Azure 虛擬機器 (Vm)](../virtual-machines/virtual-machines-windows-about.md)，請確定您 VM 架構包含一個以上的 VM 和每個 VM 納入[可用性設定][availability-sets]。   

- **您可以使用負載平衡器來發佈要求。** 負載平衡器散佈應用程式要求健全的服務執行個體，移除旋轉不佳的執行個體。 如果您的服務使用 Azure 應用程式服務或 Azure 雲端服務，它已負載平衡您。 不過，如果您的應用程式使用 Azure Vm，您會需要佈建負載平衡器。 請參閱[Azure 負載平衡器](../load-balancer/load-balancer-overview.md)概觀，如需詳細資訊。 

- **設定使用多個執行個體 Azure 應用程式閘道器。** 根據您的應用程式需求[Azure 應用程式閘道](../application-gateway/application-gateway-introduction.md)可能更適合用來發佈您的應用程式服務要求。 不過，單一的執行個體的應用程式的閘道器服務不保證 SLA 以，也可讓您的應用程式可能會失敗，如果應用程式閘道執行個體失敗。 佈建多個中型或更大的應用程式閘道執行個體來保證服務條款， [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/)的可用性。

- **每個應用程式層的使用可用性集**。 將您的執行個體中的[顯示狀態設定][availability-sets]保證連線至至少有一個 VM 執行個體中[SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/)的條款。 如果您的 Vm 中沒有您沒有可用性設定可確保保護它們，可能是其所有可能會失敗，或同時更新。 

- **請考慮部署跨多個區域的應用程式。** 如果您的應用程式部署到單一的區域，少見事件的整個區域變成無法使用時，您的應用程式也會無法使用。 這可能是無法接受條款，您的應用程式 SLA。 如果是這樣，請考慮部署跨多個區域的應用程式和服務。 主動-主動模式 （發佈跨多個作用中的執行個體的要求） 或主動被動模式 （儲存 」 暖色程度 」 的執行個體保留，以避免失敗的主要的執行個體），可以使用多區域部署。 建議您在各區域的組部署的應用程式的 services 的多個執行個體。 如需詳細資訊，請參閱[業務連續性與損毀復原 (BCDR): Azure 配對區域](../best-practices-availability-paired-regions.md)。

- **在適當的地方，請執行遠端作業的恢復模式。** 如果您的應用程式而定遠端服務之間的通訊，通訊路徑會難免會失敗。 如果有多個失敗，請剩餘健全的執行個體的應用程式服務可能會被要求。 有數種模式處理常見錯誤，包括逾時模式，[再試一次圖樣]的實用[retry-pattern]，[電路分隔][circuit-breaker]模式和其他人。 如需詳細資訊，請參閱[設計的 Azure 性應用程式](guidance-resiliency-overview.md#implementing-resiliency-strategies)。 

- **使用自動縮放載入增加回應。** 如果未擴充負載增加時自動設定您的應用程式，則可能會失敗應用程式的服務，如果它們成為飽和使用者要求。 如需詳細資訊，請參閱下列各項︰

    - 一般︰[延展性檢查清單](../best-practices-scalability-checklist.md) 
    - Azure 應用程式服務︰[手動] 或 [自動調整執行個體計數][app-service-autoscale]
    - 雲端服務︰[自動調整大小雲端服務的方式][cloud-service-autoscale]
    - 虛擬機器︰[自動縮放比例和虛擬機器縮放設定][vmss-autoscale]


- **實作非同步作業可能的話。** 同步作業可以獨佔資源，並封鎖其他作業時來電者等候完成程序。 設計您的應用程式，以允許非同步作業時可能的每一個部分。 如需有關如何在 C# 實作非同步程式設計的詳細資訊，請參閱[非同步程式設計非同步和等][asynchronous-c-sharp]。

- **使用 Azure 流量管理員將您的應用程式流量路由至不同的區域。**  [Azure 流量管理員][traffic-manager]執行負載平衡 DNS 層級，可以將流量路由至不同的區域，根據[流量路由][traffic-manager-routing]您指定的方法和應用程式的端點的狀況。 

- **設定並測試您的負載平衡器和流量管理員的狀況探查。** 確保您的狀況邏輯檢查系統的要徑的部分，並適當地回應狀況探查。

    - 狀況探查[Azure 流量管理員][traffic-manager]和[Azure 負載平衡器][load-balancer]做特定的功能。 流量管理員的狀況探查決定是否要容錯移轉到另一個區域。 負載平衡器，它決定是否要移除旋轉 VM。      

    - 流量管理員檢查，您的狀況端點應該核取的部署內的相同的區域，和其失敗就要容錯移轉到另一個區域的要徑相依性。  

    - 對於負載平衡器狀況端點應該報告 VM 的狀況。 不包含其他層級或外部服務。 否則，VM 外，就會發生失敗會導致負載平衡器 VM 移除旋轉。

    - 實作健康監視應用程式中，請參閱[狀況端點監控圖樣](https://msdn.microsoft.com/library/dn589789.aspx)。

- **監控協力廠商服務。** 如果您的應用程式的協力廠商服務相依性，會識別如何這些協力廠商服務可能會失敗，並什麼效果失敗時，會對您的應用程式。 協力廠商服務可能不包含的監控和診斷，因此很重要記錄這些您引動及建立它們應用程式的健康情況的關聯，診斷記錄使用的唯一識別碼。 監控和診斷的最佳作法的詳細資訊，請參閱[監控和診斷指引][monitoring-and-diagnostics-guidance]文件。

- **請確定您使用任何協力廠商服務，提供 SLA。** 如果您的應用程式而定協力廠商服務，但協力廠商提供任何保證 SLA 的表單中的可用性，應用程式的可用性也無法保證。 您 SLA 是為您的應用程式至少有可用的元件。


## <a name="data-management"></a>資料管理

- **瞭解您的應用程式的資料來源的複寫方法。** 您的應用程式的資料會儲存在不同的資料來源，而且有不同的顯示狀態需求。 評估 Azure 中的資料儲存的每個類型的複寫方法，包括[Azure 儲存複寫](../storage/storage-redundancy.md)和[SQL 資料庫作用中地理複寫](../sql-database/sql-database-geo-replication-overview.md)，以確保符合您的應用程式的資料需求。

- **確定沒有單一使用者帳戶已生產和備份資料的存取權。** 如果單一使用者帳戶有生產和備份來源寫入權限，會遭到盜用您資料的備份。 惡意使用者刻意無法刪除您所有的資料，一般使用者可能不小心刪除它。 設計您的應用程式，來限制權限的每個使用者帳戶，以便只需要寫入存取權的使用者擁有寫入權限，它只是為了生產或備份，但不是能同時。

- **您的資料來源失敗透過失敗後的程序和測試文件。** 在您的資料來源位置 catastrophically 失敗的情況下的人力運算子必須追蹤一組記錄的指示，容錯移轉至新的資料來源。 如果發生錯誤的記錄的步驟，運算子會無法順利遵循這些，並在資源失敗。 定期測試指示的步驟驗證運算子追蹤這些是可以順利容錯和回失敗的資料來源。

- **驗證您的資料備份。** 定期驗證您的備份資料是您預期的事情執行指令碼來驗證資料完整性、 結構描述和查詢。 沒有點有備份，如果您沒有幫助以還原您的資料來源。 登入和，可以修復備份的服務，讓報表的不一致性。

- **請考慮使用所地理多餘的儲存空間的帳戶類型。** Azure 儲存體帳戶中儲存的資料永遠會複製到本機。 然而，有多個複製策略時儲存帳戶也會佈建後，從選擇。 選取[Azure 讀取權限地理多餘的儲存 (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage)的整個區域無法使用時，保護少數的情況下您的應用程式的資料。

    > [AZURE.NOTE] 針對 Vm，不要依賴 RA GRS 複寫還原 VM 磁碟 （VHD 檔案）。 請改用[Azure 備份][azure-backup]。   

## <a name="operations"></a>作業

- **實作監視和警告應用程式中的最佳作法。** 不正確的監控、 診斷和警示，有任何應用程式中偵測失敗，通知運算子來修正這些問題。 最佳作法的詳細資訊，請參閱[監控和診斷指引][monitoring-and-diagnostics-guidance]文件。

- **測量遠端通話的統計資料，並讓應用程式小組使用的資訊。**  如果您不追蹤和報告中進行即時遠端呼叫統計資料，並提供簡單的方式來檢閱此資訊，作業小組不會將您的應用程式的健康情況的即時檢視。 如果您只量值平均遠端呼叫時間，您就沒有足夠的資訊，以顯示和在服務的問題。 摘要遠端呼叫度量資訊，例如延遲、 處理量和 99 95 的百分位數的錯誤。 執行以發掘出現在每個百分位數內的錯誤指標的統計分析。

- **在適當的時間範圍中追蹤暫時性的例外狀況和重試的數。** 如果您不追蹤和監視暫時性的例外狀況並再試一次嘗試一段時間，則可能無法隱藏問題或失敗，您的應用程式重試邏輯。 也就是說，如果您監視與記錄只會顯示成功或失敗的作業，會隱藏 [例外狀況重試多次已作業的交易。 增加一段時間的例外狀況的趨勢指出服務時遇到問題，可能會失敗。 如需詳細資訊，請參閱[重試服務特定指導][retry-service-guidance]。

- **實作警示系統的通知運算子。** 找出關鍵效能指標的應用程式的狀況，例如暫時性的例外狀況和遠端呼叫延遲，，並針對每個適當臨界值。 達到臨界值時，請傳送提醒給作業。 找出問題嚴重和需要復原回應之前的層級設定這些臨界值。

- **您的應用程式的發行程序的文件。** 詳細的發行程序的文件，而運算子可能部署不正確的更新，或不正確設定您的應用程式的設定。 明確定義的文件版本程序，並確保其是整個作業小組使用。 有彈性的部署，您的應用程式的最佳作法所述[有彈性的部署][guidance-resilient-deployment]恢復指引文件的區段。

- **請確定小組中的多個人員的訓練監視應用程式，並執行任何手動復原步驟。** 如果您只有小組可以監視應用程式，並開始進行修復步驟單一運算子，該人員就會變成單一的失敗。 訓練偵測與修復的多個每個人，並確定已隨時至少一個隨時作用中。

- **自動執行您的應用程式部署程序。** 如果您的作業人員需要以手動方式部署您的應用程式，人力錯誤可能會導致失敗部署。 如需有關自動執行的應用程式部署的最佳作法的詳細資訊，請參閱[同時部署][guidance-resilient-deployment]恢復指引文件的區段。

- **設計最大化應用程式的可用性發行程序。** 如果發行程序需要離線部署期間的服務，直到其重新連線後，將無法使用應用程式。 您可以使用[藍色/綠色](http://martinfowler.com/bliki/BlueGreenDeployment.html)或[加納利放開](http://martinfowler.com/bliki/CanaryRelease.html)部署技巧來部署生產應用程式。 這兩種技巧涉及部署您發行程式碼的同時實際執行程式碼，因此發行程式碼的使用者可以重新導向至失敗的實際執行程式碼。 如需詳細資訊，請參閱[同時部署][guidance-resilient-deployment]恢復指引文件的區段。

- **登入，及稽核您的應用程式部署。** 如果您是使用分段部署技巧，例如會執行生產環境中的應用程式的多個版本的藍色/綠色或加那利版本。 如果應該發生問題，請務必判斷哪個版本的應用程式會造成問題。 實作強大的記錄策略擷取盡可能特定版本的資訊。 

- **請確定您的應用程式不會執行進而[Azure 訂閱限制](../azure-subscription-service-limits.md)。** Azure 訂閱會在特定的資源類型，例如 [資源群組的數字、 數字的核心和數字的儲存空間帳戶受到限制。  如果應用程式的需求超出 Azure 訂閱限制，建立另一個 Azure 訂閱和佈建足夠的資源那里。

- **請確定您的應用程式不會執行進而[每個服務限制](../azure-subscription-service-limits.md)。** 個別的 Azure 服務有消耗限制&mdash;，例如限制儲存空間、 處理量數連線、 秒，要求及其他指標。 您的應用程式嘗試使用這些限制，超出的資源將會失敗。 這樣會受影響使用者的服務節流和可能停機時間。 

    根據特定的服務與應用程式的需求，您可以通常這些限制，以避免縮放 （例如，選擇其他定價層） 或橫向擴充 （新增新的執行個體）。  

- **設計您的應用程式儲存需求，以落在 Azure 儲存體延展性和效能目標。** Azure 儲存空間的設計中預先定義的延展性和效能目標函數、 設計您的應用程式，利用這些目標內的儲存空間。 如果您在超出目標應用程式，會遇到節流儲存空間。 若要修正此問題，提供額外儲存空間的帳戶。 如果您執行進而帳戶儲存空間限制，佈建其他 Azure 訂閱，然後佈建那里額外儲存空間帳戶。 如需詳細資訊，請參閱[Azure 儲存體延展性和效能目標](../storage/storage-scalability-targets.md)。

- **選取應用程式的正確的虛擬記憶體大小。** 測量實際的 CPU、 記憶體、 磁碟和 I/O 您 Vm 生產環境中，確認您已選取虛擬記憶體大小足夠。 如果不是，您的應用程式可能會遇到容量問題，當 Vm 接近其限制。 [大小中 Azure 虛擬機器中的](../virtual-machines/virtual-machines-windows-sizes.md)文件中的詳細說明 VM 大小。

- **判斷您的應用程式的工作量是否穩定或變動一段時間。** 如果您的工作量有一段時間，使用 Azure VM 縮放比例設定為自動縮放 VM 執行個體數目。 否則，您必須以手動方式增加或減少 Vm 數。 如需詳細資訊，請參閱[虛擬機器縮放比例設定概觀](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

- **選取正確的服務層 Azure SQL 資料庫。** 如果您的應用程式使用 Azure SQL 資料庫，請確定您已選取適當的服務層。 如果您選取無法處理您的應用程式資料庫交易單位 (DTU) 需求層時，您的資料使用會經流速控制。 如需有關選取正確的服務方案的詳細資訊，請參閱[SQL 資料庫選項和效能︰ 瞭解什麼是用於每個服務層級](../sql-database/sql-database-service-tiers.md)文件。 

- **有回復計劃以供部署。** 您的應用程式部署可能會失敗，導致您無法使用的應用程式，可能是。 設計復原程序來回到最後的建議版本和最小化停機時間。 請參閱[同時部署][guidance-resilient-deployment]恢復指引文件，如需詳細資訊] 區段。 

- **建立與 Azure 支援互動的程序。** 如果連絡[Azure 支援](https://azure.microsoft.com/support/plans/)的程序未設定記住連絡支援人員之前，則為第一次瀏覽支援程序時將延長停機時間。 包含連絡支援中心和屬於您的應用程式恢復從開始擴大問題的程序。

- **請確定您的應用程式不超過最大的數字的每個訂閱的儲存空間帳戶使用。** Azure 可讓每個訂閱的 200 儲存帳戶的最大值。 如果您的應用程式需要更多儲存空間帳戶超過目前可在您的訂閱，您必須建立新的訂閱，並建立那里額外的儲存空間帳戶。 如需詳細資訊，請參閱[Azure 訂閱及服務限制，配額和限制式](../azure-subscription-service-limits.md#storage-limits)。

- **請確定您的應用程式不超過延展性目標的虛擬機器磁碟。** Azure IaaS VM 支援附加資料磁碟根據下列因素的虛擬記憶體大小和儲存的帳戶類型的數字。 如果您的應用程式超過延展性目標的虛擬機器磁碟，佈建額外儲存空間帳戶，然後建立的虛擬機器磁碟。 如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標] (.../ storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks）

## <a name="test"></a>測試

- **執行錯誤移轉及回復測試您的應用程式。** 如果您沒有完整的測試錯誤移轉及回復，您就能特定的相依服務應用程式中的恢復已同步處理的方式損毀修復期間。 請確定您的應用程式相關服務錯誤移轉及失敗的順序正確無誤。

- **執行錯誤插入測試您的應用程式。** 您的應用程式可能會失敗有許多不同的原因，例如憑證到期耗盡 VM，或儲存失敗系統資源。 當關閉生產，盡可能模擬或觸發實數失敗的環境中測試您的應用程式。 比方說，刪除憑證，手動取用系統資源，或刪除儲存體來源。 請確認您的應用程式能夠修復的錯誤，了解單獨使用和組合的所有類型。 確認未散佈或階層式透過您的系統失敗。

- **使用兩個綜合及真實使用者資料在執行測試。** 測試和實際是很少相同，因此請務必使用藍色/綠色或加那利部署，並在生產環境中測試您的應用程式。 這個選項可讓您在真實負載生產環境中測試您的應用程式，並確保如預期般完全部署時，它會運作。

## <a name="security"></a>安全性

- **實作分散式拒絕服務攻擊的應用程式層級防護。** 在網路層 DDos 攻擊受到 azure 服務。 不過，Azure 無法防止應用程式層攻擊，因為很難辨別惡意使用者要求，則為 true 的使用者要求。 如需有關如何防範應用程式層級 DDoS 攻擊的詳細資訊，請參閱[Microsoft Azure 網路安全性](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)（PDF 下載） 的 「 針對 DDoS 保護 」 一節。

- **實作存取應用程式的資源的最低權限原則。** Access 應用程式的資源的預設值應該儘可能限制。 授與核准基準較高的層級權限。 授與過度寬鬆存取您的應用程式的資源，依預設會導致某人刻意或不小心刪除資源。 Azure 提供[角色型存取控制](../active-directory/role-based-access-built-in-roles.md)管理使用者的權限，但請務必確認有自己的權限系統，例如 SQL Server 其他資源最低權限的權限。 

## <a name="telemetry"></a>遙測

- **生產環境中執行應用程式時，請登入遙測資料。** 生產環境中執行應用程式，或您不會有足夠的資訊來診斷問題的原因，同時積極服務使用者時，擷取健全的遙測資訊。 在登入的詳細資訊的最佳作法適用於[監控和診斷指引][monitoring-and-diagnostics-guidance]文件。

- **實作記錄使用非同步的模式。** 如果記錄作業同步，它們可能會封鎖應用程式碼。 請確定您記錄操作的實作為非同步作業。 

- **建立記錄資料跨服務界限之間的關係。** 在一般 n 層應用程式中，使用者要求可能往返多個服務的邊界。 例如，使用者要求通常是在 web 層產生傳遞給商務層與最後保存在資料層。 在更複雜的情況下，使用者要求可能散發給許多不同的服務與資料儲存區。 請確定該記錄系統相互關聯通話跨服務界限，您可以在應用程式要求。

##  <a name="azure-resources"></a>Azure 資源 

- **使用佈建資源 Azure 資源管理員範本。** 資源管理員範本讓您更容易自動化部署透過 PowerShell 或 Azure CLI 中，會導致可靠的部署程序。 如需詳細資訊，請參閱[Azure 資源管理員概觀][resource-manager]。

- **授與資源有意義的名稱。** 授與資源有意義的名稱可讓您更易於找出特定的資源，並瞭解其角色。 如需詳細資訊，請參閱[命名慣例 Azure 資源的建議](guidance-naming-conventions.md) 

- **使用角色型存取控制 (RBAC)**。 您可以使用 RBAC 來控制您部署 Azure 資源的存取權。 RBAC 可讓您將授權角色指派給您 DevOps 小組成員，以避免意外刪除或變更已部署資源。 如需詳細資訊，請參閱[開始使用 access Azure 入口網站中的管理](../active-directory/role-based-access-control-what-is.md) 

- **使用資源鎖定的重要的資源，例如 Vm。** 資源鎖定，防止不小心刪除資源運算子。 如需詳細資訊，請參閱[鎖定資源與 Azure 資源管理員](../resource-group-lock-resources.md) 

- **地區組。** 時部署到兩個區域，請選擇 [地區成對的 [地區]。 主要的資料時復原的一個區域是選擇優先將登出每組。 某些服務，例如地理多餘的儲存空間提供自動複寫，即成對的區域。 如需詳細資訊，請參閱[商務連續性和損毀復原 (BCDR): Azure 配對區域](../best-practices-availability-paired-regions.md) 

- **函數和生命週期的組合管理資源群組**。  一般而言，資源群組應包含共用相同的生命週期的資源。 如此可讓您更容易管理部署]，刪除測試部署，並指派存取權限，減少生產部署不小心刪除或修改的機率。 建立個別的資源生產、 開發、 群組和測試環境。 在多區域部署中，將放入個別的資源群組的每個區域的資源。 如此可讓您更容易部署一個區域，而不會影響其他區域。 

## <a name="azure-services"></a>Azure 服務 

下列檢查清單項目套用至 Azure 中的特定服務。

###  <a name="app-service"></a>應用程式服務 

- **使用標準或進階版層。** 這些層支援暫存的位置，並自動備份。 如需詳細資訊，請參閱[Azure 應用程式服務方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **避免向上或向下縮放比例。** 相反地，選取層和執行個體大小符合您的效能需求下一般載入，然後按一下 [[延展](../app-service-web/web-sites-scale.md)處理變更流量的執行個體。 縮放比例向上和向下可能會觸發重新啟動應用程式。  

- **儲存為應用程式設定的設定。** 使用應用程式設定，以設定為應用程式設定的設定。 資源管理員範本，或使用 PowerShell，好讓您可以將其做為自動部署的一部分套用 / 更新程序，這是更可靠中定義的設定。 如需詳細資訊，請參閱[Azure 應用程式服務中的 [設定 web 應用程式](../app-service-web/web-sites-configure.md)。 

- **建立另一個應用程式服務方案生產和測試。** 不使用的位置上生產部署以進行測試。  在相同的應用程式服務方案中的所有應用程式共用相同的 VM 執行個體。 如果您將生產和測試部署相同的方案中，將會產生負面影響實際部署。 例如，負載測試也可能會降低實際生產網站。 來測試部署至不同的計劃，您將其隔離從生產版本。  

- **從網頁 Api 個別的 web 應用程式**。 如果您的方案有前端網頁和網路 API，請考慮分解它們到另一個應用程式服務應用程式。 此設計容易分解解決方案的工作量。 您可以執行的 web 應用程式和 API 另一個應用程式服務方案，讓他們可以獨立縮放。 如果您不需要的層級延展性在第一次，您可以將應用程式部署到相同的方案，並將其移至另一個方案之後，如有需要。

- **避免使用 Azure SQL 資料庫備份的應用程式服務備份功能。** 不過，使用 [ [SQL 資料庫自動備份][sql-backup]。 應用程式服務備份將資料庫匯出成本 DTUs SQL.bacpac 檔案。  

- **部署至暫存的位置。** 建立臨時的部署位置。 部署應用程式更新到暫存的位置，並確認之前交換其生產部署。 這會減少生產環境中的不良更新的機率。 也可確保所有執行個體接手之前生產一起移動。 許多應用程式具備有效的準備和冷開始時間。 如需詳細資訊，請參閱[設定臨時環境中 Azure 應用程式服務的 web 應用程式](../app-service-web/web-sites-staged-publishing.md)。 

-  **建立部署位置，如要保留的最後一次以良好 (LKG) 部署。** 當您部署正式的更新時，請將先前生產部署移至 LKG 位置。 如此可讓您更容易如期錯誤的部署。 如果您稍後發現問題，您可以快速回復 LKG 版本。 如需詳細資訊，請參閱[Azure 參考架構︰ 基本的 web 應用程式](guidance-web-apps-basic.md)。 

- **啟用診斷記錄**，包括應用程式的記錄，以及網頁伺服器記錄。 記錄功能是很重要的監控和診斷。 請參閱[啟用診斷記錄 Azure 應用程式服務中的 web 應用程式](../app-service-web/web-sites-enable-diagnostic-log.md)

- **記錄檔，以 blob 儲存體**。 如此可讓您更容易收集並分析資料。 

- **建立個別的儲存空間帳戶記錄。** 請勿使用同一個儲存帳戶記錄檔和應用程式的資料。 這可協助避免從減少應用程式的效能的記錄。 

- **監控效能。** 使用效能監視監視應用程式效能與行為負載[新聖器](http://newrelic.com/)或[應用程式的深入見解](../application-insights/app-insights-overview.md)等服務。  效能監視，讓您在應用程式的即時充分。 讓您以診斷問題，並執行根本原因分析的失敗次數。 


###  <a name="application-gateway"></a>應用程式的閘道器 

- **佈建至少兩個執行個體。** 將應用程式閘道部署至少兩個執行個體。 一個執行個體是失敗的單一。 使用兩個或多個執行個體重複能與延展性。 才有資格的[SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/)，您必須提供兩個或多個中型或更大的執行個體。 

### <a name="azure-search"></a>Azure 搜尋

- **佈建一個以上的複本。** 已讀取的高可用性，或三讀寫高可用性使用兩個以上的複本。

- **設定索引子多區域部署。** 如果您有多區域部署，請考慮連續性編製索引中的選項。

    - 如果地理複製的資料來源，指向每個地區 Azure 搜尋服務的每個索引子其本機資料來源複本]。  

    - 如果資料來源不是地理複寫，指向在相同的資料來源的多個索引子，以便在多個區域內的 Azure 搜尋服務持續，獨立編製索引的資料來源的資料。 如需詳細資訊，請參閱[Azure 搜尋效能與最佳化考量][search-optimization]。

###  <a name="azure-storage"></a>Azure 儲存體 

- **應用程式的資料，使用 [讀取權限地理多餘的儲存空間 (RA GRS)。** RA GRS 儲存會將資料複製到第二區域中，，並提供唯讀存取權，從第二個區域。 如果主要區域中有儲存資料，應用程式可以讀取資料，從第二個區域。 如需詳細資訊，請參閱[Azure 儲存體複寫](../storage/storage-redundancy.md)。

- **針對 VM 磁碟上，使用進階版儲存空間**如需詳細資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。

- **佇列中的儲存空間，請在另一個區域中建立備份佇列。** 佇列中儲存的唯讀複本有限使用，因為您無法佇列或取消佇列項目。 不過，建立備份佇列中另一個區域中的儲存空間帳戶。 儲存資料時，應用程式可以使用備份佇列中，直到的主要區域會再次可用。 如此一來，應用程式仍可以處理新的要求。  

###  <a name="documentdb"></a>DocumentDB 

- **在區域中複製的資料庫。** 使用多區域帳戶時，您 DocumentDB 資料庫中有一個寫入地區碼和已讀取的多個區域。 如果寫入地區失敗，您可以瞭解從另一個複本。 用戶端 SDK 處理此自動。 您也可以透過到另一個區域的寫入地區會失敗。 如需詳細資訊，請參閱[全面使用 DocumentDB 等距分佈資料](../documentdb/documentdb-distribute-data-globally.md)。


###  <a name="sql-database"></a>SQL 資料庫 

- **使用標準或進階版層。** 這些層提供再時間點還原句號 （35 天）。 如需詳細資訊，請參閱[SQL 資料庫選項與效能](../sql-database/sql-database-service-tiers.md)。

- **啟用稽核 SQL 資料庫。** 稽核可用於診斷惡意攻擊或人力錯誤。 如需詳細資訊，請參閱[開始使用 SQL 資料庫稽核](../sql-database/sql-database-auditing-get-started.md)。 

- **使用作用中的地理複寫**使用作用中的地理複寫來建立可讀的次要不同區域。  如果您的主要資料庫失敗，或只需要離線，請執行手動容錯移轉至次要資料庫。  您無法在直到次要資料庫會維持唯讀狀態。  如需詳細資訊，請參閱[SQL 資料庫作用中地理複寫](../sql-database/sql-database-geo-replication-overview.md)。 

- **使用 sharding**。 請考慮使用 sharding 水平分割資料庫。 Sharding 可以提供隔離錯誤。 如需詳細資訊，請參閱[Azure SQL 資料庫時的縮放比例](../sql-database/sql-database-elastic-scale-introduction.md)。 

- **使用時間點還原修復人力錯誤。**  時間點還原會傳回時間至之前的資料庫。 如需詳細資訊，請參閱[使用自動的資料庫備份 Azure SQL 資料庫復原][sql-restore]。

- **若要從服務中斷復原使用地理還原。** 地理還原從地理多餘的備份檔案還原資料庫。  如需詳細資訊，請參閱[使用自動的資料庫備份 Azure SQL 資料庫復原][sql-restore]。


###  <a name="sql-server-running-in-a-vm"></a>SQL Server （以 VM 執行）

- **複製的資料庫。** 使用 SQL Server 永遠在可用性群組複製的資料庫。 如果一個 SQL Server 執行個體失敗，提供高可用性。 如需詳細資訊，請參閱[詳細的資訊...](guidance-compute-n-tier-vm.md) 

- **備份資料庫**。 如果您已備份您的 Vm 使用[Azure 備份](https://azure.microsoft.com/documentation/services/backup/)，請考慮使用[SQL Server 工作負載使用 DPM Azure 備份](../backup/backup-azure-backup-sql.md)。 使用這個方法，有一個備用管理員角色，組織及 Vm 和 SQL Server 整合的修復程序。 否則，請使用[SQL Server 管理備份到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)。 


###  <a name="traffic-manager"></a>流量管理員 

- **執行手動回復。** 後流量管理員移轉後執行手動回復，而不是自動回失敗。 前回失敗，請確認所有應用程式子系統健全。  否則，您可以建立的資料中心之間來回翻轉應用程式的情況。 如需詳細資訊，請參閱[執行的 Vm 在多個區域](guidance-compute-multiple-datacenters.md)。

- **建立狀況探查結束點**。 建立自訂的端點，應用程式的整體的狀況報告。 這可讓如果任何要徑失敗，而不只是前端容錯流量管理員。 端點任何關鍵的相依性不良或無法連線時，應傳回 HTTP 錯誤碼。 沒有報告錯誤不重要的服務，不過。 否則，不需要時，建立誤判狀況探查可能會觸發容錯移轉。 如需詳細資訊，請參閱[流量管理員端點監控和容錯移轉](../traffic-manager/traffic-manager-monitoring.md)。


###  <a name="virtual-machines"></a>虛擬機器 

- **避免在單一 VM 執行生產工作量。** 單一 VM 部署不同時計畫之內進行的維修作業。 不過，將多個 Vm 可用性組或 VM 縮放設定，與負載平衡器的前方中。 才有資格的 SLA，必須在同一個可用性組中部署兩個以上的虛擬機器。 如需詳細資訊，請參閱[虛擬機器縮放比例設定概觀](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。 

- **指定當您佈建 VM 時設定的可用性。** 目前，有任何資源管理員 VM 加入 VM 已佈建後，設定可用性。 當您新增新 VM 至現有的顯示狀態就會設定、 確認 NIC 建立 VM，以及新增 NIC 負載平衡器上的位址後端資料庫。 否則，負載平衡器不會將該 vm 的網路流量路由傳送。 

- **將每個應用程式層放入個別的可用性設定。** N 層應用程式中，不放 Vm 來自不同層相同的可用性設定。 在可用性設定 Vm 橫跨故障網域 (FDs)，並更新網域 （「 ud 」）。 不過，若要取得 FDs 和 UDs 重複性的優勢，在可用性設定每個 VM 必須能夠處理相同的用戶端要求。 

- **選擇正確的虛擬記憶體大小根據效能的需求。** 將現有的工作量移至 Azure 時，開頭是最符合您的內部部署伺服器虛擬記憶體大小。 然後測量 CPU、 記憶體和磁碟 IOPS，對於您實際工作負載的效能，並視需要調整大小。 如此有助於確保應用程式的表現如預期般位於雲端環境。 此外，如果您需要多個 Nic，請注意的每個大小的 NIC 限制。 

- **使用 Vhd 進階版儲存空間。** Azure 進階版儲存提供高效能、 低延遲磁碟支援。 如需詳細資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)選擇支援進階版儲存虛擬記憶體大小。 

- **針對每個 VM 建立個別的儲存空間的帳戶。** 將一個 VM Vhd 放置至個別儲存帳戶。 這可協助避免正中 IOPS 限制儲存的帳戶。 如需詳細資訊，請參閱[Azure 儲存體延展性和效能目標](../storage/storage-scalability-targets.md)。 不過，如果您部署 Vm 有很多，請注意的儲存空間帳戶的每個訂閱上限。 請參閱[儲存空間限制](../azure-subscription-service-limits.md#storage-limits)。

- **建立個別的儲存空間的帳戶的 [診斷記錄**。 不儲存帳戶為 Vhd，若要避免診斷記錄影響 VM 磁碟 IOPS 撰寫診斷記錄。 標準的本機多餘的儲存空間 (LRS) 帳戶不足，診斷記錄。 

- **在資料的磁碟，而非 OS 磁碟上安裝應用程式。** 否則，您可能會碰到的磁碟大小限制。 

- **使用備份 Vm Azure 備份。** 意外的資料遺失保護備份。 如需詳細資訊，請參閱[使用復原服務保存庫保護 Azure Vm](../backup/backup-azure-vms-first-look-arm.md)。

- **啟用診斷記錄**，包括基本狀況指標、 基礎結構的記錄，以及[開機診斷][boot-diagnostics]。 啟動診斷可協助您診斷開機失敗，如果您 VM 進入非開機的狀態。 如需詳細資訊，請參閱[概觀的 Azure 診斷記錄][diagnostics-logs]。

- **使用 AzureLogCollector 副檔名**。 (僅限 Windows Vm。)至此分機] 彙總 Azure 平台記錄，並將其上載沒有遠端登入 VM 運算子 Azure 儲存空間。 如需詳細資訊，請參閱[AzureLogCollector 副檔名](../virtual-machines/virtual-machines-windows-log-collector-extension.md)。


###  <a name="virtual-network"></a>虛擬網路 

- **若要 whitelist 或區塊公用 IP 位址，請將子網路 NSG。** 封鎖存取惡意的使用者，或只允許使用者擁有存取應用程式的權限存取。  

- **建立自訂的狀況檢查。** 負載平衡器狀況探查可以測試 HTTP 或 TCP。 如果 VM 執行 HTTP 伺服器，HTTP 探查會是比 TCP 探查健康狀態更好指示器。 如需 HTTP 探查使用報表的整體狀況的應用程式，包括所有的要徑相依性的自訂端點。 如需詳細資訊，請參閱[Azure 負載平衡器概觀](../load-balancer/load-balancer-overview.md)。

- **不會封鎖狀況探查。** 已知的 IP 位址，168.63.129.16 會傳送負載平衡器狀況探查。 不會封鎖流量或從這個 IP 任何防火牆原則或網路安全性群組 (NSG) 規則。 封鎖狀況檢查，可能會造成負載平衡器 VM 移除旋轉。 

- **啟用負載平衡器記錄。** 記錄後端上顯示多少 Vm 未收到失敗的探查回應因為網路流量。 如需詳細資訊，請參閱[Azure 負載平衡器記錄分析資料](../load-balancer/load-balancer-monitor-log.md)。


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
