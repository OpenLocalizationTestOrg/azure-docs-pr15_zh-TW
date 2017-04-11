<properties
   pageTitle="StorSimple 虛擬陣列概觀 |Microsoft Azure"
   description="說明 StorSimple 虛擬陣列，管理儲存空間工作之間的內部部署虛擬裝置和 Microsoft Azure 雲端儲存空間的整合式的儲存空間方案。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>StorSimple 虛擬陣列簡介

## <a name="overview"></a>概觀

歡迎使用 Microsoft Azure StorSimple 虛擬陣列，管理儲存空間工作 hypervisor 和 Microsoft Azure 雲端儲存空間中執行的內部部署虛擬裝置之間的整合式的儲存空間方案。 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置） 是兼具效率與效益，輕鬆地管理檔案伺服器或 iSCSI 伺服器解決方案消除許多問題與企業儲存與資料保護與相關聯的費用。 虛擬陣列是特別適合遠端的 office 分公司 （機器） 案例。

這個概觀著重在虛擬陣列。 

- 如需 StorSimple 8000 數列的概觀，請移至[StorSimple 8000 系列︰ 雲端混合式解決方案](storsimple-overview.md)。 

- StorSimple 5000/7000 數列裝置的相關資訊，請移至[StorSimple 線上說明](http://onlinehelp.storsimple.com/)。

虛擬陣列支援 iSCSI 或伺服器的訊息區 (SMB) 通訊協定。 它會在現有的 hypervisor 基礎結構上執行，並提供 tiering 雲端、 雲端備份、 快速還原、 項目層級復原和損毀修復功能。

下表摘要列出虛擬陣列的重要功能。

| 功能 | 虛擬陣列 |
| ------- | ------------- |
|安裝需求 | 使用虛擬化基礎結構 （HYPER-V 或 VMware）|
| 顯示狀態 | 單一節點 |
| 容量總數 （包括雲端） |最多 64 TB 可用容量每個虛擬裝置 |
| 本機容量 | 390 GB 每個虛擬裝置 （需要佈建到 8 TB 500 GB 的磁碟空間） 的 6.4 TB 可用生產力|
| 原生通訊協定 | iSCSI 或 SMB |
| 復原時間目標 (RTO) | iSCSI: 2 分鐘無論大小 |
| 復原點目標 (RPO) | 每日的備份與指定備份 |
| 儲存 tiering | 使用熱對應，若要判斷應層資料，或縮小 |
| 支援 | 虛擬化基礎結構支援供應商 |
| 效能 | 根據基礎結構而異 |
| 資料行動性 | 可以還原到同一個裝置，或執行項目層級復原 （檔案伺服器） |
| 儲存層 | 本機 hypervisor 儲存和雲端 |
| 共用大小 |層︰ 20 TB，最多本機釘選︰ 最多 2 TB |
| 大量大小 |層︰ 最多 5 TB。本機釘選︰ 500 GB |
| 快照集 | 當機一致 |
| 項目層級的復原 | [否]。使用者可以還原共用 |

## <a name="why-use-storsimple"></a>為什麼要使用 StorSimple？

StorSimple 會使用者與伺服器以分鐘為單位，沒有應用程式的修改連線至 Azure 儲存體。

下表說明一些虛擬陣列解決方案提供的主要優點。

| 功能 | 優勢 |
|---------|---------|
| 透明整合 | 虛擬陣列支援 iSCSI 或 SMB 通訊協定。 本機層和雲端層之間移動資料且順暢透明給使用者。|
| 精簡的儲存空間的成本 | StorSimple，與您佈建足夠的本機儲存空間，以符合目前最常使用的快速鍵資料的需求。 儲存需求放大，StorSimple 層低溫將資料效益雲端儲存空間。 資料 deduplicated，進一步縮小儲存需求與費用傳送至雲端之前，先壓縮。|
| 簡化的儲存空間管理 | StorSimple 提供集中式的管理使用 StorSimple 管理員來管理多個裝置雲端中。| 
| 改良的損毀修復和法規遵循 | 立即還原中繼資料，並視需要請還原資料 StorSimple 幫助您更快速地損毀修復。 這表示盡量減少可以繼續正常運作。|
| 資料行動性 | 雲端的資料層可以復原與移轉進行其他網站的存取。 請注意，您可以還原資料僅原始的虛擬陣列。 不過，您可以使用損毀修復功能至另一個虛擬陣列還原整個虛擬陣列。|

## <a name="storsimple-workload-summary"></a>StorSimple 工作量摘要

支援的 StorSimple 工作負載的摘要時即下方形成表格。

| 案例                | 工作負載              | 支援 |  限制                                  | 版本              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| 機器共同作業      | 共用檔案          | [是]       | 請參閱[檔案伺服器的最大限制](storsimple-ova-limits.md)。 <br>請參閱[支援的中小企業版的系統需求](storsimple-ova-system-requirements.md)。   | 所有版本      |


## <a name="workflows"></a>工作流程

StorSimple 虛擬陣列是特別適合下列的工作流程︰

- [雲端儲存空間管理](#cloud-based-storage-management)
- [獨立位置的備份](#location-independent-backup)
- [資料保護與損毀修復](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>雲端儲存空間管理

您可以使用 Azure 傳統入口網站中執行的 StorSimple 管理員服務管理多個裝置上和在多個位置中儲存的資料。 這是在分散式的分支案例中特別有用。 請注意，您必須建立 StorSimple 管理員服務管理虛擬陣列及實體 StorSimple 裝置的個別執行個體。 

![雲端儲存空間管理](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>獨立位置的備份

虛擬陣列中，使用雲端快照提供位置獨立、 時間點音量或共用的複本。 依預設會啟用雲端快照，並無法停用。 所有的磁碟區與共用備份設定同時透過單一的每日備份原則，而您可以採取一些額外的臨機操作備份，必要時。

### <a name="data-protection-and-disaster-recovery"></a>資料保護與損毀修復

虛擬陣列支援下列資料保護與損毀復原案例︰

- **音量或共用還原**– 使用還原為新的工作流程要復原的音量或共用。 使用此方法在要還原整個音量或共用。
- **項目層級的復原**– 共用允許簡化的存取最近的備份。 您可以輕鬆地復原個別檔案，從雲端中可用的特殊.backup 資料夾。 此還原功能使用者導向，而是必要的任何管理工作。
- **損毀修復**– 使用復原所有區或共用資料夾，以新的虛擬陣列的容錯移轉功能。 建立新的虛擬陣列，並登錄 StorSimple 管理員服務，然後在原始的虛擬陣列會失敗。 新的虛擬陣列會然後假設能夠的資源。 

## <a name="virtual-array-components"></a>虛擬陣列元素

虛擬陣列包含下列元件︰

- [虛擬陣列](#virtual-array)– 根據佈建後，在虛擬的環境或 hypervisor 虛擬機器中的混合式雲端儲存裝置。  
- [StorSimple 管理員服務](#storsimple-manager-service)– Azure 傳統入口網站，可讓您的副檔名從單一網頁介面，您可以從不同的地理位置存取管理一或多個 StorSimple 裝置。 您可以使用 「 StorSimple 管理員服務來建立和管理服務、 檢視和管理裝置和通知，及管理區、 共用與現有的快照。
- [本機網頁使用者介面](#local-web-user-interface)– 網頁 UI，可用來設定裝置，使其可以連線到本機的網路，然後登錄的 StorSimple 管理員服務使用的裝置。 
- [命令列介面](#command-line-interface)– 虛擬陣列上啟動支援工作階段，您可以使用 Windows PowerShell 介面。
下列各節說明這些元件的較大的詳細資料，並說明方案如何排列資料、 配置儲存空間，和儲存管理與資料保護。

### <a name="virtual-array"></a>虛擬陣列

虛擬陣列是單一節點儲存空間方案，會提供主要儲存、 管理與雲端儲存空間，通訊和有助於確保的安全性和儲存在裝置上的所有資料機密性。

虛擬陣列中會有一個可供下載的模型。 儲存陣列具有最大容量 6.4 TB 的裝置 （8 TB 基礎儲存需求） 和 64 TB 包括雲端儲存空間。 

虛擬陣列具有下列功能︰

- 它會在成本。 它會讓您現有的虛擬化基礎結構的使用，並可在現有 HYPER-V 或 VMware hypervisor 部署。
- 它位於資料中心，並可以設定成 iSCSI 伺服器或檔案伺服器。 
- 它整合雲端。
- 備份會儲存在雲端，可促進損毀修復並簡化項目層級復原 (ILR)。 
- 就像您想要套用至實體裝置，您可以套用到虛擬的陣列，更新。

>[AZURE.NOTE] 無法展開虛擬陣列。 因此，請務必佈建有足夠的儲存空間，當您建立的虛擬裝置。 

### <a name="storsimple-manager-service"></a>StorSimple 管理員服務

Microsoft Azure StorSimple 提供的 web 式使用者介面 （StorSimple 管理員服務），可讓您集中管理資料中心和雲端儲存空間。 您可以使用 「 StorSimple 管理員服務來執行下列工作︰

- 管理多個 StorSimple 虛擬陣列單一服務。 
- 設定及管理 StorSimple 裝置的安全性設定。 （在雲端的加密是 Microsoft Azure Api 而定）。
- 儲存帳戶認證與屬性設定。
- 設定及管理磁碟區或共用。
- 備份及還原磁碟區或共用上的資料。
- 監控效能。
- 檢閱系統設定，並找出可能的問題。

您可以使用 「 StorSimple 管理員服務來執行每日管理您的虛擬陣列。

如需詳細資訊，請參閱[管理您的 StorSimple 裝置 StorSimple 管理員服務](storsimple-manager-service-administration.md)。

### <a name="local-web-user-interface"></a>本機網頁使用者介面

虛擬陣列包含網頁的 UI，用於一次性設定和註冊 StorSimple 管理員服務的裝置。 您可以使用其關閉並重新啟動虛擬陣列執行診斷測試、 更新軟體、 變更裝置系統管理員的密碼，檢視系統記錄檔，並連絡 Microsoft 支援服務要求。 

使用 web 使用者介面的相關資訊，請移至[使用 web UI 來管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。

### <a name="command-line-interface"></a>命令列介面

包含的 Windows PowerShell 介面，可讓您開始使用 Microsoft 支援服務的支援工作階段，好讓他們可以協助您疑難排解並解決問題，您可能會遇到的虛擬裝置上。

## <a name="storage-management-technologies"></a>儲存管理技術

除了虛擬陣列，其他元件 StorSimple 方案會使用下列軟體技術提供快速存取重要資料、 減少儲存消耗及保護您的虛擬陣列上儲存的資料︰

- [自動儲存 tiering](#automatic-storage-tiering) 
- [本機固定與區](#locally-pinned-shares-and-volumes)
- [Deduplication 和壓縮的資料層或備份到雲端](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [排程與指定的備份](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>自動儲存 tiering

虛擬陣列會使用新的 tiering 機制，以管理虛擬陣列與雲端儲存的資料。 有只有兩層︰ 本機虛擬陣列和 Azure 雲端儲存空間。 StorSimple 虛擬陣列自動排列成根據熱力圖，追蹤目前的使用狀況、 年齡與關聯到其他資料層的資料。 最作用中 （最高溫） 會儲存在本機，而不作用中] 與 [非使用中的資料會自動移轉至雲端的資料。 （所有備份會都儲存在雲端）。StorSimple 調整會重新排列資料，並使用模式指派儲存變更。 例如，部分資訊可能會降低作用中一段時間。 變得較少作用中時，將它層出至雲端。 如果相同資料會變成作用中一次，將它層中的儲存空間陣列。

特定分層的共用或大量的資料保證自己本機層的空間。 （大約 10%該共用或大量的總能夠空間）。 雖然這會減少虛擬共用或大量的裝置上的可用儲存空間，其可確保，適用於一個共用或大量 tiering 會不會受到的其他共用或磁碟區 tiering 需求。 因此上一個 [共用] 或 [大量忙碌工作量無法強制至雲端的所有工作負載。 

![自動儲存 tiering](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] 您可以指定在本機釘選大量，這種情況下會保留在虛擬陣列上的資料，以及絕不會是層至雲端]。 如需詳細資訊，移至[本機釘選共用和磁碟區](#locally-pinned-shares-and-volumes)。

### <a name="locally-pinned-shares-and-volumes"></a>本機固定與區

您可以建立適當的共用及本機釘選的區。 此功能可確保重要的應用程式所需的資料會保留在虛擬陣列中，並不層至雲端。 本機固定與區有下列功能︰ 

- 並非受制於雲端延遲或連線問題。
- 他們仍會因 StorSimple 雲端備份與損毀修復功能。

您可以還原本機固定的共用或大量為層分層的共用或本機磁碟區釘選。 

如需有關固定在本機磁碟的詳細資訊，請參閱[管理區 StorSimple 管理員服務](storsimple-manage-volumes-u2.md)。

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplication 和壓縮的資料層或備份到雲端

StorSimple 使用 deduplication 及資料壓縮進一步縮小儲存在雲端的需求。 Deduplication 減少整體排除中儲存的資料集的複本儲存的資料。 變更資訊，如 StorSimple 會忽略未變更的資料，並擷取的變更。 此外，StorSimple 減少儲存的資料識別並移除重複的資訊。 

>[AZURE.NOTE] 虛擬陣列上儲存的資料不是 deduplicated 或壓縮。 所有 deduplication 並壓縮之前傳送資料至雲端。

### <a name="scheduled-and-on-demand-backups"></a>排程與指定的備份

StorSimple 資料保護功能可讓您建立指定備份。 此外，預設備份排程可確保資料的備份每日。 在表單中的儲存在雲端的累加快照集進行備份。 快照，自上次備份記錄的變更，您可以建立及快速還原。 這些快照集可能會損毀修復狀況中很重要，因為他們取代次要儲存系統 （例如備份），並允許您將資料還原至您的資料中心或其他網站如有必要。

## <a name="next-steps"></a>後續步驟

瞭解如何[準備虛擬陣列入口網站](storsimple-ova-deploy1-portal-prep.md)。


