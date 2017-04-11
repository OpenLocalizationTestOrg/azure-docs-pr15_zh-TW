<properties
   pageTitle="設計模式 multitenant SaaS 應用程式和 Azure SQL 資料庫 |Microsoft Azure"
   description="本文將告訴您的需求及執行位於雲端環境的應用程式需要考慮的 multitenant 資料庫及各種必須做的取捨這些模式與相關聯的常見的資料結構模式。 同時也會說明如何 Azure SQL 資料庫]，以彈性的集區和彈性的工具，協助解決這些需求無洩漏的方式。"
   keywords=""
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
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>設計模式 multitenant SaaS 應用程式和 Azure SQL 資料庫

本文中，您可以瞭解需求與相同資料 multitenant 軟體架構模式為雲端環境中執行服務 (SaaS) 的資料庫應用程式。 此外，同時也會說明您需要考慮的因素及折衷方案的不同的設計模式。 彈性的集區和 Azure SQL 資料庫中的彈性工具可協助您符合您特定需求而不會危害其他目標。

開發人員有時候會使需要針對其長期最佳的興趣之外，其設計 tenancy 模型資料的層 multitenant 應用程式時的選項。 一開始，至少開發人員可能會覺得開發與降低雲端服務提供者為考量優先於租用戶隔離成本或應用程式的延展性。 這個選項可能會導致客戶滿意度疑慮和昂貴的課程校正更新版本。

Multitenant 應用程式是裝載在雲端環境的應用程式，以及提供服務數百或千分位人員不共用或查看其他人的資料的租用戶的同一組。 範例是到租用戶中的雲端裝載環境提供服務的 SaaS 應用程式。

## <a name="multitenant-applications"></a>Multitenant 應用程式

Multitenant 應用程式中的資料與工作負載可以輕鬆地分割。 您可以分割資料和工作量，例如沿著租用戶界限因為大部分要求租用戶的範圍內。 該屬性中的資料和工作量，而其偏袒本文所述的應用程式模式。

開發人員在雲端的應用程式，包括整個範圍使用此類型的應用程式︰

- 會以 SaaS 應用程式轉換至雲端的合作夥伴資料庫應用程式
- 建立大綱，從雲端 SaaS 應用程式
- 直接、 客戶對外公開的應用程式
- 員工具企業應用程式

合作夥伴資料庫應用程式通常是專為雲端和開根號與 SaaS 應用程式是 multitenant 應用程式。 這些 SaaS 應用程式會傳送到他們的租用戶特殊的軟體應用程式與服務。 可以存取應用程式服務租用戶，並有相關聯的資料儲存為應用程式組件的完整的擁有權。 但利用 SaaS 優點，租用戶必須投降一些控制自己的資料。 信任 SaaS 服務提供者，以確保資料的安全及隔離從其他租用戶的資料。 這種 multitenant SaaS 應用程式的範例是 MYOB SnelStart，與 Salesforce.com。 每個這些應用程式可以分割沿著租用戶的界限及支援應用程式設計我們討論本文中的模式。

為客戶提供直接服務，或在 （通常稱為使用者，而不是租用戶） 的組織內的員工是 multitenant 應用程式範圍的另一個類別的應用程式。 客戶訂閱服務，並不是擁有的服務提供者會收集及儲存資料。 服務提供者有較不嚴格的需求，以保留超出政府強制規定隱私權法規隔離彼此的客戶資料。 這類客戶具 multitenant 應用程式的範例是 Netflix、 Spotify，和 Xbox LIVE 等媒體內容的提供者。 可輕鬆地分割的應用程式的其他範例客戶對外公開，網際網路比例應用程式] 或網際網路的項目 (IoT) 應用程式中的每一個客戶或裝置可以做為資料分割。 使用者和裝置，可以分隔分割邊界。

並非所有應用程式磁碟分割輕鬆沿著單一的屬性，例如租用戶、 客戶、 使用者或裝置。 複雜的企業資源規劃 (ERP) 應用程式，例如有產品、 訂單及客戶。 通常有複雜的結構描述與數以千計的高度互連式的表格。

任何單一磁碟分割策略可以套用至所有資料表，並使用應用程式的工作量。 本文著重於 multitenant 有可輕鬆地分割的資料與工作負載的應用程式。

## <a name="multitenant-application-design-trade-offs"></a>Multitenant 應用程式設計取捨

設計模式 multitenant 應用程式開發人員選擇通常根據下列因素的考量︰

-   **租用戶隔離**。 開發人員必須確定沒有租用戶有不想要的存取其他租用戶的資料。 隔離需求延伸至其他屬性，例如提供保護從雜訊其他例項，能夠還原租用戶的資料，及實作租用戶專屬的自訂。
-   **雲端資源成本**。 必須成本競爭力 SaaS 應用程式。 Multitenant 應用程式開發人員可能選擇最佳化以用於在使用雲端資源，例如儲存空間的成本計算成本。
-   **輕鬆 DevOps**。 Multitenant 應用程式開發人員必須併入隔離保護、 維護及監視的應用程式和資料庫結構描述，健康狀況和租用戶問題進行疑難排解。 在應用程式開發和作業複雜度直接將成本提高和挑戰的租用戶滿意度。
-   **延展性**。 累加新增更多的租用戶與容量需要用它租用戶的能力請務必成功 SaaS 作業。

這些因素有取捨比較到另一個。 最低成本雲端提供可能不會提供最方便的開發經驗。 請務必開發人員若要使應用程式設計程序謹慎的選擇這些選項及其折衷方案。

常用的開發模式是將一或多資料庫套件多個租用戶。 這個方法的優點是較低的成本，因為您使用的幾個資料庫及相對簡化使用有限的資料庫。 但一段時間，SaaS multitenant 應用程式開發人員會瞭解此選擇已在租用戶隔離能與延展性獲得超乎缺點。 如果租用戶隔離變得很重要，來防止未經授權的存取或雜訊例中共用的儲存空間的租用戶資料需要額外的工作。 開發和隔離維護成本，這個額外的工作可能會大幅拓展。 同樣地，如果需要新增租用戶，此設計模式通常需要轉散發資料庫正確調整應用程式的資料層之間的租用戶資料的專業知識。  

租用戶隔離通常是 SaaS multitenant 應用程式的公司或組織迎合基本需求。 開發人員可能會以認知優點簡化和成本租用戶隔離能與延展性 tempted。 此取捨可以證明複雜且昂貴服務成長以及租用戶隔離需求變得更重要，且受管理應用程式層。 不過，為客戶提供直接的消費者具服務的 multitenant 應用程式，在租用戶隔離可能會比最佳化雲端資源成本更低的優先順序。

## <a name="multitenant-data-models"></a>Multitenant 資料模型

常見的租用戶資料放置的設計作法遵循三個不同的模型，圖 1。


  ![Multitenant 應用程式的資料模型](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
圖 1: multitenant 資料模型的常見設計作法

-   **每個租用戶的資料庫**。 每個租用戶有自己的資料庫。 所有租用戶專屬的資料是限於租用戶的資料庫，與其他租用戶和其資料。
-   **共用資料庫 sharded**。 多個租用戶共用其中一個多個資料庫。 一組不同的租用戶是由使用分割策略例如雜湊、 範圍或清單分割分派給每個資料庫。 此資料分配策略通常稱為 sharding。
-   **共用資料庫單一**。 有時大型，資料庫適用於所有承租人，明示租用戶的識別碼欄中包含的資料。

> [AZURE.NOTE] 應用程式開發人員可能選擇不同的租用戶置於其他資料庫結構描述，然後使用結構描述名稱來區分不同的租用戶。 因為還是需要使用動態 SQL，而且不能在計劃快取有效我們不建議這種方法。 在本文的其餘部分，重點的類別 multitenant 應用程式共用的資料表的方法。

## <a name="popular-multitenant-data-models"></a>熱門 multitenant 資料模型

請務必評估不同類型的 multitenant 資料模型，以應用程式設計取捨我們已識別出。 這些因素協助描述稍早所述的三個常見 multitenant 資料模型和其資料庫使用圖 2] 中所示。

-   **隔離**。 租用戶間隔離的程度可以是資料模型達到多少租用戶隔離的量值。
-   **雲端資源成本**。 租用戶之間共用資源的數量最佳化雲端資源成本。 資源可以定義為計算，儲存空間的成本。
-   **DevOps 成本**。 應用程式開發、 部署及管理能力以便於減少整體 SaaS 作業成本。  

圖 2 Y 軸顯示租用戶隔離層級。 X 軸會顯示資源共用層級。 灰色，在中間的對角線箭號，指出 DevOps 成本，以增加或減少降低的方向。

![熱門 multitenant 應用程式的設計模式](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)圖 2︰ 熱門 multitenant 資料模型

圖 2 右下方會顯示可能很大，使用的應用程式模式共用單一的資料庫，並共用的資料表 （或分隔結構描述） 的方法。 請務必讓資源共用，因為所有租用戶單一資料庫中使用相同的資料庫資源 （CPU、 記憶體、 輸入輸出）。 但租用戶隔離有限。 您可能需要採取其他步驟來保護其他應用程式層的租用戶。 這些額外的步驟可能會大幅增加 DevOps 成本開發及管理應用程式。 延展性受限於硬體裝載資料庫的小數位數。

圖 2 」 左下處理程序說明多個租用戶 sharded 跨多個資料庫 （通常是，不同的硬體不按比例縮放的單位）。 每個資料庫裝載的地址的其他模式延展性考量的租用戶，子集。 如果需要更多的租用戶的更多能力，就可以輕易租用戶上新的資料庫配置給新硬體刻度] 單位。 不過，會減少資源共用的量。 僅限租用戶放在相同的 [刻度] 單位共用資源。 這種方法提供改善到租用戶隔離，因為許多租用戶仍會組不會自動受保護的其他人的動作。 應用程式的複雜度保持不變高。

圖 2 左上角四分之一是第三種方法。 它會將每個租用戶資料置於自己的資料庫。 此方法均有很好的租用戶隔離內容，但限制資源共用時每個資料庫具有自己專屬的資源。 這個方法是很好，如果所有的租用戶有如預期呈現的工作量。 如果租用戶負載變得難以預期，提供者無法最佳化資源共用。 不可預測性是常見 SaaS 應用程式。 提供者必須 [過度佈建到符合需求或較低的資源。 任一動作會產生更高的成本或較低的租用戶滿意度。 較高的租用戶跨共用資源會變成需要進行加值的解決方案。 增加資料庫的數目，也會增加 DevOps 成本部署與維護應用程式。 儘管有這些顧慮，這個方法會提供最佳和最簡單的方法隔離的租用戶。

這些因素也會影響客戶所選擇的 [設計模式︰

-   **租用戶資料的擁有權**。 應用程式的租用戶保留的自己的資料所有權偏袒單一資料庫每一租用戶的圖樣。
-   **縮放比例**。 應用程式為目標數百數千或數百萬種租用戶偏袒共用方法，例如 sharding 的資料庫。 隔離需求仍可能會造成的挑戰。
-   **值和商務模型**。 如果應用程式的每個-租用戶營收如果小型 （小於美元）、 隔離變得比較不重要的需求及共用的資料庫意義。 如果每個租用戶營收金額幾個或更多]，每個租用戶的資料庫模型比較可行的方法。 它也許可以協助降低開發成本。

指定顯示在圖 2 設計折衷方案，必須很好的租用戶隔離屬性合併與共用之間的租用戶的最佳資源理想 multitenant 模型。 此模型符合圖 2 右上方所述的類別。

## <a name="multitenancy-support-in-azure-sql-database"></a>Azure SQL 資料庫中的 multitenancy 支援

Azure SQL 資料庫支援大綱圖 2] 中的所有 multitenant 應用程式模式。 彈性的資料庫，它也支援結合絕佳資源共用應用程式的模式與每個承租人資料庫隔離優點方法 （請參閱圖 3 」 右上方處理程序）。 彈性的資料庫工具和功能 SQL 資料庫協助降低成本開發和操作有許多資料庫 （顯示在 [陰影] 區域圖 3） 的應用程式。 這些工具可協助您建立及管理應用程式使用的任何多重資料庫模式。

![Azure SQL 資料庫中的模式](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)圖 3: Multitenant 的應用程式模式中 Azure SQL 資料庫

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>每個租用戶的資料庫模型彈性的集區和工具

在 SQL 資料庫的彈性集區合併租用戶隔離與資源共用進一步支援的租用戶資料庫之間的每個租用戶的資料庫的方法。 SaaS 提供者建立 multitenant 應用程式的資料層解決方案 SQL 資料庫。 租用戶間共用資源資料庫服務層輪班應用程式層。 管理與查詢在資料庫之間的複雜性簡化彈性的工作、 彈性的查詢，彈性的交易和彈性的資料庫用戶端文件庫。

| 應用程式的需求 | SQL 資料庫功能 |
| ------------------------ | ------------------------- |
| 租用戶隔離與共用資源 | [彈性的集區](sql-database-elastic-pool.md)︰ 配置 SQL 資料庫資源資料庫，並在各種不同的資料庫間共用資源。 此外，個別資料庫可以視需要以配合租用戶負載中變更容量 demand 波峰上資料庫中繪製盡可能資源。 彈性集區本身可調整向上或向下視。 彈性的集區也可輕鬆管理和監視及疑難排解資料庫層級。 |
| 以便於 DevOps 跨資料庫 | [彈性的集區](sql-database-elastic-pool.md)︰ 如先前所述。|
||[彈性的查詢](sql-database-elastic-query-horizontal-partitioning.md)︰ 在報表的資料庫間的查詢或交叉租用戶分析。|
||[彈性的工作](sql-database-elastic-jobs-overview.md)︰ 封裝並可靠部署至多個資料庫的 [資料庫維護作業或資料庫結構描述變更。|
||[彈性的交易](sql-database-elastic-transactions-overview.md)︰ 原子和隔離的方式處理程序會變更為多個資料庫。 應用程式需要 「 所有或執行任何動作 」 保證透過數種資料庫作業時，所需彈性的交易。 |
||[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)︰ 管理資料散發及對應至資料庫的租用戶。 |

## <a name="shared-models"></a>共用的模型

先前所述，大部分的 SaaS 提供者，以共用的模型的方法可能會造成租用戶隔離問題的問題與應用程式開發與維護的複雜性。 不過，直接為消費者提供服務的 multitenant 應用程式，租用戶隔離需求可能無法為最小化成本為高優先順序。 對方可能無法在密度降低成本的一個或多個資料庫中套件租用戶。 使用單一資料庫或多個 sharded 資料庫的共用資料庫模型可能會造成其他的資源共用與整體成本的效率。 Azure SQL 資料庫提供一些功能，協助客戶建置隔離的改良的安全性與管理在資料層中。

| 應用程式的需求 | SQL 資料庫功能 |
| ------------------------ | ------------------------- |
| 安全性隔離功能 | [列層級安全性](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [資料庫結構描述](https://msdn.microsoft.com/library/dd207005.aspx) |
| 以便於 DevOps 跨資料庫 | [彈性的查詢](sql-database-elastic-query-horizontal-partitioning.md) |
|| [彈性的工作](sql-database-elastic-jobs-overview.md) |
|| [彈性的交易](sql-database-elastic-transactions-overview.md) |
|| [彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md) |
|| [彈性的資料庫分割及合併](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>摘要

租用戶隔離需求重視大多數 SaaS multitenant 應用程式。 若要提供隔離的最佳選擇重度 leans 趨近於每個租用戶的資料庫的方法。 其他兩種方法要求需要技術開發人員提供大幅增加成本與風險的隔離的複雜的應用程式層級的投資。 如果隔離要求都不會計算在服務開發，修整他們可能會更高的前兩個模型中。 每個租用戶的資料庫模型相關聯的主要缺點相關提高的雲端資源成本，因為精簡共用和維護和管理許多資料庫。 進行這些折衷方案時，通常必須努力 SaaS 應用程式開發人員。

雖然折衷方案可能與大部分的雲端資料庫服務提供者的主要障礙，Azure SQL 資料庫不以彈性的集區和彈性的資料庫功能障礙。 SaaS 開發人員可以合併的每個租用戶的資料庫模型隔離特性，並使用彈性的資料庫及相關聯的工具來最佳化資源共用及管理能力改良的許多資料庫。

共用的資料模型中其他資源分享的效率的結果，並減少整體成本，可能會發現 multitenant 的應用程式提供者擁有沒有租用戶隔離需求，以及誰可以套件租用戶密度在資料庫中。 Azure SQL 資料庫彈性的資料庫工具 sharding 文件庫與安全性功能協助 SaaS 提供者建立和管理 multitenant 應用程式。

## <a name="next-steps"></a>後續步驟

[開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md)範例應用程式的示範用戶端文件庫。

建立[彈性的集區自訂儀表板 SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard)彈性的集區用於、 調整資料庫解決方案範例應用程式。

使用 Azure SQL 資料庫工具移轉[擴充中現有的資料庫](sql-database-elastic-convert-to-use-elastic-tools.md)。

如何[建立彈性的資料庫](sql-database-elastic-pool-create-portal.md)檢視我們教學課程。  

瞭解如何[監控和管理彈性的資料庫](sql-database-elastic-pool-manage-portal.md)。

## <a name="additional-resources"></a>其他資源

- [什麼是 Azure 的彈性資料庫？](sql-database-elastic-pool.md)
- [擴展 Azure SQL 資料庫](sql-database-elastic-scale-introduction.md)
- [有彈性的資料庫的工具和列層級安全性 multitenant 應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [使用 Azure Active Directory 和 OpenID 連線 multitenant 應用程式中的驗證](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin 問卷應用程式](../guidance/guidance-multitenant-identity-tailspin.md)
- [解決辦法快速入門](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>問題及功能要求

問題，我們中找到[論壇，在 SQL 資料庫](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)。 新增功能邀請中的[SQL 資料庫意見反應](https://feedback.azure.com/forums/217321-sql-database/)。
