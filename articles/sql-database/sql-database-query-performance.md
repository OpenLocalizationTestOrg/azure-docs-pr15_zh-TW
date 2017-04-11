<properties 
   pageTitle="Azure SQL 資料庫查詢效能充分" 
   description="查詢效能監視 Azure SQL 資料庫的識別大部分的 CPU 使用查詢。" 
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
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL 資料庫查詢效能充分


管理及調整關聯式資料庫的效能是需要嚴重專業知識與時間投資的挑戰工作。 查詢效能充分可讓您節省更多時間資料庫效能疑難排解提供下列步驟︰

- 到消耗您資料庫資源 (DTU) 的深入見解。 
- CPU/工期執行計算，可能可以針對更佳的效能調整上方的查詢。
- 向下切入的查詢，詳細資料可檢視其文字和資源使用量的歷程記錄。 
- 效能調整顯示[SQL Azure 資料庫顧問](sql-database-advisor.md)所執行的動作的註釋  



## <a name="prerequisites"></a>必要條件

- 查詢效能充分僅適用於使用 Azure SQL 資料庫 V12 的。
- 查詢效能充分需要[查詢儲存](https://msdn.microsoft.com/library/dn817826.aspx)為使用您的資料庫。 如果未在執行查詢存放區，入口網站會提示您加以開啟。

 
## <a name="permissions"></a>權限

下列的[角色型存取控制](../active-directory/role-based-access-control-configure.md)」 權限，才能使用查詢效能充分︰ 

- **讀取**、**擁有人**、**參與者**、 **SQL DB 參與者**或**SQL Server 參與者**的權限，才能檢視頂端的資源使用查詢和圖表。 
- **擁有人**、**參與者**、 **SQL DB 參與者**或**SQL Server 參與者**的權限，才能檢視查詢文字。



## <a name="using-query-performance-insight"></a>使用查詢效能充分

查詢效能充分很容易使用︰

- 開啟[Azure 入口網站](https://portal.azure.com/)，尋找您想要檢查的資料庫。 
  - 從左側功能表的] 下支援與疑難排解，請選取 [」 查詢效能充分 」]。
- 在第一個標籤中，檢閱的清單上方的耗費資源的查詢。
- 選取個別的查詢，若要檢視其詳細資料]。
- 開啟[SQL Azure 資料庫顧問](sql-database-advisor.md)，檢查是否可以使用任何建議。
- 使用滑桿或縮放圖示，以變更觀察的間隔。

    ![效能儀表板](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] 所提供的查詢效能獲得深入見解的 SQL 資料庫查詢市集来擷取需要幾個小時的資料。 如果資料庫有沒有活動或查詢存放區不是作用中時一段時間，圖表會是空的時顯示的期間。 不在執行時，您可能會在任何時間啟用查詢存放區。   



## <a name="review-top-cpu-consuming-queries"></a>檢閱上方的 CPU 使用查詢

在[入口網站](http://portal.azure.com)執行下列動作︰

1. 瀏覽到 SQL 資料庫，然後按一下 [**所有設定** > **支援 + 疑難排解** > **查詢效能充分**。 

    ![查詢效能充分][1]

    隨即會開啟 [查詢] 檢視，並列出的主要的 CPU 耗用的查詢。

1. 按一下 [文繞圖，如需詳細資訊。<br>最上方的線條顯示資料庫的整體 DTU %時顯示在選取的間隔耗用的選取查詢的 CPU %的列, (例如，如果已選取 [**週**每個橫條均代表一天)。

    ![主要查詢][2]

    底部格線代表顯示查詢的彙總的資訊。

  - 查詢識別碼 – 內部資料庫查詢的唯一識別碼。
  - CPU 每個查詢顯著間隔內 （彙總函式而定）。
  - 每個查詢的持續時間 （依彙總函式而定）。
  - 總數量執行特定查詢的詳細資訊。

    選取或清除個別的查詢，來包含或排除其從圖表使用核取方塊。

1. 如果您的資料過期，請按一下 [**重新整理**] 按鈕。
1. 您可以使用滑桿和縮放按鈕來變更觀察間隔和調查波峰上︰ ![設定](./media/sql-database-query-performance/zoom.png)
1. （選擇性） 如果您想不同的檢視，您可以選取 [**自訂**] 索引標籤，並設定︰
  
  - 公制 (CPU、 持續時間、 執行計算)
  - 時間間隔 （週、 上個月過去，最後一個 24 小時）。 
  - 查詢數。
  - 彙總函式。

    ![設定](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>檢視個別的查詢詳細資料

若要檢視查詢的詳細資料︰

1. 按一下任何查詢] 清單中的查詢。

    ![詳細資料](./media/sql-database-query-performance/details.png)

1. 詳細資料檢視會開啟，並查詢 CPU 消耗/工期執行計算分解一段時間。
1. 按一下 [文繞圖，如需詳細資訊。
  - 上方的圖表會顯示虛線整體資料庫 DTU %，然後長條是由選取的查詢的 CPU %。
  - 第二個圖表會顯示總工期，以選取的查詢。
  - 底部圖表會顯示總次數所選取的查詢。
    
    ![查詢詳細資料][3]

1. 您也可以使用滑桿、 縮放] 按鈕，或按一下 [**設定**來自訂查詢資料的顯示方式，或挑選不同的時間範圍。

## <a name="review-top-queries-per-duration"></a>檢閱每工期查詢

介紹中的查詢效能充分新的更新，可以協助您識別潛在的障礙的兩個新指標︰ 持續時間] 及 [執行計算。<br>

長時間查詢可能最大的鎖定資源更長的時間、 封鎖其他使用者，以及限制延展性。 這也是最適合進行最佳化。<br>

若要識別長時間執行的查詢︰

1. 開啟所選資料庫查詢效能充分中**自訂**] 索引標籤
1. 將指標變更為**持續時間**
1. 選取查詢與觀察間隔的數的字
1. 選取彙總函式
  - **加總**將整個觀察間隔內的所有查詢執行時間。
  - **最大值**會尋找查詢的執行時間間隔整個觀察是最大值。
  - **平均**尋找平均的執行時間，所有的查詢執行，並顯示您登出這些平均前。 

    ![查詢持續時間][4]

## <a name="review-top-queries-per-execution-count"></a>檢閱每執行計數查詢

高次數可能不會影響資料庫本身和資源使用量很低，但是整體的應用程式可能會收到變得很慢。

在某些情況下，更高的執行計數可能會導致增加的網路往返。 往返大幅會影響效能。 他們的年齡主旨網路延遲和下游伺服器的延遲。 

例如，許多資料導向網站重度存取資料庫中的每個使用者要求。 雖然連線共用可協助，可以增加的網路流量和處理載入到資料庫伺服器上的可能會影響效能。  一般的建議是將往返絕對的最小值。

若要找出經常執行查詢 （「 冗長 」） 的查詢︰

1. 開啟所選資料庫查詢效能充分中**自訂**] 索引標籤
1. 變更為**執行計算**的指標
1. 選取查詢與觀察間隔的數的字

    ![查詢執行計數][5]

## <a name="understanding-performance-tuning-annotations"></a>了解效能調整註釋 

時瀏覽您的查詢效能充分的工作量，您可能會發現垂直線圖表上方含圖示。<br>

下列圖示表示附註。所代表的效能影響[SQL Azure 資料庫顧問](sql-database-advisor.md)所執行的動作。 依暫留註解，您會收到關於巨集指令的基本資訊︰

![查詢註釋][6]

如果您想要更詳細的資訊，或套用顧問建議，請按一下圖示。 隨即會開啟動作的詳細資料。 如果是作用中的建議您可以套用立即使用命令。

![查詢註釋詳細資料][7]

### <a name="multiple-annotations"></a>多個註釋。 ###

有可能的縮放比例，由於相鄰的註釋成一份會取得摺疊。 這會以特殊的圖示表示，按一下會開啟新的刀位置的清單分組的註解就會顯示。
若要進一步瞭解您的工作量，可協助相互關聯查詢及效能調整動作。 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>最佳化查詢效能充分的查詢存放區設定

在您使用的查詢效能充分，您可能會遇到下列查詢市集訊息︰

- 「 查詢市集未正確設定此資料庫。 按一下這裡以進一步。 」
- 「 查詢市集未正確設定此資料庫。 按一下這裡以變更設定。 」 

當查詢市集無法收集新資料，這些訊息通常會顯示。 

第一種情況，這時查詢儲存為唯讀狀態，並以最佳方式設定參數。 您可以藉由增加查詢存放區的大小或清除查詢存放區來修正此問題。

![qds] 按鈕][8]

查詢儲存為關閉，或參數未設定以最佳方式，就會情況第二個情況。 <br>您可以變更的保留與擷取原則，並啟用查詢市集，藉由執行下列命令，或直接從入口網站︰

![qds] 按鈕][9]

### <a name="recommended-retention-and-capture-policy"></a>建議的保留與擷取原則

有兩種類型的保留原則︰

- 依據 – 大小如果設定為 [自動它會讓資料更簡潔自動達到附近大小上限。
- 時間基礎-預設我們會將它設定為 30 天，這表示，如果查詢儲存空間不足，它會刪除查詢資訊 30 天之前

擷取原則可能會設定為︰

- **所有**-擷取所有查詢。
- **自動**– 略過不常用的查詢和不重要的編譯和執行期間的查詢。 在內部取決於執行計算、 編譯及執行階段期間臨界值。 這是預設的選項。
- **無**– 查詢市集停止擷取新的查詢，不過仍收集已擷取查詢的執行階段統計資料。
    
我們建議您所有的原則設為自動] 及 [初始狀態為 30 天的原則︰

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

增加查詢存放區的大小。 連線至資料庫發出下列查詢和執行︰

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

不過，如果您不想要等到您可以清除查詢存放區，套用這些設定會最後會使查詢市集收集新查詢。 
> [AZURE.NOTE] 執行下列查詢會刪除查詢存放區目前所有的資訊。 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>摘要

查詢效能充分可協助您瞭解您的查詢工作負載的影響，以及如何將其關聯到消耗資料庫資源。 使用此功能，您將瞭解如何使用查詢頂端，並輕鬆找出來修正問題之前的項目。




## <a name="next-steps"></a>後續步驟

提升效能 SQL 資料庫的其他建議，請按一下 [**查詢效能充分**刀上的 [[建議](sql-database-advisor.md)]。

![效能警告](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

