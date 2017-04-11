<properties
    pageTitle="什麼是 SQL 資料庫 V12 新增 |Microsoft Azure"
    description="說明為何，現在升級版本 V12 助益的雲端中使用 Azure SQL 資料庫 business 系統。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>在 SQL 資料庫 V12 中的新功能


本主題說明版本 V11 V12 新版 Azure SQL 資料庫有許多優點。


我們持續將功能新增至 V12。 因此我們建議您造訪我們服務更新網頁的 Azure，以及如何使用其篩選︰


- 篩選[SQL 資料庫服務](https://azure.microsoft.com/updates/?service=sql-database)。
- 篩選上市[(GA) 的相關公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)SQL 資料庫功能。


在記錄的相關資源限制 SQL 資料庫的最新的資訊︰<br/>[Azure SQL 資料庫資源限制](sql-database-resource-limits.md)。


## <a name="increased-application-compatibility-with-sql-server"></a>提高應用程式與 SQL Server 的相容性


改善 [Microsoft SQL Server 2014 年，與相容性，並維持發行新版本的 SQL Server 相容性都是 SQL 資料庫 V12 的一個主要目標。 在其他區域時，V12 達到程式設計的重要區域中的 SQL server 的不一致性致歉。 例如︰

- [內建 JSON 支援](https://msdn.microsoft.com/library/dn921897.aspx)

- [視窗函數](http://msdn.microsoft.com/library/ms189798.aspx)，[在](http://msdn.microsoft.com/library/ms189461.aspx)

- [XML 索引](http://msdn.microsoft.com/library/bb934097.aspx)和[選擇性的 XML 索引](http://msdn.microsoft.com/library/jj670104.aspx)

- [追蹤修訂](http://msdn.microsoft.com/library/bb933875.aspx)

- [選取 [...]將](http://msdn.microsoft.com/library/ms188029.aspx)

- [全文檢索搜尋](http://msdn.microsoft.com/library/ms142571.aspx)

- [變更資料庫 SCOPED 設定 (TRANSACT-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

請參閱[以下](sql-database-transact-sql-information.md)的小型 SQL 資料庫中尚不支援的功能集。


### <a name="compatibility-level-130"></a>相容性層級 130


> [AZURE.IMPORTANT] *新*資料庫建立 Azure SQL 資料庫 V12**年 6 月 2016年**中開始，有從 130 會比對 Microsoft SQL Server 2016 GA.開始的相容性層級
> 
> 您可以使用`ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120`如果您想要的話。
> 
> 2016 年 6 月之前所建立的資料庫沒有變更這項變更預設的相容程度。 也不是由從 V11 升級 V12 變更資料庫的層級。



說明您可以比較您最重要的查詢之間的最新與先前的相容性層級，請參閱︰

- [改良的查詢效能與 SQL Azure 資料庫中的相容性等級 130](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>更多進階版效能，新的效能層級


在 V12，我們會增加資料庫交易單位 (DTUs) 所有的進階版效能層級免費的 25%。 更高的效能提升可達到等新功能︰


- 支援的記憶體內[columnstore 索引](http://msdn.microsoft.com/library/gg492153.aspx)。
- [資料表分割列](http://msdn.microsoft.com/library/ms187802.aspx)搭配[TABLE](http://msdn.microsoft.com/library/ms177570.aspx)相關的增強功能。
- 動態管理可使用檢視來協助監視並調整的效能[(Dmv)](http://msdn.microsoft.com/library/ms188754.aspx) 。


### <a name="reliable-performance"></a>可靠的效能


如果您的用戶端程式連線到 SQL 資料庫 V12 時您的用戶端執行 Azure 虛擬機器 (VM) 上時，您必須開啟下列連接埠範圍 VM 上︰

- 11000 11999
- 14000 14999


按一下[這裡](sql-database-develop-direct-route-ports-adonet-v12.md)的詳細資料的 SQL 資料庫 V12 的連接埠。 連接埠所需的 SQL 資料庫 V12 中的效能增強功能。


## <a name="better-support-for-cloud-saas-vendors"></a>更佳的雲端支援 SaaS 廠商


只在 V12，我們會發佈新的標準效能層級 S3 及公用的[彈性的資料庫集區](sql-database-elastic-pool.md)預覽。 彈性的資料庫集區是專為雲端 SaaS 廠商的解決方案。  使用彈性的資料庫集區，您可以︰


- 共用資料庫，以減少大量的資料庫的成本之間的 DTUs。
- 執行[彈性的資料庫工作](sql-database-elastic-jobs-overview.md)管理資料庫在小數位數。


## <a name="security-enhancements"></a>安全性增強功能


安全性是任何人都可以執行其企業雲端中的主要考量。 發行 V12 中最新的安全性功能包括︰


- [列層級安全性](http://msdn.microsoft.com/library/dn765131.aspx)(RLS)
- [動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)
- [所包含的資料庫](http://msdn.microsoft.com/library/ff929188.aspx)
- 授與]，拒絕管理[應用程式角色](http://msdn.microsoft.com/library/ms190998.aspx)、 撤銷
- [透明資料加密](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)(TDE)
- [使用 Azure Active Directory 驗證連線到 SQL 資料庫](sql-database-aad-authentication.md)
 - SQL 資料庫現在支援 Azure Active Directory 驗證]，使用身分識別的 Azure Active Directory (Azure AD) 連線到 SQL 資料庫機制。 Azure Active Directory 驗證您可以集中管理的身分識別資料庫使用者及其他 Microsoft 服務在一個中央位置。
- [永遠加密](https://msdn.microsoft.com/library/mt163865.aspx)（以預覽版本） 可讓您加密透明應用程式，並允許用戶端，而不必共用加密金鑰以 SQL 資料庫加密用戶端應用程式內的機密資料。


## <a name="increased-business-continuity-when-recovery-is-needed"></a>增加業務連續性需要修復時


V12 提供改良的復原點目標 (RPOs) 和估計的復原 (ERTs):


| 業務連續性功能 | 較舊版本 | V12 |
| :-- | :-- | :-- |
| 地理還原 | • RPO < 24 小時的時間。<br/>• ERT < 12 小時。 | • RPO < 1 小時。<br/>• ERT < 12 小時。 |
| 作用中的地理複寫 | • RPO < 5 分鐘。<br/>• ERT < 1 小時。 | • RPO < 5 的秒數。<br/>• ERT < 30 秒數。 |


如需詳細資訊，請參閱[SQL 資料庫業務連續性](sql-database-business-continuity.md)。


## <a name="more-reasons-to-upgrade-now"></a>若要立即升級的其他原因


有許多為什麼客戶應該現在從升級至 Azure SQL 資料庫 V12 V11 的絕佳理由︰


- SQL 資料庫 V12 有冗長的限制，超出 V11 的各項功能的功能清單。
- 您無法繼續 V12，以加入新功能，但沒有新的功能將會新增至 V11。
- 在所發行的 Microsoft SQL Server 前 SQL 資料庫 V12 所發行大部分的新功能。


## <a name="are-you-using-v12-already"></a>您是否已使用 V12？


若要查看您是否資料庫或舊版的 SQL 資料庫服務上執行的邏輯伺服器一個簡單的方法是執行下列動作︰


1. 移至[Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [**瀏覽**]。
3. 按一下 [ **SQL Server**]。
4. 您的伺服器或資料庫旁邊的圖示會告訴本文︰
 - ![V12 伺服器圖示](./media/sql-database-v12-whats-new/v12_icon.png) **V12 邏輯伺服器**
 - ![舊版本伺服器圖示](./media/sql-database-v12-whats-new/earlier_icon.png)**舊版本邏輯伺服器**


另一個技巧，以確定版本是執行`SELECT @@version;`陳述式中您的資料庫，並檢視類似結果︰


- **12**.0.2000.10 &nbsp; *（版本 V12）*
- **11**.0.9228.18 &nbsp; *（版本 V11）*


可以只在 V12 邏輯伺服器上裝載 V12 資料庫。 與 V12 伺服器可以主控只有 V12 資料庫。


如果您還不執行 V12 上，您就可以依照本文中[升級至 SQL 資料庫 V12 就地](sql-database-v12-plan-prepare-upgrade.md)升級邏輯伺服器。


## <a name="V12AzureSqlDbPreviewGaTable"></a>一般的顯示狀態區域


- 2015 年 7 月 31 日，以所有區域有已都升級至一般可用性 (GA)。
- 在 2014 年 12 月的年，但只會在預覽的狀態，已發行 V12。

[使用 Microsoft Azure 預覽的補充條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
