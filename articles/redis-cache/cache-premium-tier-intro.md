<properties 
    pageTitle="Azure Redis 快取進階版層簡介 |Microsoft Azure" 
    description="瞭解如何建立和管理 Redis 持續性、 Redis 叢集，以及您的進階版層 Azure Redis 快取執行個體 VNET 支援" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Azure Redis 快取進階版層簡介
Azure Redis 快取是分散式的受管理的快取，可協助您建置高度擴充性和回應的應用程式提供快速存取您的資料。 

新的進階版層是企業版 」 好層，包括所有的標準層功能及其他內容，例如較佳的效能、 放大負載、 損毀修復、 匯入/匯出和增強的安全性。 繼續閱讀以進一步瞭解進階版快取層的其他功能。

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>更佳的效能比較標準或 Basic 層
**較高的效能透過標準或基本層。** 硬體會有更快速的處理器，並提供更佳的效能比較 Basic 或標準層部署中的進階版層快取。 進階版層快取有較高的輸出量和較低的延遲時間。 

**處理量為相同大小的快取中較高進階與標準層。** 例如，53 第 4 頁 GB 的處理量 （進階版） 快取是第二個與 150 K C6 的每個 250 K 要求 （標準）。

如需有關大小的詳細資訊，請處理量及頻寬與進階版的快取，請參閱[Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis 資料持續性
進階版層可讓您保存在 Azure 儲存體帳戶中的快取資料。 基本/標準快取中只在記憶體中儲存的所有資料。 若基礎結構有問題可以是可能的資料遺失。 建議您使用意指資料持續性功能在進階版層增加恢復資料遺失。 Azure Redis 快取提供 RDB 和很少 （即將推出） 中[Redis 持續性](http://redis.io/topics/persistence)的選項。 

如需設定持續性的相關指示，請參閱[如何設定持續進階版 Azure Redis 快取。](cache-how-to-premium-persistence.md)

##<a name="redis-cluster"></a>Redis 叢集
如果您想要建立的快取大於 53 GB，或要跨多個意指節點的晶怪資料，您可以使用意指叢集已在進階版層。 每個節點是由受 Azure 高可用性主要/複本快取組所組成。 

**意指叢集可讓您最大比例和處理量。** 處理量直線增加您增加擊碎 （節點） 叢集內的數字。 例如。 如果您建立的 10 個擊碎，第 4 頁叢集，然後使用處理量是 250 K * 10 = 秒 2.5 百萬要求。 請如需詳細資訊大小、 處理量，以及使用進階版的快取的頻寬[Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)，參閱。

若要開始使用叢集，請參閱[如何設定叢集進階版 Azure Redis 快取](cache-how-to-premium-clustering.md)。

##<a name="enhanced-security-and-isolation"></a>增強的安全性和隔離

在瀏覽器或標準層中建立的快取就可以存取公用網際網路上。 快取有限制存取根據便捷鍵。 與進階版層進一步您可以確保只有指定的網路中的用戶端可以存取快取。 您可以部署 Redis [Azure 虛擬網路 (VNet)](https://azure.microsoft.com/services/virtual-network/)中的快取。 您可以使用所有功能的子網路存取控制 」 原則，例如 VNet 及其他功能，以進一步限制存取意指。

如需詳細資訊，請參閱[如何設定進階版 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)。

## <a name="importexport"></a>匯入/匯出

匯入/匯出是 Azure Redis 快取資料管理作業可讓您將 Azure Redis 快取資料匯入或匯出 Azure Redis 快取中的資料，以匯入及匯出從進階版快取 Redis 快取資料庫 (RDB) 快照，以在 Azure 儲存體帳戶中的頁面 blob。 這可讓您移轉之間不同的 Azure Redis 快取執行個體，或填入資料之前使用快取。

匯入可從任何執行中的任何雲端或環境，包括意指 Linux、 Windows，或任何雲端提供者，例如 Amazon Web 服務與其他人所執行的意指伺服器開啟意指相容 RDB 檔案。 匯入資料時輕鬆建立預先填入資料快取。 匯入期間 Azure Redis 快取從 Azure 儲存體載入記憶體 RDB 檔案，然後插入快取中的按鍵。

匯出可讓您匯出 Azure Redis 快取以 Redis 相容 RDB 檔案中儲存的資料。 若要將資料從 Azure Redis 快取執行個體之一，到另一個或另一個意指伺服器，您可以使用這項功能。 在匯出程序，暫存檔案會建立上 VM 裝載 Azure Redis 快取伺服器執行個體，且檔案上傳到指定的儲存空間帳戶。 匯出作業完成後任一狀態為成功或失敗，請刪除暫存檔案。

如需詳細資訊，請參閱[如何將資料匯入及匯出 Azure Redis 快取中的資料](cache-how-to-import-export-data.md)。

## <a name="reboot"></a>重新啟動

進階版層可讓您重新啟動您快取視的一或多個節點。 這個選項可讓您測試您的應用程式，恢復失敗。 您可以重新啟動下列節點。

-   您的快取] 主版節點
-   從屬節點的快取
-   主要和備用的節點的快取
-   使用時的進階版快取與叢集，您可以重新啟動母片、 從屬或兩個節點的快取中的個別擊碎

如需詳細資訊，請參閱[重新啟動](cache-administration.md#reboot)，並[重新啟動的常見問題集](cache-administration.md#reboot-faq)。

## <a name="schedule-updates"></a>排程更新

排程的更新功能可讓您指定的快取進行的維修作業] 視窗。 指定進行的維修作業] 視窗時，任何意指伺服器會更新期間這個視窗。 指定進行的維修作業] 視窗，選取所要的日期，並指定進行的維修作業] 視窗會啟動每個小時。 請注意，進行的維修作業] 視窗時間以 utc 表示。 

如需詳細資訊，請參閱[排程更新](cache-administration.md#schedule-updates)與[排程更新常見問題集](cache-administration.md#schedule-updates-faq)。

>[AZURE.NOTE] 僅限 Redis 排程的維護期間所做的更新的伺服器。 進行的維修作業] 視窗不會套用至電子郵件地址] 或更新 VM 作業系統 Azure 更新。

## <a name="to-scale-to-the-premium-tier"></a>若要縮放到進階版層

若要縮放到進階版層，只需選擇的進階版中**變更價格層**刀。 您也可以調整快取來使用 PowerShell 和 CLI 進階版層。 如需逐步指示，請參閱[如何比例 Azure Redis 快取](cache-how-to-scale.md)，以及[如何自動化縮放比例的作業](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

## <a name="next-steps"></a>後續步驟

建立的快取及探索新的進階層功能。

-   [如何設定持續進階版 Azure Redis 快取](cache-how-to-premium-persistence.md)
-   [如何設定進階版 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)
-   [如何設定叢集進階版 Azure Redis 快取](cache-how-to-premium-clustering.md)
-   [如何將資料匯入及匯出資料從 Azure Redis 快取](cache-how-to-import-export-data.md)
-   [如何管理 Azure Redis 快取](cache-administration.md)
  

