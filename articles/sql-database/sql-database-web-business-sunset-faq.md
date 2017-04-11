<properties
   pageTitle="Azure SQL 資料庫網頁和商務版日落的常見問題集 |Microsoft Azure"
   description="瞭解當 Azure SQL 網頁和商務資料庫會已停用，並進一步瞭解的功能和新的服務層的功能。"
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>網站與商務版日落的常見問題集

Azure SQL 網頁和商務資料庫現在已停用。 基本、 標準、 進階版及彈性層取代淘汰網站和商務資料庫。

為協助您升級網站和商務資料庫，SQL 資料庫服務建議以適當的服務層和效能等級 （價格層） 根據其歷程記錄工作負載的每個資料庫。

**若要取得價格層建議︰**

- [升級至 SQL 資料庫 V12 使用 Azure 入口網站](sql-database-upgrade-server-portal.md)
- [使用 PowerShell 以 SQL 資料庫 V12 升級](sql-database-upgrade-server-powershell.md)
- [變更網頁或企業版的資料庫的價格層](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>為什麼 Azure 入口網站會顯示我網頁和企業版資料庫，已停用？

網頁版和企業版資料庫年 9 月 2015 年之後將無法使用，因為入口網站標示為已停用的網頁和商務資料庫。 已停用的標籤也會提供的任何網站和商務資料庫應該可升級至標準]、 [基本] 或 [進階版的提醒。 升級現有的網站或商務資料庫至新的服務層的詳細資訊，請參閱[升級至 Azure SQL 資料庫 V12](sql-database-upgrade-server-portal.md)。

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>哪些新的服務層是升級我現有的網站或商務資料庫，最佳選擇？

選取適當新服務層和效能層級的現有的網站或商務資料庫取決於您的應用程式的特定功能和效能需求。

使用價格層建議的上方，或以的詳細資訊，協助您選取適當的新服務層級，請參閱[升級至 Azure SQL 資料庫 V12](sql-database-upgrade-server-portal.md)。

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Microsoft 介紹新服務層的為何？

根據客戶意見反應，Azure SQL 資料庫會介紹新服務層級，以協助客戶詳細輕鬆支援關聯式資料庫的工作量。 新的層級的設計跨輕量型重量交易應用程式要求的 7 個層級的範圍進行預期的效能。 此外，新層提供某範圍的業務連續性功能，更嚴密的執行時間 SLA，更大的資料庫大小小於金錢和改良的帳單體驗。

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>何處可以進一步瞭解新的服務層？

如需詳細瞭解新的服務層和效能模型的詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。 若要取得詳細價格新服務層的資訊，請參閱[SQL 資料庫價格](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>不會提供基本、 標準和進階功能為何？

「 聯盟 」 功能會使用網頁版和企業版已停用。 改為使用或[彈性的資料庫](sql-database-elastic-scale-get-started.md)工具移轉[Azure SQL 資料庫](sql-database-elastic-scale-get-started.md)，可簡化建立和管理的應用程式使用 sharding 都需要擴充其資料庫的客戶。 .NET 用戶端文件庫可讓應用程式，以定義資料對應至適當的資料庫擊碎和路由 OLTP 要求的方式。 若要支援的資料分佈擊碎之間的方式重新設定管理作業，可 Azure 雲端服務範本是包含您可以主控自己 Azure 訂閱中。 [彈性的資料庫工具](sql-database-elastic-scale-get-started.md)] 下，除了 Microsoft 會繼續建立及發佈[自訂 sharding 圖樣及作法指引](https://msdn.microsoft.com/library/azure/dn764977.aspx)根據檢討的深客戶的互動。 需要延展功能的新客戶應該取出[彈性的資料庫工具](sql-database-elastic-scale-get-started.md)及/或連絡 Microsoft 支援服務評估他們的選項。

Microsoft 也會變更資料庫複製體驗進階版的資料庫。 進階資料庫配額是受限制，先前建立資料庫... 為在複本 T SQL 建立暫停進階版的資料庫不保留資源，以商務資料庫相同工資率支付薪資。 現在更免費進階版配額現狀，將不再支援暫停進階版。 資料庫複本現在會建立相同的版本與來源的效能層級，而且將會相應地付款。 客戶可以選擇將降級至不同的服務層] 或 [效能等級，以減少其成本，如果想要複製的資料庫。 暫停進階版中現有的資料庫會轉換成商務版做為此版本的一部分。 預計的[時間點還原](sql-database-recovery-using-backups.md#point-in-time-restore)簡介會減少需要建立資料庫的備份複本。

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>如何並 Basic、 標準和進階版改善我帳單體驗？

基本、 標準、 和進階版 Azure SQL 資料庫付費給小時，您可以向上或向下調整每個資料庫。 您會根據您選擇每小時的最高服務層和效能等級固定速率付費。 此外，效能層級 (範例︰ 基本、 S1 和 P2) 發生在的帳單，使其看起來更清楚的資料庫天/時您所產生的每個效能層級的單一月數。 網頁和商務資料庫繼續使用資料庫單位根據資料庫大小，向收費。 請造訪[SQL 資料庫價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)若要進一步瞭解定價和新的服務層級之間的差異。


## <a name="see-also"></a>另請參閱

[Azure SQL 資料庫](https://azure.microsoft.com/documentation/services/sql-database/)

[Web 和商務價格](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[服務層](sql-database-service-tiers.md)

[升級至 Azure SQL 資料庫 V12](sql-database-upgrade-server-portal.md)
