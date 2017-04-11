<properties 
    pageTitle="如何設定進階版 Azure Redis 快取資料持續性" 
    description="瞭解如何設定及管理資料保存進階版層 Azure Redis 快取執行個體" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>如何設定進階版 Azure Redis 快取資料持續性

Azure Redis 快取有提供彈性的快取大小和功能，包括新的進階版層選擇不同的快取服務。

Azure Redis 快取進階版層包括叢集、 保存及虛擬網路支援等功能。 本文將說明如何設定持續進階版 Azure Redis 快取執行個體中。

其他進階快取功能的詳細資訊，請參閱[Azure Redis 快取進階版層簡介](cache-premium-tier-intro.md)。

## <a name="what-is-data-persistence"></a>什麼是資料保存？
意指持續可讓您保存意指中儲存的資料。 您也可以快照，並先備份資料，您可以在硬體失敗時載入。 這是基本或標準層位置記憶體已儲存的所有資料，並可以有可能的資料遺失，若快取節點向下在哪裡的失敗大優點。 

Azure Redis 快取提供意指持續使用[RDB 模型](http://redis.io/topics/persistence)，在 Azure 儲存體帳戶中儲存資料的位置。 持續性設定時，Azure Redis 快取保留意指快取磁碟根據可設定的備份頻率意指二進位格式的快照。 如果發生嚴重事件來停用 [主要] 和 [複本的快取，被重建快取，使用最新的快照。

期間建立快取，然後在 [現有的進階版快取**設定**刀，您可以從**新 Redis 快取**刀設定持續性。

## <a name="create-a-premium-cache"></a>建立進階版的快取

若要建立的快取及設定持續性，登入[Azure 入口網站](https://portal.azure.com)，按一下 [**新增**->**資料 + 的儲存空間**>**Redis 快取**。

![建立意指快取][redis-cache-new-cache-menu]

若要設定持續性，第一次選取其中一個**進階版**快取中**選擇您的價格層**刀。

![選擇您的價格層][redis-cache-premium-pricing-tier]

選取後價格層進階版，請按一下 [ **Redis 持續性**]。

![Redis 持續性][redis-cache-persistence]

下一節中的步驟說明如何在您新增的進階版快取設定意指保存。 意指持續性設定之後，按一下 [**建立**意指持續以建立新的進階版快取。

## <a name="configure-redis-persistence"></a>設定意指持續性

Redis **Redis 資料保存**刀上設定持續性。 為新的快取，此刀存取快取建立程序期間前一節所述。 現有的快取，從您快取**設定**刀存取**Redis 資料保存**刀。

![Redis 設定][redis-cache-settings]

若要啟用意指持續性，請按一下 [啟用 RDB （意指資料庫） 備份的 [**啟用**]。 若要停用意指保存在先前已啟用進階版快取，請按一下 [**停用**]。

若要設定的備份時間間隔，請從下拉式清單中選取**備份的頻率**。 選擇包含**15 分鐘**、 **30 分鐘**、 **60 分鐘**、 **6 小時**、 **12 小時**及**24 小時的時間**。 這段開始計算向下，在先前的備份作業成功完成後，經過時啟動新的備份。

按一下以選取要使用，請儲存帳戶**儲存的帳戶**，然後選擇 [**主索引鍵**] 或 [**次要鍵**使用**儲存空間鍵**下拉式]。 您必須選擇儲存帳戶快取中，為相同的區域，建議**進階版儲存**帳戶，因為進階版儲存較高處理量。 

>[AZURE.IMPORTANT] 如果儲存持續帳戶並重新產生金鑰，您必須在從**儲存鍵**下拉式 rechoose 所需的索引鍵。

![Redis 持續性][redis-cache-persistence-selected]

按一下**[確定**] 儲存持續設定。

超過備份頻率間隔時間之後的下一個備份 （或新的快取的第一個備份） 是啟動時。



## <a name="persistence-faq"></a>持續性常見問題集

下列清單包含有關 Azure Redis 快取持續常見問題的解答。

-   [我可以啟用保存在先前建立的快取？](#can-i-enable-persistence-on-a-previously-created-cache)
-   [我建立快取之後，可以變更備份頻率嗎？](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [為什麼我有 60 分鐘的備份頻率是否超過 60 分鐘備份之間？](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [發生什麼情況舊的備份新備份？](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [如果我有不同的大小調整和備份還原已縮放比例作業之前，會發生什麼情況？](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>我可以啟用保存在先前建立的快取？

是的同時在建立快取，然後在 [現有的進階版快取，您可以設定意指保存。

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>我建立快取之後，可以變更備份頻率嗎？

是的您可以變更**Redis 資料保存**刀上備份的頻率。 如需相關指示，請參閱[設定 Redis 保存](#configure-redis-persistence)。

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>為什麼我有 60 分鐘的備份頻率是否超過 60 分鐘備份之間？

備份頻率間隔就無法啟動，直到已順利完成前一個備份程序。 如果備份頻率是 60 分鐘花備份的程序 15 分鐘才能順利完成下, 一步備份無法啟動 75 分鐘後的前一個備份的開始時間為止。

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>發生什麼情況舊的備份新備份？

所有的備份，除了的最新的項目會自動刪除。 刪除可能不會立即發生，但不是會永遠保存較舊的備份。

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>如果我有不同的大小調整和備份還原已縮放比例作業之前，會發生什麼情況？

-   如果您有調整為較大的大小，也不會影響。
-   如果您有調整為較小的大小，而且您有自訂[資料庫](cache-configure.md#databases)設定的大於[資料庫限制](cache-configure.md#databases)的新的大小，這些資料庫中的資料無法還原。 如需詳細資訊，請參閱[是我自訂的資料庫時縮放比例設定受影響？](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   如果您有調整為較小的大小，並在較小的大小，以保留所有從最近的備份資料中沒有足夠的空間，就會還原程序，通常使用[allkeys lru](http://redis.io/topics/lru-cache)收回原則收回鍵。

## <a name="next-steps"></a>後續步驟
瞭解如何使用更多進階快取功能。

-   [Azure Redis 快取進階版層簡介](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
