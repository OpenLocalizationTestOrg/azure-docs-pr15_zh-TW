<properties
   pageTitle="Azure SQL 資料庫 Azure 案例研究 Snelstart |Microsoft Azure"
   description="深入了解 SnelStart 快速展開速率為每個月 1000 個新 Azure SQL 資料庫 business 服務所使用的 SQL 資料庫"
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

# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>使用 Azure SnelStart 具有快速展開速率為每個月 1000 個新 Azure SQL 資料庫 business 服務

![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart 將荷蘭熱門財務和商務-管理軟體適用於小型及中型大小企業 (SMBs)。 55000 客戶會服務 110 的員工，包括 35 的 IT 人員的人員。 從桌面軟體移動到軟體-為-的-服務 (SaaS) 提供內建在 Azure，SnelStart 所做內建的服務，的最自動化管理 C# 中使用熟悉的環境，以使用彈性的資料庫集區非移轉-或下-佈建企業最佳化效能與延展性。 使用 Azure 提供 SnelStart 的內部部署與雲端之間移動客戶 fluidity。

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##<a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>為什麼 SnelStart 延伸從桌面到雲端服務

> 「 使用 Azure 表示我們提供更快速的軟體、 快速回應客戶的要求，並不按比例縮放的解決方案時要求增加 」。

> 由於已，軟體設計師

SnelStart 執行成功的軟體商務年，使用傳統的開發模型︰ 程式碼、 封裝、 運送，及重複。 一段時間，進行變更的速度更快成長快速。 法規經常變更，客戶需要更容易處理財務記錄和進行共同作業的那些變更其會計和政府版的方式。

「 傳送給客戶的軟體是成本限制，「 根據由於已，軟體架構。 「 產品包裝，與運費有限的頻率，我們無法發行軟體。 我們需要套件更新定期貨，但的變得很難需求客戶的變更。 我們可能不確定我們的客戶升級至最新版本的產品。 因此，我們需要支援多個版本，讓支援工作很難發揮。 」

已加入，「 我們想要在加速程式和發行更新的方式適中，因此我們可以創新速度更快，建立新的服務，我們的客戶。 我們也想要的方式將更多的程序自動化以簡化客戶的商務系統需求。 」

SnelStart，方案為延伸其服務成為雲端 SaaS 提供者。 Azure SQL 資料庫平台協助 SnelStart 而不會需要基礎結構為-的-服務 (IaaS) 解決方案的主要 IT 負荷取得。

雲端模型也可讓 SnelStart 來修正錯誤，並不需要下載並升級軟體的客戶快速，提供的新功能。 根據已，「 使用 Azure 雲端服務可讓我們可以快速根據需求從協力廠商的變更。 而不是由數以千計的客戶提供新版本，我們可以採用從桌面應用程式所需的協力廠商的新格式傳送的資訊。 」

##<a name="a-modern-company-with-traditional-roots"></a>新式公司與傳統開根號

SnelStart 是現代、 敏捷式高技術企業版與卑微開根號約會經驗談至 1964，founders 何時開始公司為樂器組件的製造商。 SnelStart 軟體企業的核心真的開始打 1980，個人電腦的期間。 公司需要與會計專用軟體時，較好的選擇，使花費到它自己的手事務。 公司︰ 1982，建立什麼最後會變得 SnelStart 會計專用軟體的基礎。 從 [開始] 軟體 admired 的簡單和速度，而已，讓公司最後變更焦點，並將軟體公司經歷本身。

在 1995，SnelStart 發行 Windows 版的第一個記錄應用程式。 應用程式，內建在 Microsoft Visual Basic 1.0 和 Microsoft Access 資料庫，幫助放大客戶基底 5000 個以上的使用者。

現在，SnelStart 是主要的搜尋提供者線條商務 (LOB) 和荷蘭 SMBs 和自我採用的企業適用對象的企業管理應用程式。 為地卡羅 Kuip，IT 架構，指出: 「 我們的目標是提供我們的客戶的商務管理服務的 100%自動化。 」

##<a name="optimizing-performance-and-cost-with-elastic-pools"></a>最佳化效能與成本與彈性的資料庫

SnelStart 已大型的採納彈性的資料庫集區。 彈性的集區協助公司限制成本及更有效率地管理效能需求。 根據已 」 藉由使用彈性的資料庫的資料庫，我們可以最佳化效能根據我們的客戶的需求，但不過度佈建。 如果有佈建以最大使用量載入時，就會很高。 不過，多個間共用資源的選項低用量資料庫可讓我們建立一個方案，以及執行，並成本有效。 」

##<a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Azure SQL 資料庫協助 containerize 隔離及安全性的資料 

Azure SQL 資料庫可讓您輕鬆地並無障礙地移動客戶的內部部署企業管理 Azure 資料 SnelStart。 Azure SQL 資料庫是方便的容器，提供隔離、 邊界驗證、 授權及簡單的備份與還原功能。 資料庫 business 管理提供適合的概念性模型。 根據地卡羅 Kuip，IT 架構 」 此容器邊界中的項目包含是企業版，很重要的機密資料，並將這些項目儲存在隔離的資料庫將這些保護。 我們可以管理在資料庫層級的授權，甚至自動化而不需要資料庫管理員 (Dba) 在教職員的 [管理和向外的資料庫。 」

Azure SQL Data Warehouse 也會依協助收集遙測資料，例如資料入侵偵測與連線使用者活動記錄，公司播放角色 SnelStart 安全性與管理本文中。

##<a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure 負荷移除，好讓開發人員可以都要花更長的時間進行值 

Azure 平台模型移除開銷基礎結構，並啟用 SnelStart 自動化部署使用 C# 管理文件庫。 為 Kuip 狀態時，「 我們可以放大的很小教職員我們目前作業時同時增加我們的用戶端的延展性、 速度和損毀復原選項。 釋放將焦點放在新的服務和功能，而非只更新現有的程式碼，將設定與新法規或稅碼資源的服務開發 shift。 」 他加入 「 來自動化管理和使用提供，我們將可我們的用戶端提供更多的值，而不必大投資中操作人員 SaaS 」。 例如，藉由使用 Azure 和彈性的資料庫的資料庫，SnelStart 已新增不同的新功能，包括與銀行更強大的客戶資料整合新增計費服務、 小型企業背景檢查和電子郵件服務。

> 「 中只的前幾個月 2016年，我們會展開從關於 5500 我們 Azure SQL 資料庫部署至多個 12000 和目前所新增的每個月的關於 1000 個資料庫 」。

> 由於已，軟體設計師

資料庫管理進一步被自動使用彈性工作功能。 為 Kuip 狀態時，「 高度感謝 SQL 資料庫的 [伺服器] 執行個體上的資料庫的自動探索。 」 這個選項可讓 SnelStart 跨沒有其他開銷其動態成長客戶執行管理作業。

SnelStart 也開發 API，做為代理人之間客戶資料和第三方軟體協力廠商所建立的應用程式。 Kuip 狀態，請為 「 這個 API 會啟用將功能新增至我們的軟體，例如消除發票及其他文件的資料輸入其他廠商。 」 客戶將不再需要手動輸入他們小型企業的會計專用軟體; 發票SnelStart 軟體會直接交換所需的資訊。 這個選項可讓客戶加入的資訊新的產業數位轉換生態其企業管理工作。  

##<a name="how-azure-services-enable-saas-for-snelstart"></a>Azure 服務會啟用 SaaS SnelStart 的方式

藉由使用 Azure，其客戶和其會計更流暢地在雲端，可以做 SnelStart。 例如，客戶和會計可以直接存取 SnelStart 的用戶端 API，裝載於 Azure 共用資訊。 Kuip 狀態，「 這些可重複使用的服務可提供給我們面對客戶的 web 應用程式中，且會提供常見的基礎結構與函數，以允許與存取權的客戶的商務管理客戶所用的第三方軟體。 範例包括提供產品設定功能、 管理防火牆規則，以及管理長時間執行程序，例如備份。 」

> 我們的目標是提供給我們的客戶的商務管理服務的 100%自動化。 」 

> — 地卡羅 Kuip，IT 架構

此外，SnelStart web 服務所允許的客戶及會計輕鬆存取 Azure SQL 資料庫彈性集區中的資料。 此 SaaS 模型，搭配資料庫 elasticity 和 Azure 資源管理員，提供 SnelStart 的互補每個 Azure 部署延展性功能。 實作已完全自動化使用 C# 管理文件庫。

![SnelStart 架構](./media/sql-database-implementation-snelstart/figure1.png)

圖 1。 迄年 6 月 2016 SnelStart 具有多個 11000 資料庫及超過 50 個彈性的資料庫集區
 
##<a name="simplicity-from-the-cloud"></a>從雲端簡單

移至 Azure 雲端方案，因為 SnelStart 已可支援快速客戶等比級數時提供創新的功能及服務。 根據已 」 與 Azure，我們可以傳送附近持續更新我們的客戶，沒有展開我們作業的人員。 我們取得所有其他好 Azure 功能，例如延展性與損毀復原，一起包含在。 」

> 「 時，我們實際移到雷蒙市中...我收到通話回荷蘭，開發人員打電話我有關特定問題。 我們可以取得 Microsoft 生產環境中進行變更，以解決我們問題 48 小時內。 」

> 由於已，軟體設計師

SnelStart 也十分贊同他們已與 Microsoft Azure SQL 資料庫小組所開發的強式合作關係。 為 Kuip 狀態，請 」 功能上有討論和如何使用技術，這不是感謝雙面 」。
SnelStart 立即目標是保留成長其滿足的客戶基底。 當已狀態，「 我們為 ISV 技術和資源限制，而有沒有限制多久我們可以放大。 」


## <a name="more-information"></a>詳細資訊

- 若要進一步瞭解 Azure 彈性資料庫集區，請參閱[彈性的資料庫集區](sql-database-elastic-pool.md)。

- 若要深入瞭解 Web 角色和工作者角色，請參閱[工作者角色](../fundamentals-introduction-to-azure.md#compute)。 

- 若要進一步瞭解 Azure SQL Data Warehouse，請參閱[SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- 若要進一步瞭解 SnelStart，請參閱[SnelStart](http://www.snelstart.nl)。


