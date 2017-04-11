<properties 
   pageTitle="價格層建議 Azure SQL 資料庫" 
   description="變更價格時中 Azure 入口網站，價格層建議層是提供建議您最好層適合用來執行現有 Azure SQL 資料庫的工作量。 定價層說明 SQL 資料庫的服務層和效能層級。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>定價層建議 SQL 資料庫

 定價層建議使用建議的服務層和效能層級的最適合用於執行現有 Azure SQL 資料庫的工作量。

> [AZURE.NOTE] 定價層建議只適用於網頁和商務資料庫和彈性的資料庫集區，並僅適用於[Azure 入口網站](https://portal.azure.com/)。


取得價格層建議期間下列工作︰

- [變更服務層和效能的層級 （價格層） SQL 資料庫](sql-database-scale-up.md)
- [Azure SQL server 升級至 V12](sql-database-upgrade-server-portal.md)
- 瀏覽至您的 V12 伺服器。 請參閱[SQL 資料庫價格層建議](sql-database-service-tier-advisor.md)。
- [建立彈性的資料庫集區](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>概觀

SQL 資料庫服務會以評估為 SQL 資料庫的歷史資源使用狀況分析目前的效能和功能的需求。 此外，最小的可接受服務層取決於資料庫，並啟用的[業務連續性](sql-database-business-continuity.md)功能的大小。 

這項資訊分析的服務層和效能層級的最適合用來執行資料庫的一般工作負載和建議維護目前的功能集。

- 服務會檢查前 15 到 30 天的歷史 （資源使用狀況、 資料庫大小和資料庫活動） 的資料，並使用資源量和實際的限制目前提供的服務層和效能層級之間進行比較。
- 資料分析中 15 的第二個時間間隔，每個間隔結果集分類到現有的服務層級並效能層級的最適合用來處理該結果集的工作量。
- 這些 15 第二個範例會然後彙總為較大的 15 30 天分析，建議您可以以最佳方式處理 95%的歷程記錄工作負載的服務層和效能層級。

### <a name="recommendations"></a>建議

根據您的資料庫的使用方式，目前有 2 類別可遇到的建議︰


| 建議 | 描述 |
| :--- | :--- |
| 升級 | 升級至新的層。 |
| 無法使用 | 資料庫需要最小的工作量或大約 35 天的活動。 沒有足夠的資料來提供有效的建議。 |

## <a name="getting-pricing-tier-recommendations"></a>取得價格層建議

取得價格層建議選取 [現有的網站或商務資料庫，按一下 [**所有設定**]，然後按一下**價格層 （縮放 DTUs）**。 (價格層建議，也會出現時您[升級 Azure SQL server V12](sql-database-upgrade-server-portal.md)。)

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下 [**瀏覽** > **SQL 資料庫**。
4. 在**SQL 資料庫**刀中，按一下您想要查看建議的資料庫︰

    ![選取資料庫][1]

5. 在資料庫刀中，選取 [**所有設定**，然後都選取**價格層 （縮放 DTUs）**]。


7. **建議價格層**開啟，您可以在其中按一下 [建議的層，然後按一下 [**選取**] 按鈕來變更為該層。

    ![註冊預覽][4]

8. 您也可以按一下 [開啟您可以在此檢視資料庫的建議的層、 目前和建議層級之間的歷史資源使用狀況分析圖表的功能比較**價格層建議詳細資料**刀**檢視使用情況的詳細資料**]。

    ![註冊預覽][5]



## <a name="summary"></a>摘要

定價層建議提供自動化的經驗收集遙測資料的每個 SQL 資料庫及建議的最佳服務層效能層級組合為資料庫的實際的效能需求及功能的需求。 設定刀按一下**價格層 （縮放 DTUs）**若要查看價格層建議的任何 Web 和企業版的資料庫。



## <a name="next-steps"></a>後續步驟

根據特定資料庫的詳細資料，通常會執行升級或降級不會發生立即。 入口網站會提供通知到的新層，將資料庫轉換為或您可以監視升級狀態查詢 SQL 資料庫伺服器的主要資料庫中的 [ [sys.dm_operation_status （Azure SQL 資料庫）](https://msdn.microsoft.com/library/dn270022.aspx) ] 檢視。


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
