<properties
   pageTitle="Azure SQL 資料庫 Azure 案例研究 Umbraco |Microsoft Azure"
   description="進一步瞭解 Umbraco 如何使用 SQL 資料庫快速佈建和縮放比例數以千計的租用戶在雲端服務"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco 使用快速佈建和小數位數的服務千分位在雲端的租用戶的 Azure SQL 資料庫

![Umbraco 標誌](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco 是熱門開啟來源內容管理系統 (CMS)，可以執行任何項目小型行銷活動或摺頁冊網站複雜的應用程式 Fortune 500 大企業和全域媒體網站。 

> 「 我們有很大社群的開發人員使用多個 100000 開發人員論壇和超過 350000 個是作用中的網站上執行 Umbraco 系統 」。

> — Morten Christensen，技術的潛在客戶，Umbraco

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

若要簡化客戶部署，Umbraco 新增 Umbraco--的-服務 (UaaS): 軟體-為-的-服務 (SaaS) 內容，不需要針對內部部署，提供內建的縮放比例，並讓開發人員，將焦點集中在產品創新，而不是解決方案管理負擔移除管理。 Umbraco 會依賴彈性的平台-為-的-服務 (PaaS) 模型所提供的 Microsoft Azure 提供所有這些優點。

UaaS 可讓 SaaS 客戶使用 Umbraco CMS 功能先前出其範圍。 這些客戶佈建工作 CMS 環境包含生產資料庫。 客戶可以新增開發和暫存的環境，根據其需求的兩個其他資料庫。 在新的環境要求時，自動化程序會指派的客戶資料庫自動。 資料庫已預先佈 Umbraco 從 Azure 彈性的可用 （請參閱圖 1） 的資料庫，因為秒數，可以開始新的資料庫。


![Umbraco 佈建生命週期](./media/sql-database-implementation-umbraco/figure1.png)

圖 1。 佈建週期 Umbraco 為服務 (UaaS)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure 彈性集與自動化簡化部署

Azure SQL 資料庫及其他 Azure 服務，Umbraco 客戶自我佈建他們的環境，然後 Umbraco 可以輕鬆地監控和管理資料庫直覺式的工作流程的一部分︰

1.  佈建

    Umbraco 維護 200 彈性的集區從可用的預先能夠資料庫的容量。 當新的客戶註冊 UaaS 時，Umbraco 提供新 CMS 環境近期即時客戶分派給他們可用性資料庫中的資料庫。

    當可用性資料庫達到其臨界值時，建立新的彈性資料庫時，且新的資料庫預先能夠視指派給客戶。

    實作已完全自動化使用 C# 管理文件庫和 Azure 服務匯流排佇列。

2.  利用

    客戶會使用自己的資料庫中的一到三個環境 （適用於生產、 臨時，及/或開發），每個。 客戶資料庫位於彈性的資料庫集區，這可讓 Umbraco 提供有效而不需要過度佈建的縮放比例。

    ![Umbraco 專案概觀](./media/sql-database-implementation-umbraco/figure2.png)

    ![Umbraco 專案詳細資料](./media/sql-database-implementation-umbraco/figure3.png)

    圖 2。 Umbraco--的-服務 (UaaS) 客戶網站，顯示 [專案概觀 」 和 「 詳細資料

    Azure SQL 資料庫來代表實際的資料庫交易所需的相對電力使用資料庫交易單位 (DTUs)。 UaaS 的客戶，在 [關於 10 DTUs，通常是操作資料庫，但各有視需要調整 elasticity。 這表示 UaaS 可以確保的客戶只能必要的資源，甚至在尖峰期間。 例如，最近的星期日晚上運動事件期間一個 UaaS 客戶經驗豐富的資料庫會尖峰遊戲期間最多 100 個 DTUs。 Azure 彈性的集區實現 Umbraco 支援不效能降低高要求。

3.  監視器

    Umbraco 監視器資料庫使用儀表板中 Azure 的入口網站，以及自訂的電子郵件提醒的活動。

4.  修復損毀

    Azure 提供兩種損毀修復 (DR) 選項︰ 作用中的地理複寫和地理還原。 選取公司應該的 DR 選項取決於其[業務連續性目標](sql-database-business-continuity.md)。

    作用中的地理複寫提供停機時間時最快的回應層級。 使用作用中的地理複寫時，您可以在不同區域，伺服器上建立四個可讀取次要連結，您都可以再啟動次要連結的任何容錯移轉失敗。

    Umbraco 不需要地理複製，但它會利用的 Azure 地理-還原] 來協助您確保在中斷時的最小停機時間。 地理還原依賴地理多餘的 Azure 儲存體中的資料庫備份。 可讓使用者在主要區域中的資料時，從備份複本還原。

5.  取消佈建

    刪除專案環境時，任何相關聯的資料庫 （開發、 臨時，或即時） 將會移除 Azure 服務匯流排佇列中清除期間。 此自動化程序會還原 Umbraco 的彈性的資料庫可用性集區，使其可供未來佈建維持最大使用量未使用的資料庫。

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>彈性的集區允許 UaaS 輕鬆縮放

利用 Azure 彈性的資料庫的資料庫，Umbraco 可以最佳化效能，其客戶，不需移轉或置中佈建。 Umbraco 目前有幾乎 3000 資料庫跨 19 彈性的資料庫資料庫，輕鬆地不按比例縮放視需要以納入其間的任何其現有 325,000 客戶或準備好部署 CMS 雲端中的新客戶的能力。

事實上，根據 Morten Christensen，在 Umbraco，技術會導致 「 UaaS 現在發生成長關於 30 每日的新客戶。 我們的客戶樂意方便也可以提供新專案秒數，請立即發佈的開發環境中使用 '單鍵部署' live 網站更新而進行快速做變更，如果他們找出錯誤。 」

如果客戶不不再需要在第二個及/或第三個環境，它只需要移除這些環境。 釋放空間可用於其他客戶當作 Umbraco 彈性的資料庫可用性區的資源。

![Umbraco 部署架構](./media/sql-database-implementation-umbraco/figure4.png)

圖 3。 在 Microsoft Azure UaaS 部署架構

##<a name="the-path-from-datacenter-to-cloud"></a>從雲端的資料中心的路徑

當 Umbraco 開發人員最初決定移至 SaaS 模型，他們知道他們必須建置服務的效益和擴充的方式。

> 「 彈性的資料庫集區是答對的我們 SaaS，提供因為我們可以向上和向下視撥打容量。 佈建很容易，而且我們安裝程式，我們可以將使用在最大值]。

> — Morten Christensen，技術的潛在客戶，Umbraco

「 我們想要解決客戶的問題，我們的時間都要花不管理基礎結構。 我們想要讓我們的客戶，以取得的大部分的值，輕鬆地 」 指出 Niels Hartvig，Umbraco 創立者。 「 我們最初視為主控伺服器，但是容量規劃了夢魘。 」 正巧，Umbraco 不是使用任何資料庫系統管理員，其中強調使用 UaaS 金鑰價值。

Umbraco 開發人員的其中一個重要的目標是提供 UaaS 客戶佈建環境，快速且不容量限制的方式。 但提供的專用的代管的服務 Umbraco 資料中心的需要許多處理高速量處理中的多餘的容量。 想要有也就是說新增會有已定期充分的大計算基礎結構。

此外，Umbraco 開發小組想要允許他們重複使用現有越好程式碼解決方案。 為 Umbraco 開發人員 Mikkel Madsen 狀態，「 我們滿意，我們已，例如 Microsoft SQL Server、 Microsoft Azure SQL 資料庫、 ASP.net 及 Internet Information Services (IIS) 熟悉的 Microsoft 開發工具。 再投資 IaaS 或 PaaS 雲端方案，我們要確認它會支援我們 Microsoft 工具與平台，因此我們不會對我們的程式碼基底進行大量的變更。 」

若要符合所有準則，Umbraco 尋找雲端合作夥伴與條件︰

-   足夠的容量和可靠性
-   支援 Microsoft 開發工具]，以便不會強制 Umbraco 工程師完全重建其的開發環境
-   所有的 UaaS 競相 （企業需要以確保他們可以快速存取他們的資料和其資料儲存於符合其地區法規需求的位置） 地理市場中的目前狀態

##<a name="why-umbraco-chose-azure-for-uaas"></a>為什麼 Umbraco 選擇 Azure UaaS

根據 Morten Christensen 」 之後考慮所有的選項，由於我們選取 Azure 其符合所有我們準則，從 [管理和擴充程度的瞭解和效益。 我們將在 Azure Vm 環境設定，而且每個環境有自己的 Azure SQL 資料庫執行個體，與彈性的資料庫集區中的所有執行個體。 區隔開發、 臨時和即時環境之間的資料庫，我們可以我們的客戶提供強大的效能隔離符合小數位數，這些 win。 」

持續 Morten，「 之前，必須手動佈建的 web 資料庫伺服器。 現在，我們不必思考。 所有項目自動化，從清理佈建。 」

Morten 功能也快樂 Azure 所提供的縮放功能。 「 彈性的資料庫集區是答對的我們 SaaS，提供因為我們可以向上和向下視撥打容量。 佈建很容易，而且我們安裝程式，我們可以將使用在最大值]。 Morten 狀態，「 彈性的資料庫，以及服務層基礎 DTUs，保證簡化會提供我們佈建視需要新的資源資料庫 power。 最近，其中一個較大的客戶都以其即時環境中的 100 DTUs。 使用 Azure，我們彈性的集區提供他們即時不必預測 DTU 需求所需的資源的客戶的資料庫。 簡單來說，我們的客戶取得預期，開啟周圍時間，我們可以滿足我們效能服務等級協定。 」

Mikkel Madsen 概括: 「 我們已擁抱連線常見的 SaaS 情況 （使用即時在登入新客戶） 的強大 Azure 演算法 （預先佈建資料庫，這兩個開發和 live） 我們應用程式模式的頂端 （Azure 服務匯流排佇列搭配使用 Azure SQL 資料庫） 的基礎技術。 」

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>使用 Azure UaaS 超過客戶期望

自選擇 Azure 身其 cloud partner，Umbraco 具有都能提供最佳化的內容管理效能，而不需要自行主控解決方案的 IT 資源投資 UaaS 客戶。 Morten 顯示，請為 「 我們喜歡的開發人員便利性及延展性 Azure 提供給我們，且我們的客戶滿意可靠性的功能。 整體來說，已經過我們的絕佳 win ！ 」
 
## <a name="more-information"></a>詳細資訊

- 若要進一步瞭解 Azure 彈性資料庫集區，請參閱[彈性的資料庫集區](sql-database-elastic-pool.md)。

- 若要進一步瞭解 Azure 服務匯流排，請參閱[Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)。

- 若要深入瞭解 Web 角色和工作者角色，請參閱[工作者角色](../fundamentals-introduction-to-azure.md#compute)。 

- 若要進一步瞭解虛擬網路，請參閱[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)。    

- 若要進一步瞭解備份及修復，請參閱[業務連續性](sql-database-business-continuity.md)。  

- 若要瞭解更多關於監控 ppols，請參閱[監控集區](sql-database-elastic-pool-manage-portal.md)。 

- 若要進一步瞭解 Umbraco 為服務，請參閱[Umbraco](https://umbraco.com/cloud)。

