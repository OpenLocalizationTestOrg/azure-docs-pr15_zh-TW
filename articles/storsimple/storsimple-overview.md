<properties 
   pageTitle="什麼是 StorSimple？ |Microsoft Azure" 
   description="說明 StorSimple tiering、 裝置、 虛擬裝置、 服務及儲存管理並介紹鍵 StorSimple 中所用的字詞。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 系列︰ 混合式雲端儲存空間方案

## <a name="overview"></a>概觀

歡迎使用 Microsoft Azure StorSimple，管理儲存空間工作內部部署裝置與 Microsoft Azure 雲端儲存空間之間的整合式的儲存空間方案。 StorSimple 是可消除許多問題與企業儲存與資料保護與相關聯的費用兼具效率與效益，輕鬆地管理儲存空間] 區域網路 （舊） 的解決方案。 它會使用專屬的 StorSimple 8000 數列裝置、 整合雲端服務，並提供順暢檢視所有的企業儲存空間，包括雲端儲存空間的一組管理工具。 （適用於只 StorSimple 8000 數列裝置的 Microsoft Azure 網站上發佈的 StorSimple 部署資訊。 如果您使用的 StorSimple 5000/7000 數列裝置，請移至[StorSimple 說明。）](http://onlinehelp.storsimple.com/)

StorSimple 使用[儲存空間 tiering](#automatic-storage-tiering)以不同的儲存空間媒體管理儲存的資料。 目前的工作集會儲存於內部部署實心狀態磁碟機 (SSDs)、 不常使用的資料儲存於硬碟機 (HDDs)，並保存資料放至雲端。 此外，StorSimple 使用 deduplication 和壓縮減少的資料使用的儲存空間量。 如需詳細資訊，請到[Deduplication 壓縮](#deduplication-and-compression)。 如需定義的其他重要的術語與 StorSimple 8000 數列文件中使用的概念，移至本文結尾的[StorSimple 術語](#storsimple-terminology)。

StorSimple 更新 2，您可以為主要的資料保持本機裝置，且不會層至雲端的 [*本機釘選*出適當的區。 這可讓您執行時繼續使用雲端備份敏感雲端的延遲，例如 SQL 和虛擬機器負載，本機釘選磁碟區上的工作量。 如需有關固定在本機磁碟的詳細資訊，請參閱[使用 StorSimple 管理員服務管理區](storsimple-manage-volumes-u2.md)。 

更新 2 也可讓您建立的善用低延遲和高效能提供 Azure 進階版儲存 StorSimple 虛擬裝置。 StorSimple 進階版虛擬和裝置有關的詳細資訊，請參閱[部署及管理 StorSimple 虛擬裝置 Azure 中的](storsimple-virtual-device-u2.md)。 如需 Azure 進階版儲存空間的詳細資訊，請移至[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。

除了儲存管理 StorSimple 資料保護功能可讓您建立視和排程的備份，然後將它們儲存在本機或雲端中。 形式的累加快照，這表示其，您可以建立及還原快速進行備份。 雲端快照集可能會損毀修復狀況中很重要，因為他們取代次要儲存系統 （例如備份），並允許您將資料還原至您的資料中心或其他網站如有必要。

![[視訊] 圖示](./media/storsimple-overview/video_icon.png) 觀看視訊，Microsoft Azure StorSimple 的快速簡介。

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>為什麼要使用 StorSimple？

下表說明一些 Microsoft Azure StorSimple 提供的主要優點。

| 功能 | 優勢 |
|---------|---------|
|透明整合 | Microsoft Azure StorSimple 使用 iSCSI 通訊協定幕後連結資料儲存設備。 這可確保資料儲存在雲端，在資料中心，或在遠端伺服器上會顯示儲存在單一位置。|
|精簡的儲存空間的成本|Microsoft Azure StorSimple 配置足夠的本機或雲端儲存空間，以符合目前的要求，並將延伸僅在必要時的雲端儲存空間。 它進一步減少儲存需求和費用消除重複相同的資料 (deduplication) 版本及使用壓縮。|
|簡化的儲存空間管理|Microsoft Azure StorSimple 提供的系統管理工具，來設定及管理資料儲存於內部部署，在遠端伺服器上，並在雲端，您可以使用。 此外，您可以管理備份和還原函數從 Microsoft 管理主控台 (MMC) 嵌入式管理單元 」。 StorSimple 提供個別的選擇性介面，您可以用來延伸 StorSimple 管理及資料保護服務以在 SharePoint 伺服器上儲存的內容。 |
|改良的損毀修復和法規遵循|Microsoft Azure StorSimple 不需要擴充的復原時間。 不過，在需要時還原資料。 這表示盡量減少可以繼續正常運作。 此外，您可以設定來指定備份的排程與資料保留原則。
|資料行動性|上傳到 Microsoft Azure 雲端服務的資料可以復原與移轉進行其他網站的存取。 此外，您可以使用 StorSimple 設定 StorSimple 虛擬裝置上執行的 Microsoft Azure 虛擬機器 (Vm)。 Vm 使用虛擬裝置存取儲存的資料進行測試或復原。|
|針對其他雲端服務提供者提供的支援 |軟體 StorSimple 8000 數列更新 1 或更新版本支援 Amazon S3 給、 HP，與 OpenStack 雲端服務，以及 Microsoft Azure。 （您仍需要 Microsoft Azure 儲存體帳戶的裝置管理。）如需詳細資訊，移至[功能更新 1.2 的新增功能](storsimple-update1-release-notes.md#whats-new-in-update-12)。|
|業務連續性 | 更新 1 或更新版本提供新的移轉功能，可讓 StorSimple 5000 7000 數列使用者將其資料移轉到 StorSimple 8000 數列裝置。|
|Azure 政府版入口網站中的可用性 | Azure 政府版入口網站中使用 StorSimple 更新 1 或更新版本。 如需詳細資訊，請參閱[部署內部部署 StorSimple 裝置政府版入口網站中](storsimple-deployment-walkthrough-gov.md)。|
|資料保護與可用性 | 更新 1 或更新版本的 StorSimple 8000 數列支援區域多餘儲存空間 (ZRS)，除了本機多餘儲存空間 (LRS) 和地理多餘的儲存空間 (GRS)。 請參閱[本文 Azure 儲存體重複選項](https://azure.microsoft.com/documentation/articles/storage-redundancy/)的 ZRS 詳細資料。|
|重要的應用程式的支援 | StorSimple 更新 2，您可以出本機釘選適當的區。 此功能可確保重要的應用程式所需的資料不層至雲端。 釘選至本機磁碟區沒有雲端延遲或連線問題。 如需有關固定在本機磁碟的詳細資訊，請參閱[使用 StorSimple 管理員服務管理區](storsimple-manage-volumes-u2.md)。|
|低延遲和高效能 | StorSimple 更新 2 可讓您建立虛擬利用高效能中、 低延遲功能的 Azure 進階版儲存空間的裝置。 StorSimple 進階版虛擬和裝置有關的詳細資訊，請參閱[部署及管理 StorSimple 虛擬裝置 Azure 中的](storsimple-virtual-device-u2.md)。|

![[視訊] 圖示](./media/storsimple-overview/video_icon.png)觀看[這段影片](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be)，以概略 StorSimple 8000 數列功能與優點。

## <a name="storsimple-components"></a>StorSimple 元件

Microsoft Azure StorSimple 方案包含下列元件︰

- **Microsoft Azure StorSimple 裝置**– 內部部署混合式儲存空間陣列，其中包含 SSDs 和 HDDs，加上多餘的控制站和自動容錯移轉功能。 控制站管理 tiering、 目前使用 （或快速鍵） 將資料放入本機的儲存空間 （在 [裝置或內部部署伺服器）] 移動至雲端的較少經常使用的資料時的儲存空間。
- **StorSimple 虛擬裝置**– 也稱為 StorSimple 虛擬應用裝置，這是 StorSimple 裝置複寫架構的軟體版本與大部分功能的實體混合式存放裝置。 Azure 虛擬機器中的單一節點上執行，StorSimple 虛擬裝置。 進階版虛擬裝置，善用進階版 Azure 儲存空間，是用於更新 2 及更新版本。
- **StorSimple 管理員服務**– Azure 傳統入口網站，可讓您從單一網頁介面管理 StorSimple 裝置或 StorSimple 虛擬裝置的副檔名。 若要建立和管理服務、 檢視和管理裝置、 檢視的提醒、 管理區，並檢視及管理備份的原則和類別目錄，您可以使用 「 StorSimple 管理員服務。
- **Windows PowerShell 的 StorSimple** – 您可以用來管理 StorSimple 裝置的命令列介面。 Windows PowerShell 的 StorSimple 有功能，讓您註冊 StorSimple 裝置、 設定您的裝置上的網路介面、 安裝特定類型的更新，存取支援工作階段，即可疑難排解在您的裝置，並將裝置狀態變更。 您可以連線到序列主控台或使用 Windows PowerShell 遠端存取 StorSimple Windows PowerShell。
- **Azure PowerShell StorSimple cmdlet** – Windows PowerShell cmdlet，可讓您從命令列服務等級與移轉工作自動化的集合。 StorSimple 的 Azure PowerShell cmdlet 的相關詳細資訊，請移至[cmdlet 參考](https://msdn.microsoft.com/library/dn920427.aspx)。
- **StorSimple 快照管理員**MMC 嵌入式管理單元用以大量群組和 Windows 區陰影複製服務產生應用程式一致的備份。 此外，您可以使用 StorSimple 快照管理員建立備份的排程和複製或還原區。 
- **StorSimple 介面卡的 SharePoint** -無障礙延伸至 Microsoft Azure StorSimple 儲存與資料保護功能 SharePoint 伺服器陣列，在進行 StorSimple 儲存空間，可檢視並從 [SharePoint 管理中心入口網站管理工具。

下圖中提供的 Microsoft Azure StorSimple 架構和元件的高層級檢視。

![StorSimple 架構](./media/storsimple-overview/overview-big-picture.png)

下列各節說明這些元件的較大的詳細資料，並說明方案如何排列資料、 配置儲存空間，和儲存管理與資料保護。 最後一節提供定義的一些重要詞彙和 StorSimple 元件和其管理相關的概念。

## <a name="storsimple-device"></a>StorSimple 裝置

Microsoft Azure StorSimple 裝置是內部部署混合式存放裝置陣列，提供主要儲存和 iSCSI 存取儲存在其上的資料。 它管理通訊與雲端儲存空間，並有助於確保安全性與機密性的所有資料儲存於 Microsoft Azure StorSimple 解決方案。

StorSimple 裝置包含 SSDs 和硬碟 HDDs，以及支援叢集和自動容錯移轉。 包含共用的處理器、 共用及兩個左右對稱的控制器。 每個控制站提供下列功能︰

- Host （主機） 電腦的連線
- 連線至區域網路 (LAN) 最多六個網路連接埠
- 硬體監視
- 靜態隨機存取記憶體 （開機項目識別碼，） 會保留資訊，即使電源中斷
- 叢集更新管理容錯移轉叢集在伺服器上的軟體更新，以便更新已最小或不會影響服務可用性
- 叢集的服務，哪些功能，例如後端叢集，提供高可用性和最小化如果 HDD 或 SSD 失敗，或可能會發生任何負面影響離線

只有一個控制站是作用中的任何點時間。 如果作用中的控制器失敗，請在第二個控制站會變成作用中自動。 

如需詳細資訊，請到[StorSimple 硬體元件的狀態](storsimple-monitor-hardware-status.md)。

## <a name="storsimple-virtual-device"></a>StorSimple 虛擬裝置

您可以使用 StorSimple 建立虛擬裝置複寫架構及實體混合式存放裝置的功能。 Azure 虛擬機器中的單一節點上執行，StorSimple 虛擬裝置 （也稱為 StorSimple 虛擬應用裝置）。 （虛擬裝置只能建立 Azure 虛擬機器上。 您無法建立一個 StorSimple 裝置或內部部署伺服器）。 

虛擬裝置有下列功能︰

- 它與 round 類似的實體應用裝置，並可提供給在雲端的虛擬機器 iSCSI 介面。 
- 您可以建立虛擬裝置數量在雲端，並視需要將其開啟和關閉。 
- 它可以協助模擬內部部署環境中損毀修復、 開發及測試方案，並從備份的項目層級擷取可以協助。 

更新 2 及更新版本 StorSimple 虛擬裝置有兩種模型︰ 8010 （先前稱為 1100年模型） 裝置和裝置 8020。 8010 裝置有 30 TB 的最大的容量。 8020 裝置，可以利用 Azure 進階版儲存空間，會有 64 TB 的最大的容量。 （本機層，Azure 進階版儲存儲存資料上 SSDs 而標準的儲存空間將資料儲存在 HDDs。）請注意，您必須使用進階版儲存 Azure 進階版儲存帳戶。 如需進階版儲存空間的詳細資訊，請移至[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。

如需 StorSimple 虛擬裝置的詳細資訊，請移至[部署及管理 StorSimple 虛擬裝置 Azure 中的](storsimple-virtual-device-u2.md)。

## <a name="storsimple-manager-service"></a>StorSimple 管理員服務

Microsoft Azure StorSimple 提供的 web 式使用者介面 （StorSimple 管理員服務），可讓您集中管理資料中心和雲端儲存空間。 您可以使用 「 StorSimple 管理員服務來執行下列工作︰

- 設定系統 StorSimple 裝置。
- 設定及管理 StorSimple 裝置的安全性設定。
- 雲端認證與屬性設定。
- 設定及管理伺服器上的區。
- 設定大量群組。
- 備份及還原的資料。
- 監控效能。
- 檢閱系統設定，並找出可能的問題。

您可以使用 「 StorSimple 管理員服務執行所有系統管理工作，除了需要系統停機時間，例如初始安裝和安裝的更新。

如需詳細資訊，請參閱[管理您的 StorSimple 裝置 StorSimple 管理員服務](storsimple-manager-service-administration.md)。

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell 的 StorSimple

Windows PowerShell 的 StorSimple 提供可用來建立和管理的 Microsoft Azure StorSimple 服務設定和監視 StorSimple 裝置的命令列介面。 這是 Windows PowerShell 為基礎的命令列介面包含適用於管理 StorSimple 裝置專用指令程式。 Windows PowerShell 的 StorSimple 具有功能，讓您︰

- 註冊裝置。
- 在裝置上設定的網路介面。
- 安裝特定類型的更新。
- 存取支援工作階段，即可疑難排解在您的裝置。
- 變更裝置狀態。

您可以存取 Windows PowerShell StorSimple 從循序主控台 （主機在電腦上直接連接到裝置） 或遠端電腦上使用 Windows PowerShell 遠端處理。 請注意，部分的 Windows PowerShell 以 StorSimple 工作，例如初始裝置註冊，才可在循序主控台。 

如需詳細資訊，移至[使用 Windows PowerShell 的 StorSimple 來管理您的裝置](storsimple-windows-powershell-administration.md)。

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple cmdlet

Azure PowerShell StorSimple cmdlet 是 Windows PowerShell cmdlet，可讓您從命令列服務等級與移轉工作自動化的集合。 StorSimple 的 Azure PowerShell cmdlet 的相關詳細資訊，請移至[cmdlet 參考](https://msdn.microsoft.com/library/dn920427.aspx)。

## <a name="storsimple-snapshot-manager"></a>StorSimple 快照管理員

StorSimple 快照管理員是 Microsoft 管理主控台 (MMC) 嵌入式管理單元可供您建立一致的當地時間點備份與雲端資料。 嵌入式管理單元執行 Windows Server 型主機上。 您可以使用 StorSimple 快照管理員︰

- 設定、 備份及刪除區。
- 設定大量群組，以確保備份資料是一致的應用程式。
- 管理備份原則，以便在預定的排程執行備份及儲存在指定的位置 （本機或在雲端） 資料。
- 還原區及個別檔案。

備份會擷取為快照，因為最後一個快照記錄的變更，並要求比完整備份少儲存空間。 您可以建立備份排程或視需要立即引起其他的備份。 此外，您可以使用 StorSimple 快照管理員，以建立保留原則多少快照集將會儲存該控制項。 如果您就需要若要從備份，StorSimple 快照管理員可讓您選取從目錄的本機或雲端快照。 

如果發生損毀，或如果您要還原的另一個原因資料，請 StorSimple 快照管理員將它還原從屬參照視是。 還原資料時，不需要時還原檔案、 取代設備，或將作業移到另一個網站關閉整個系統。

如需詳細資訊，請移至[什麼是 StorSimple 快照管理員？](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Sharepoint StorSimple 介面卡

Microsoft Azure StorSimple 包含 StorSimple 介面卡的 SharePoint 中，直接將延伸到 SharePoint 伺服器陣列的 StorSimple 儲存與資料保護功能的選用元件。 介面卡可用於遠端 Blob 儲存體 (RBS) 提供者和 SQL Server RBS 功能，讓您可以將二進位大型物件移至 Microsoft Azure StorSimple 系統備份伺服器。 Microsoft Azure StorSimple 然後 BLOB 資料儲存在本機或在雲端，根據使用方式。

[SharePoint 管理中心入口網站中的 SharePoint StorSimple 介面卡可從管理。 因此，SharePoint 管理仍會保留集中式，並顯示可以在 SharePoint 伺服器陣列中的所有的儲存空間。

如需詳細資訊，請前往[StorSimple 介面卡的 SharePoint](storsimple-adapter-for-sharepoint.md)。 

## <a name="storage-management-technologies"></a>儲存管理技術
 
除了專用的 StorSimple 裝置、 虛擬裝置，以及其他元件，Microsoft Azure StorSimple 會使用下列軟體技術，提供快速存取資料，並減少儲存消耗︰

- [自動儲存 tiering](#automatic-storage-tiering) 
- [型佈建](#thin-provisioning) 
- [Deduplication] 和 [壓縮](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>自動儲存 tiering

Microsoft Azure StorSimple 自動排列根據目前的使用狀況、 年齡和其他資料的關聯性的邏輯階層中的資料。 最作用中的資料儲存在本機，而不作用中] 與 [非使用中的資料會自動移轉至雲端。 下圖顯示此儲存空間的方法。
 
![StorSimple 儲存層](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

若要啟用快速存取，StorSimple 非常作用中資料儲存 （資料） 上 SSDs StorSimple 裝置中。 儲存有時候使用的資料 （暖資料） 上 HDDs 裝置，或在資料中心的伺服器上。 移動非作用中的資料，備份資料，並保留資料保存或法規遵循用途至雲端。 

>[AZURE.NOTE] 在更新 2 或更新版本，您可以指定在本機釘選音量，資料會保留在本機的裝置上的這種情況下，且不層至雲端。 

StorSimple 調整會重新排列資料，並使用模式指派儲存變更。 例如，部分資訊可能會降低作用中一段時間。 變得較少作用中時，它是從移轉 SSDs HDDs 然後至雲端。 如果再次相同資料變成作用中時，它會移轉回到存放裝置。

儲存 tiering 程序，如下所示︰

1. 系統管理員設定 Microsoft Azure 雲端儲存空間帳戶。
2. 系統管理員使用循序主控台和 （Azure 傳統入口網站中執行） StorSimple 管理員服務設定裝置及檔案伺服器，建立區與資料保護原則。 內部部署電腦 （例如檔案伺服器） 會使用網際網路小型電腦系統介面 (iSCSI) 來存取 StorSimple 裝置。
3. 一開始，StorSimple 快速 SSD 層的裝置上的資料儲存。
4. 為 SSD 層接近容量，StorSimple deduplicates 壓縮最舊的資料區塊，並將其移至 HDD 層。
5. 為 HDD 層方法容量、 StorSimple 會加密最舊的資料區塊，並安全地傳送到 Microsoft Azure 儲存體帳戶透過 HTTPS。
6. Microsoft Azure 資料的多個複本其資料中心，且建立遠端資料中心，確保如果發生損毀，可以復原資料。 
7. 當檔案伺服器要求資料儲存在雲端時，StorSimple 流暢地將其傳回並將複本儲存在 SSD 層的 StorSimple 裝置上。

### <a name="thin-provisioning"></a>型佈建

型佈建是出現超過實體資源的可用儲存空間的虛擬化技術。 事先預約足夠的儲存空間，而不是 StorSimple 使用型佈建配置只足夠的空間以符合目前的需求。 彈性的雲端儲存空間性質協助這種方法，因為 StorSimple 可以增加或減少符合變更要求的雲端儲存空間。 

>[AZURE.NOTE] 不狗熊佈建固定在本機磁碟。 建立區時，完整儲存至本機專用大量配置是佈建後。

### <a name="deduplication-and-compression"></a>Deduplication] 和 [壓縮

Microsoft Azure StorSimple 使用 deduplication 及資料壓縮進一步降低儲存的需求。

Deduplication 減少整體排除中儲存的資料集的複本儲存的資料。 變更資訊，如 StorSimple 會忽略未變更的資料，並擷取的變更。 此外，StorSimple 減少儲存的資料識別並移除不必要的資訊。 

>[AZURE.NOTE] 在 [固定至本機磁碟上的資料不 deduplicated 或壓縮。 不過，固定在本機磁碟區的備份 deduplicated 而壓縮。

## <a name="storsimple-workload-summary"></a>StorSimple 工作量摘要

支援的 StorSimple 工作負載的摘要時即下方形成表格。

| 案例                  | 工作負載                | 支援 |  限制                                  | 版本              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| 共同作業             | 共用檔案            | [是]       |                                                | 所有版本         |
| 共同作業             | 分散式的檔案共用| [是]       |                                                | 所有版本         |
| 共同作業             | SharePoint              | [是] *      |僅支援固定在本機磁碟      | 更新 2 及更新版本   |
| 保存                  | 簡易檔案封存   | [是]       |                                                | 所有版本         |
| 虛擬化            | 虛擬機器        | [是] *      |僅支援固定在本機磁碟      | 更新 2 及更新版本   |
| 資料庫                  | SQL                     | [是] *      |僅支援固定在本機磁碟      | 更新 2 及更新版本   |
| 視訊監視        | 視訊監視      | [是] *       |時 StorSimple 裝置致力於為此工作負載只支援| 更新 2 及更新版本   |
| 備份                    | 主要目標備份 | [是] *      |時 StorSimple 裝置致力於為此工作負載只支援| 更新 3 及更新版本 |
| 備份                    | 第二個目標備份 | [是] *      |時 StorSimple 裝置致力於為此工作負載只支援| 更新 3 及更新版本 |

*是 & #42;應該套用方案指導方針和限制。*

StorSimple 8000 數列裝置不支援下列的工作量。 如果部署上 StorSimple，這些工作負載會導致不受支援的設定。

-  醫療影像
-  Exchange
-  在 VDI
-  Oracle
-  SAP
-  顯示較大的資料
-  內容的通訊群組
-  從 SCSI 開機

以下是支援 StorSimple 基礎結構元件的清單。 

| 案例 | 工作負載      | 支援 |  限制                                 | 版本      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| 一般  | Express 路由 | [是]       |                                               |所有版本 |
| 一般  | DataCore FC   | [是] *       |支援 DataCore SANsymphony            | 所有版本 |
| 一般  | DFSR          | [是] *      |僅支援固定在本機磁碟     | 所有版本 |
| 一般  | 編製索引      | [是] *       |分層區，僅中繼資料支援索引 （無資料）。<br>釘選至本機磁碟區，被支援完整索引。| 所有版本 |
| 一般  | 防毒軟體    | [是] *       |分層區，僅限掃描 [開啟和關閉支援。<br> 釘選至本機磁碟，被支援完整掃描。| 所有版本 |

*是 & #42;應該套用方案指導方針和限制。*

## <a name="storsimple-terminology"></a>StorSimple 詞彙 

部署之前 Microsoft Azure StorSimple 方案，建議您檢閱下列詞彙和定義。

### <a name="key-terms-and-definitions"></a>重要詞彙和定義

| 字詞 （縮寫或縮寫） | 描述 |
| ------------------------------ | ---------------- |
| access 控制項記錄 (ACR)    | 決定哪些主機可以連線到 Microsoft Azure StorSimple 裝置上的大量相關聯的一筆記錄。 判斷根據 iSCSI 完整名稱 (IQN) 連線到您的 StorSimple 裝置的主機 （包含在 ACR）。|
| AES 256                        | 256 位元進階加密標準 (AES) 演算法來加密資料時，將移動至雲端。 |
| 配置單位大小 （澳洲）     | 最小的可配置來保留您在 Windows 中的檔案的磁碟空間量檔案系統。 如要保留的檔案 （進位至最下一個叢集大小） 如果檔案大小不是叢集大小的倍數，必須使用額外的空間造成遺失的空格及片段的硬碟。 <br>建議的澳洲 Azure StorSimple 區是 64 KB，因為其運作方式與 deduplication 演算法。|
| 自動的儲存 tiering      | 然後啟用的所有的儲存空間，從中央的使用者介面的 [管理和自動移動 SSDs 從較少的作用中的資料，HDDs 然後至雲端，層。|
| 備份的目錄 | 備份，通常與相關應用程式類型所用的集合。 此集合會顯示在 StorSimple 管理員服務使用者介面的備份型錄頁面。|
| 備份目錄檔案             | 包含目前儲存備份資料庫的 StorSimple 快照 Manager 中可用的快照集清單的檔案。 |
| 備份原則                   | 選取範圍的區與類型的備份，可讓您建立備份的預先定義的排程時間。|
| 二進位大型物件 (Blob)    | 為單一項目儲存在資料庫管理系統的二進位資料集合。 二進位大型物件通常是圖像、 音訊或其他多媒體的物件，但有時候二進位可執行的程式碼儲存為 BLOB。|
| 挑戰交換驗證通訊協定 (CHAP) | 用來驗證的連線，根據共用密碼或私人對等通訊協定。 CHAP 可以是單向或相互。 單向孩子，目標會驗證啟動器。 相互 CHAP 需要目標驗證啟動器和啟動器驗證目標。 | 
| 複製                          | 大量的複本。 |
|雲端為層 (CaaT)          | 雲端儲存空間，讓所有的儲存空間似乎是一個企業儲存網路的一部分，為內的儲存空間架構層整合。|
| 雲端服務提供者 (CSP)   | 雲端運算的服務提供者。|
| 雲端快照                 | 儲存在雲端的大量資料的時間點複本。 雲端快照等於不同、 離線儲存系統上所複製的快照。 雲端快照集就非常有用損毀修復狀況。|
| 雲端儲存空間加密金鑰   | 密碼或 StorSimple 裝置用來存取您的裝置傳送至雲端的加密的資料的金鑰。|
| 叢集更新         | 管理容錯移轉叢集在伺服器上的軟體更新，以便更新已最小或服務可用性不會影響。|
| 資料路徑                       | 執行間連線的資料處理作業的功能單位的集合。|
| 停用                     | 永久巨集指令線 StorSimple 裝置與相關聯的雲端服務之間的連線。 雲端快照的裝置會保留在此程序之後，和可以複製或用於損毀修復。|
| 磁碟鏡像                 | 邏輯的磁碟區不同硬碟上的複寫磁碟機中進行即時中，以確保連續的可用性。|
| 動態磁碟鏡像         | 在動態磁碟上的邏輯磁碟區複寫。|
| 動態磁碟                  | 使用邏輯磁碟管理員 (LDM) 來儲存和管理資料在多個實體磁碟的磁碟大量格式。 動態磁碟可以放大提供更多的可用空間。|
| 擴充的磁碟 Bunch (EBOD) 圍繞符號 | Microsoft Azure StorSimple 裝置包含額外硬碟機的額外儲存空間的磁碟的次要圍繞符號。|
| fat 佈建               | 傳統的儲存空間佈建配置中的儲存空間根據預期需求 （，通常是超出目前需要）。 另請參閱*型佈建*。|
| 硬碟 (HDD)          | 用來儲存資料的旋轉堅硬磁碟機。|
| 混合式雲端儲存空間           | 使用本機和離資源，包括雲端儲存空間的儲存空間架構。|
| 小型電腦系統介面網際網路 (iSCSI) | 網際網路通訊協定 IP 為基礎的儲存空間網路標準連結資料儲存區設備。|
| iSCSI 啟動器                 | 軟體元件，可讓執行 Windows 連線到外部的 iSCSI 儲存空間網路主機電腦。|
| iSCSI 完整名稱 (IQN)      | 唯一的名稱，以識別 iSCSI 目標或啟動者。|
| iSCSI 目標                    | 在儲存空間的區域網路磁碟子系統提供集中式的 iSCSI 軟體元件。|
| live 封存                  | 在其中保存資料是存取所有的時間 （它不會儲存開膠帶貼上，例如） 以儲存方法。 Microsoft Azure StorSimple 使用即時封存。|
|本機固定的音量 | 大量的裝置上並不會層至雲端。 |
| 本機快照                  | 儲存在 Microsoft Azure StorSimple 裝置的大量資料的時間點複本。|
| Microsoft Azure StorSimple      | 強大的解決方案，組成的資料中心存放應用裝置和軟體，可讓 IT 組織當作資料中心的儲存空間，請運用雲端儲存空間。 StorSimple 簡化資料保護與資料管理，同時降低成本。 解決方案合併主要儲存空間、 封存、 備份和損毀修復 (DR 透過運用雲端緊密地整合)。 藉由組合企業級平台上的舊儲存與雲端資料管理，StorSimple 裝置啟用速度，簡化和可靠性，所有的儲存空間相關需求。|
| 和製冷模組 (PCM)  | 包含 power 耗材和冷卻風扇，因此 Power 的名稱及製冷模組 StorSimple 裝置的硬體元件。 裝置的主索引的圍繞符號會有兩個 764W PCMs EBOD 圍繞符號含有兩個 580W PCMs。|
| 主要的圍繞符號               | 包含應用程式的平台控制站 StorSimple 裝置的主的圍繞符號。|
| 復原時間目標 (RTO)   | 最大應該之前商務程序或系統耗用的時間量就完全復原損毀。| 
|循序附加 SCSI (SA)       | 硬碟 (HDD) 一種。|
| 服務資料加密金鑰     | 註冊 StorSimple 管理員服務所做的任何新的 StorSimple 裝置可用的金鑰。 設定資料的 StorSimple 管理員服務與裝置之間傳送使用公開金鑰加密，然後再可以解密僅在使用私密金鑰的裝置。 服務資料加密金鑰可讓您取得此私密金鑰解密的服務。|
| 服務登錄機碼        | 協助登錄 StorSimple 裝置的 StorSimple 管理員服務，讓它會出現在 Azure 傳統的入口網站，進一步管理動作的索引鍵。|
| 小型電腦系統介面 (SCSI) | 標準實際連線到電腦，以及將資料傳遞它們之間的一組。|
| 實心狀態磁碟機 (SSD)         | 包含任何移動的組件; 磁碟例如，快閃磁碟機。|
| 儲存帳戶                 | 存取認證的一組連結至您儲存的帳戶指定的雲端服務提供者。| 
| Sharepoint StorSimple 介面卡| 無障礙延伸到 SharePoint 伺服器陣列的 StorSimple 儲存與資料保護 Microsoft Azure StorSimple 元件。|
| StorSimple 管理員服務      | Azure 傳統入口網站，可讓您管理您的 Azure StorSimple 內部部署和虛擬裝置的延伸。|
| StorSimple 快照管理員     | Microsoft 管理主控台 (MMC) 嵌入式管理單元管理 Microsoft Azure StorSimple 中的備份與還原作業。|
| 需要備份                     | 這項功能，可讓使用者取得大量的互動式備份。 這是進行手動備份區而不是採取透過定義的原則自動的備份的替代方法。|
| 型佈建               | 最佳化的可用儲存空間用於儲存系統的效率的方法。 在型佈建儲存空間配置之間根據特定時間的每位使用者所需的最低空間的多個使用者。 另請參閱*fat 佈建*。|
| tiering | 排列根據目前的使用狀況、 年齡和其他資料的關聯性的邏輯群組中的資料。 StorSimple 自動排列層中的資料。   |
| 音量                          | 磁碟機的形式呈現的邏輯存放區。 StorSimple 區會對應到裝載主應用程式，包括發現 iSCSI 和 StorSimple 裝置的區。|
 | 大量容器                | 區和套用至這些設定的群組。 您 StorSimple 裝置中的所有磁碟區會分組為大量容器。 大量容器設定包括儲存帳戶、 資料傳送至雲端與相關聯的加密金鑰，並使用涉及雲端運算的頻寬的加密設定。|
| 大量群組                    | StorSimple 快照管理員中，大量群組是設定為促進備份處理區的集合。|
| 大量陰影複製服務 (VSS)| Windows 伺服器作業系統服務，讓應用程式的一致性以進行共同作業的累加快照建立 VSS 注意應用程式與通訊。 VSS 可確保快照是時，會暫時停用應用程式。|
| Windows PowerShell 的 StorSimple | 用來操作，並管理您的 StorSimple 裝置以 Windows PowerShell 命令列介面。 維持的 Windows PowerShell 的基本功能，此介面會有其他都能管理 StorSimple 裝置的專用的 cmdlet。|


## <a name="next-steps"></a>後續步驟

瞭解[StorSimple 安全性](storsimple-security.md)。




 

 
