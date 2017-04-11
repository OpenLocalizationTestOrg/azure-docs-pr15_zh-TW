<properties
   pageTitle="Azure SQL 資料庫 Azure 案例研究 Daxko/CSI |Microsoft Azure"
   description="瞭解如何 Daxko/CSI 使用 SQL 資料庫加速開發週期和增強效能及其客戶服務"
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
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI 用於 Azure，加速開發週期及增強其客戶服務及效能

![Daxko/CSI 標誌](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI 軟體遇到的挑戰︰ 其客戶的基底健身和重新建立中心正在快速成長，獎狀成功的它全面涵蓋所有內容的企業軟體的方案，但保持在該成長客戶 IT 基礎結構需求使用基本的測試公司的 IT 人員。 公司逐漸已受限於上升作業負荷，特別是針對管理其成長的資料庫。 較，該作業開銷已剪下到新的方案，例如新行動性功能，在公司的軟體開發資源。

根據 David Molina，產品開發的主管 Daxko/CSI，在 Azure 提供簡化資料庫的管理、 增加延展性和釋放聚焦於軟體，而不是選項的資源時所需的平台為-的-服務 (PaaS) 模型 CSI 軟體。 「 Azure SQL 資料庫是我們絕佳的選項。 不需要擔心維護 SQL Server、 容錯移轉叢集，以及所有其他的基礎結構需求不適合用於我們。 」

自移轉至 Azure，CSI 軟體需求兩個只管理超過 600 客戶資料庫作業的人員。 公司使用 Azure SQL 資料庫彈性集區將客戶資料庫根據大小，需要。

持續 Molina，「 客戶水彩變更立即。 前彈性的資料庫，有時候原本逾時和其他問題尖峰期間。 使用 Azure 彈性的資料庫，他們可以傳送視和使用本軟體順利。 」

改善效能的客戶，除了 Azure 彈性資料庫釋放 CSI 軟體資源，將焦點集中在開發新服務和功能，而不是處理作業] 和 [管理資料庫。 這些 IT 相關資源協助 CSI 軟體改善企業軟體提供，SpectrumNG，以協助加入比成員，提升教職員效率，並為人員和成員行動裝置存取互動式的工作和即時通知。

Azure 也有幫助 CSI 軟體加速，藉此提升開發和品質保證 (QA) 循環圖啟用自動化選項。 公司的 Azure 實作，建立管理員可以封裝設定] 按鈕的按一下元件。 Molina 所述，「 發行週期的一部分，問與答現在無法部署到測試環境中 Azure 密切模仿我們生產堆疊。 我們可以將部署建置立即我們的開發環境對變更。 這是最大優點的是，因為我們不需要的之前的不一致性致歉。 」

## <a name="offloading-to-the-cloud"></a>卸載至雲端

移動至雲端之前, CSI 軟體已成功建立自己的區域資料中心的 multitenant 基礎結構。 公司展開，它會面臨購買、 佈建與維護的硬體和軟體支援其客戶所需的所有增加初期難題。 IT 專業人員的處理作業變得另一個瓶頸導致佈建新資源和客戶推出新服務會降低。

CSI 軟體讀取到雲端選項消除負擔，使其無法著重於其程式碼，而非其作業。 公司發現許多上方雲端提供者只提供基礎結構-為-的-服務 (IaaS) 解決方案仍需要大型的 IT 人員，以管理 IaaS 堆疊。 最後，CSI 軟體取決於 Azure PaaS 解決方案是最適合的需求。 說明 Molina，」，我們可以同時減少我們 IT 開銷焦點放在我們的軟體產品，讓 Azure 取得硬體和系統軟體的方式，」。

## <a name="making-the-transition-to-azure"></a>Azure 提供轉場效果

選取後 Azure 其 PaaS 解決方案，CSI 軟體會開始移轉至雲端的後端架構和資料庫。 Azure 之前 SpectrumNG 客戶所需安裝 Windows Communication Foundation (WCF) 服務後端通訊的用戶端應用程式。 根據 Molina，「 雖然某些客戶主控自己的資料中心中的所有項目我們建置出要 multitenant 產品。 我們裝載的資料中心的所有項目使用 SQL Server 作為資料存放區。

「 我們也提供的產品包含成員具入口網站 （撰寫 ASP.net），這專為白色標示比對客戶的 web 目前狀態及可支援線上網頁與任何第三方整合 SOAP API 」。

移轉至雲端不需花架構。 根據 Molina，「 大部分的處理修改我們讀取設定檔資訊、 集中的連接字串修改和自動化包裝、 上傳，和部署我們的版本類似的方式的投入比。 」

若要開發建置自動化，CSI 軟體工程師會使用 PowerShell 的 Azure 和 REST Api 來建立套件和以上傳這些檔案版本每天晚上的開發環境。
整體轉場效果至 Azure 雲端部署會發生快速且順暢; CSI 軟體 IT 小組。 說明 Molina，「 在所有，我們 beta 環境雲端中對專案採取的三個 4 個星期內。 這是我們意外 win。 」

設定及測試環境之後, CSI 軟體開始移轉客戶。 已經在使用 CSI 軟體主機服務的客戶，都是幾乎順暢轉場效果。 從內部部署升級的客戶，移轉至雲端採取一些額外的時間，但仍主要免客戶和 CSI 軟體兩者均適用。

新的客戶，CSI 軟體的 IT 人員使用下列程序板這些 Azure:

1.  Azure 的 PowerShell 指令碼用來微調設定為新資料庫的客戶。所有的客戶開始進階版層，以確保足夠的轉場效果的初始處理量。
2.  可能的話，請 CSI 軟體會使用 Azure SQL 移轉精靈，將現有的資料移到 Azure SQL 資料庫執行個體。
3.  最後，Microsoft SQL Server 整合服務 (SSIS) 用來協調資料中的差異，或執行任何所需的資料清除。

現在，關於 99 CSI 軟體客戶都跨四個區域資料中心 （北美中央、 南部中央，東部，和西部） 裝載於 Azure。 在每個客戶的地理區域中具有資料中心，延遲是降到最低。

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Azure 彈性資料庫集區釋放 IT 資源

Azure 的幾項功能有幫助 CSI 軟體的基礎架構與作業至正在功能和集中的開發集中 shift。 可能是最大的優勢已從彈性的資料庫集區。

CSI 軟體目前提供關於 550 資料庫的客戶。 彈性的資料庫之前, 很難管理層結構中的許多資料庫。 選項管理員必須指派效能層根據尖峰客戶的需求，需要嚴重 IT 資源成本。 有彈性的資料庫集區，管理員可以指派進階版或標準的資料庫，請適當的租用戶然後移動根據大小的客戶，並需要。 客戶立即; 水彩彈性的資料庫集區的效果彈性的資料庫，再客戶逾時和其他問題尖峰使用期間，但有彈性的資料庫，客戶可能會遇到活動高速量視需要他們可以繼續使用 SpectrumNG 順利。

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure 作用中的地理複寫加速報告

多個 CSI 軟體客戶也善用的 Azure Active 地理複寫。 與使用中地理複寫，進位到四個可讀的次要資料庫可以設定的相同或不同的資料中心區域中。 CSI 軟體可讓您使用的作用中地理-複寫兩種方法︰ 首先，次要資料庫可在資料中心中斷或無法連線至主要資料庫。和次要資料庫第二，可讀取，可以用來卸載唯讀的工作量，例如報告工作。 有些 CSI 軟體客戶加速報表的工作流程使用此項優惠。

## <a name="csi-software-application-logic-and-architecture"></a>CSI 軟體應用程式邏輯和架構

SpectrumNG 使用 web 角色。 因為應用程式多租用戶，WCF 服務用來處理初始連線要求客戶。 為 Molina 狀態時，「 要求識別每位客戶，然後可讓我們建立他們執行上述程序必須執行的資料庫時的連接字串。 」

服務 web 層，CSI 軟體會利用 Azure 自動縮放比例，根據日期和時間。 可用的資源是自動增加，以容納較高的使用狀況上班時間，根據每個地區資料中心的時區。 資源也會設定為縮小週末，客戶的需求時較低。

     
![Daxko/CSI 架構](./media/sql-database-implementation-daxko/figure1.png)

圖 1。 雲端服務工作者角色繪製 Azure SQL 資料庫與從資料表儲存體半結構化的資料的結構化的資料。 SpectrumNG 使用者互動資料到雲端服務 web 角色。

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>使用行動應用程式的 web 應用程式與 web 計劃層

若要啟用新的方案，包括完成的行動裝置平台使用 Azure SQL 資料庫釋放資源 CSI 軟體會根據自訂的 API 裝載於 Azure web 應用程式。 在平台可讓比成員和教職員使用行動裝置來檢查排程、 書籍課程]，並接收郵件。

平台單一元件，才能使用服務導向架構 (SOA)，像銷售點系統 （位置） 」 或 「 銷售系統，立即將其移到另一個網頁方案，然後微調設定為支援元件，同時保留其他內容之原始的 web 方案的服務。 這項功能可讓 CSI 軟體彈性，並協助降低成本。

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure 可讓應用程式與服務的 CSI 軟體開發人員焦點

Azure SQL 資料庫不只一大 SpectrumNG 客戶，享受快速、 可靠的服務，也是 CSI 軟體的最大優點 IT 人員及開發人員。 卸載至 Azure 雲端中的選項，CSI 軟體精簡其負荷資源] 及 [基礎結構，大幅加速其開發週期和不再需求，若要最佳化效能，其租用戶 micromanage 資料庫。

## <a name="more-information"></a>詳細資訊

- 若要進一步瞭解 Azure 彈性資料庫集區，請參閱[彈性的資料庫集區](sql-database-elastic-pool.md)。

- 若要深入瞭解資料庫工具] 和 [彈性的縮放比例，請參閱[彈性的資料庫工具] 和 [彈性的縮放比例](sql-database-elastic-scale-get-started.md)。

- 若要進一步瞭解移轉 SQL Server 資料庫，請參閱[Azure SQL 移轉精靈]](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)。

- 若要進一步瞭解使用地理複寫，請參閱[使用中的地理複寫](sql-database-geo-replication-overview.md)。

- 若要深入瞭解 Web 角色和工作者角色，請參閱[工作者角色](../fundamentals-introduction-to-azure.md#compute)。 

- 若要進一步瞭解 Azure 服務匯流排，請參閱[Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)。

- 若要進一步瞭解自動調整大小，請參閱[縮放雲端服務](../cloud-services/cloud-services-how-to-scale.md)。
