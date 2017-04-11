<properties
    pageTitle="監控和管理 Azure 入口網站的彈性的資料庫集區 |Microsoft Azure"
    description="瞭解如何使用 Azure 入口網站和 SQL 資料庫的內建智慧管理、 監控及向右大小調整彈性資料庫集區最佳化資料庫效能及管理成本。"
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>監控和管理 Azure 入口網站的彈性的資料庫集區

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T SQL](sql-database-elastic-pool-manage-tsql.md)


您可以使用 [Azure 入口網站監控和管理的彈性的資料庫集區和集區中的資料庫]。 從入口網站，您可以監視的彈性的資料庫，該資料庫中的資料庫]。 您也可以對您彈性的資料庫中的一組的變更，並同時送出所有的變更。 這些變更包括新增或移除資料庫、 變更您的彈性的資料庫設定或變更您的資料庫設定。

下圖顯示的範例彈性集區。 檢視包含︰

*  監視彈性的資料庫與集區中的資料庫的資源使用狀況的圖表。
*  若要變更的彈性的集區中 [**設定**資料庫] 按鈕。
*  建立新的資料庫，並將它新增到目前的彈性資料庫**建立資料庫**] 按鈕。
*  彈性的工作可協助您管理大量的資料庫執行清單中的所有資料庫交易 SQL 指令碼。

![資料庫檢視][2]

若要暫時進行本文中的步驟，您需要 SQL server Azure 中至少有一個資料庫與彈性的資料庫。 如果您不會有彈性的資料庫，請參閱[建立資料庫](sql-database-elastic-pool-create-portal.md)。如果您沒有資料庫，請參閱[SQL 資料庫教學課程](sql-database-get-started.md)。

## <a name="elastic-pool-monitoring"></a>監控彈性的資料庫

您可以移至特定的資料庫，以查看其資源使用狀況。 根據預設，資料庫會設為顯示最後一個小時的儲存空間及 eDTU 的使用方式。 圖表可以透過各種時間視窗顯示不同的指標設定。

1. 選取要使用的資料庫。
2. 在下**彈性資料庫監控**圖表標示為 [**資源使用狀況**。 按一下 [圖表]。

    ![監控彈性的資料庫][3]

    **公制**刀隨即會開啟，顯示 [指定的時間] 視窗的指定單位的詳細的綜覽。   

    ![公制刀][9]

### <a name="to-customize-the-chart-display"></a>若要自訂圖表顯示

您可以編輯圖表與公制顯示 CPU 百分比、 資料 IO 百分比，以及使用記錄 IO 百分比等其他指標。

2. 在 [公制刀中，按一下 [**編輯**]。

    ![按一下 [編輯]][6]

- 在**編輯圖表**刀中，選取新的時間範圍 （小時、 today、 過去或過去的週），或按一下**自訂**選取最後一個兩週內的任何日期範圍。 選取的圖表類型 （列或行），然後選取要監視的資源。

> 附註︰ 只使用相同的度量單位的度量可以顯示在圖表中相同的時間。 例如，如果您選取 「 eDTU 百分比 」 然後您只能若要選取其他標準百分比做為度量單位。

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 然後按一下**[確定]**。


## <a name="elastic-database-monitoring"></a>監控彈性的資料庫

潛在問題也可以監視個別的資料庫。

1. 在**彈性資料庫監控**有圖表，顯示五個資料庫的指標。 根據預設，圖表會顯示上方的 5 個資料庫集區中的平均 eDTU 使用過去小時內。 按一下 [圖表]。

    ![監控彈性的資料庫][4]

2. **資料庫資源使用量**刀隨即出現。 這會提供集區中的資料庫使用狀況的詳細的綜覽。 使用在刀的下半部的格線，您可以在圖表 （最多 5 資料庫） 中顯示其使用集區中選取任何資料庫。 您也可以自訂顯示在圖表中，按一下 [**編輯圖表**的指標及時間] 視窗。

    ![資料庫資源使用量刀][8]

### <a name="to-customize-the-view"></a>若要自訂檢視

1. 在**資料庫資源使用量**刀中，按一下 [**編輯圖表**。

    ![按一下 [編輯圖表](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. 在**編輯**圖表防禦，以選取新的時間範圍 （過去的小時或過去 24 小時），或按一下**自訂**顯示過去 2 週內選取不同的日期。

    ![按一下 [自訂]](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. 按一下以選取不同的度量單位，比較資料庫時使用的**比較資料庫的**下拉式清單。

    ![編輯圖表](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>若要選取要監視的資料庫

在 [**資料庫資源使用量**刀在 [資料庫] 清單中，您可以找到特定的資料庫，或輸入資料庫的名稱來尋找清單中的頁面。 您可以使用核取方塊，選取資料庫。

![監控資料庫的搜尋][7]


## <a name="add-an-alert-to-a-pool-resource"></a>新增資料庫資源的提醒

您可以新增規則到電子郵件傳送給個人或提醒字串 URL 端點，當您設定使用閾值資源的資源。

> [AZURE.IMPORTANT]監控彈性的集區的資源使用量有至少 20 分鐘的延遲。 目前不支援的彈性的集區設定通知的 30 分鐘。 任何通知彈性的集區設定以英文句點 (稱為參數 」-WindowSize 」 中 PowerShell API) 30 分鐘的可能不會觸發。 請確定您定義彈性的集區的任何通知使用 30 分鐘或更一段 (WindowSize)。

**若要新增提醒給任何資源︰**

1. 按一下 [開啟**公制**刀，按一下 [**新增提醒**中，**資源使用量**圖表，然後填寫 [**新增提醒的規則**的刀 （**資源**會自動設定為您正在使用的資料庫） 中的資訊。
2. 輸入的**名稱**和**描述**可識別通知寄件者和收件者。
3. 選擇您想要從清單提醒**公制**。

    動態的圖表會顯示資源使用狀況，以協助您選擇臨界值的度量。

4. 選擇**條件**（大於、 小於、 等等） 及**閥值**。
5. 按一下**[確定]**。



## <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移到彈性的資料庫

您可以新增或移除現有的資料庫中的資料庫。 資料庫可以在其他集區。 不過，您可以只需新增相同的邏輯伺服器上的資料庫。

1. 在集區刀，**彈性的資料庫**下按一下 [**設定資料庫**。

    ![按一下 [設定資料庫][1]

2. 在 [**設定資料庫**刀中，按一下 [**新增至資料庫。**

    ![按一下 [新增至資料庫](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. 在**新增資料庫**刀中，選取要新增至資料庫的資料庫。 然後按一下 [**選取**]。

    ![選取要新增的資料庫](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    **設定資料庫**刀現在會列出您選擇要加入，其狀態設定為 [**擱置**的資料庫。

    ![擱置的資料庫新增](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. 在 [**設定資料庫刀**中，按一下 [**儲存**]。

    ![按一下 [儲存]](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>將資料庫移出彈性的資料庫

1. 在 [**設定資料庫**刀中，選取 [若要移除的資料庫]。

    ![列出的資料庫](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. 按一下 [**從資料庫移除**]。

    ![列出的資料庫](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    **設定資料庫**刀現在會列出您要移除其狀態設定為 [**擱置中**選取的資料庫。

    ![預覽資料庫新增和移除](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. 在 [**設定資料庫刀**中，按一下 [**儲存**]。

    ![按一下 [儲存]](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>變更集區的效能設定

當您監視集區的資源使用狀況，您可能會發現一些調整所需。 也許集區需要的效能或儲存空間限制的變更。 可能是您要變更資料庫中的資料庫設定。 您可以在任何時間，以取得最佳餘額的效能與成本變更集區的設定。 請參閱[何時彈性的資料庫集區使用？](sql-database-elastic-pool-guidance.md)如需詳細資訊。

**若要變更的 eDTUs 或儲存空間限制，每個集區，並 eDTUs 每個資料庫︰**

1. 開啟 [**設定資料庫**刀。

    **彈性的資料庫集區設定**] 下使用任一滑桿以變更資料庫設定。

    ![彈性的資料庫資源使用量](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 設定變更，顯示顯示估計的每月成本的變更。

    ![更新資料庫和新的每月成本](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>建立及管理彈性的工作

彈性的工作可讓您執行的資料庫在資料庫中的任何數字 TRANSACT-SQL 指令碼。 您可以建立新的工作，或管理現有的工作，使用入口網站。

![建立及管理彈性的工作][5]


您使用的工作之前，請安裝彈性工作元件，並提供您的認證。 如需詳細資訊，請參閱[彈性的資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

請參閱[Azure SQL 資料庫時的縮放比例](sql-database-elastic-scale-introduction.md)︰ 若要向外，移動資料，請使用彈性的資料庫工具查詢，或建立交易。



## <a name="additional-resources"></a>其他資源

- [SQL 資料庫彈性的資料庫](sql-database-elastic-pool.md)
- [建立彈性的資料庫集區入口網站](sql-database-elastic-pool-create-csharp.md)
- [使用 PowerShell 建立彈性的資料庫集區](sql-database-elastic-pool-create-powershell.md)
- [C# 建立彈性的資料庫集區](sql-database-elastic-pool-create-csharp.md)
- [彈性的資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
