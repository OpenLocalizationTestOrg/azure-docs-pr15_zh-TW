<properties
    pageTitle="如何監視儲存帳戶 |Microsoft Azure"
    description="瞭解如何使用 Azure 入口網站中監控 Azure 中的儲存空間帳戶。"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>監控 Azure 入口網站中的儲存空間帳戶

## <a name="overview"></a>概觀

您可以監視您儲存的帳戶從[Azure 入口網站](https://portal.azure.com)。 當您設定的監控透過網站您儲存的帳戶時，Azure 儲存體會使用[儲存空間分析](http://msdn.microsoft.com/library/azure/hh343270.aspx)來追蹤您的帳戶的指標及記錄要求資料。

> [AZURE.NOTE]其他成本會檢查監視[Azure 入口網站](https://portal.azure.com)中的資料與相關聯。 如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">儲存分析及帳單</a>。 <br />

> Azure 檔案儲存空間目前支援儲存分析指標，但還不支援記錄。 您可以啟用透過[Azure 入口網站](https://portal.azure.com)的 Azure 檔案儲存空間的指標。

> 儲存與複寫類型區域多餘儲存空間 (ZRS) 的帳戶沒有 [指標] 或 [在此時間啟用記錄功能。 

> 使用識別、 診斷，及疑難排解 Azure 儲存體相關問題的儲存空間分析及其他工具深入指南，請參閱[監視器，診斷，及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>如何︰ 設定監視儲存帳戶

1. 在[Azure 入口網站](https://portal.azure.com)中，按一下 [**儲存**]，然後按一下以開啟儀表板儲存體帳戶名稱。

2. 按一下 [**設定**]，然後向下 blob、 表格和佇列中服務的**監控**設定捲動。

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. 在 [**監視**設定的層級的監控和每個服務的資料保留原則︰

    -  若要設定的監控層級，請選取下列其中一項︰

      **最小**-收集指標，例如輸入/出口、 可用性、 延遲和成功百分比，blob、 表格和佇列中服務的彙總。

      **詳細資訊**的最小的度量，除了收集指標的每個儲存作業 Azure 儲存服務 API 的同一組。 詳細資訊的指標啟用更深入的分析的應用程式作業期間發生的問題。

      **關閉**-關閉監控。 現有監視資料保留期限的保存。

- 若要設定資料保留原則，**保留天數**中，輸入要保留介於 1 到 365 天的資料的天數。 如果您不想保留原則的設定，請輸入零。 如果沒有保留原則，這是您要刪除的監控的資料。 我們建議您設定保留原則，根據您想要保留您的帳戶的儲存空間分析資料，因此免費系統可以刪除舊及未使用狀況分析資料的時間長度。

4. 當您完成監視設定時，請按一下 [**儲存**]。

您應該能如常看到監視儀表板和需一個小時後的**監視器**頁面上的資料。

您設定監控儲存帳戶，直到不監控會收集資料，且儀表板和**監視器**頁面上的指標圖表是空的。

監控層級和保留原則設定之後，您可以選擇其中一個可用的指標監控在[Azure 入口網站](https://portal.azure.com)，並繪製指標圖表上的指標。 一組預設的度量資訊會顯示每個監控層級。 您可以使用 [**新增指標**來新增或移除 [指標] 清單中的指標。

指標會儲存在名為 $MetricsTransactionsBlob、 $MetricsTransactionsTable、 $MetricsTransactionsQueue 及 $MetricsCapacityBlob 的四個資料表中的儲存空間帳戶。 如需詳細資訊，請參閱[關於儲存分析指標](http://msdn.microsoft.com/library/azure/hh343258.aspx)。


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>如何︰ 自訂監控儀表板

在儀表板，您可以選擇最多六個度量來繪製九個可用的指標的標準圖表。 每個服務 （blob、 表格和佇列中），可用性、 成功百分比和總要求指標可供使用。 使用儀表板上的度量資訊也適用於監控的最小或詳細資訊。

1. 在[Azure 入口網站](https://portal.azure.com)中，按一下 [**儲存**]，然後按一下以開啟儀表板儲存體帳戶名稱。

2. 若要變更繪製圖表的度量資訊，請進行下列動作之一︰

    - 若要新增至圖表的新的度量，請按一下圖表下表所列的公制標頭] 旁的 [彩色] 核取方塊。

    - 若要隱藏在圖表的繪製方式的度量，請清除公制的標頭] 旁的 [彩色] 核取方塊。

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. 根據預設，圖表會顯示趨勢，顯示每個公制 （**相對**頂端的 [圖表選項） 的目前值。 若要顯示 Y 軸，讓您瞭解絕對值，選取 [**絕對**]。

4. 若要變更的時間範圍指標圖表會顯示，會選取 24 小時的時間或 7 天 6 小時頂端的圖表。


## <a name="how-to-customize-the-monitor-page"></a>如何︰ 自訂 [監視器] 頁面

在 [**監視器**] 頁面中，您可以檢視完整的指標儲存帳戶。

- 如果您儲存的帳戶有最小監控設定，例如輸入/出口、 可用性、 延遲和成功百分比的指標會彙總從 blob、 表格和佇列中的服務。

- 如果您儲存的帳戶有設定的詳細資訊監控，指標，可在個別的儲存空間作業，除了服務層級彙總更妥善地解析度。

您可以使用下列程序來選擇哪些儲存指標來檢視中的指標圖表及表格會顯示在 [**監視器**] 頁面上的。 收集、 彙總，與儲存空間的監控儲存帳戶中的資料不會影響這些設定。

## <a name="how-to-add-metrics-to-the-metrics-table"></a>如何︰ 指標表格加上指標


1. 在[Azure 入口網站](https://portal.azure.com)中，按一下 [**儲存**]，然後按一下以開啟儀表板儲存體帳戶名稱。

2. 按一下 [**監視器**]。

    **監視器**頁面隨即開啟。 根據預設，指標表格會顯示可供監控指標的子集。 下圖顯示儲存帳戶的 [預設監視器] 顯示所有三個服務所設定的詳細資訊監視。 使用**新增指標**來選取您想要從所有可用的指標監控的指標。

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] 當您選取的度量，請考慮成本。 有交易和出口成本與重新整理監控顯示相關聯。 如需詳細資訊，請參閱[儲存分析及帳單](http://msdn.microsoft.com/library/azure/hh360997.aspx)。

3. 按一下 [**新增指標**。

    彙總計量所提供的最小監控位於清單頂端。 如果選取此核取方塊，公制會顯示在 [標準] 清單中。

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. 將游標停留在 [右側的 [顯示捲動軸消失，您可以拖曳到檢視中捲動其他指標] 對話方塊。

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. 按一下向下箭號的度量單位，若要展開公制範圍限定為包含的作業的清單。 選取您想要檢視在[Azure 入口網站](https://portal.azure.com)的標準資料表中每一項作業。

    在下圖中，展開授權錯誤百分比公制。

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. 選取所有服務的指標後，按一下 [確定] （核取記號） 更新監視設定。 選取的指標會新增至 [指標] 資料表。

7. 若要從資料表刪除度量單位，請按一下加以選取，公制，，然後按一下 [**刪除公制**。

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>如何︰ 自訂 [監視器] 頁面上的指標圖表

1. 在儲存帳戶的 [指標] 資料表中的 [**監視器**] 頁面上選取 [若要繪製指標圖表上最多 6 指標]。 若要選取度量單位，請按一下它左邊的核取方塊。 若要移除圖表中的度量單位，請清除核取方塊。

2. 若要切換相對值 （只顯示最後一個值） 和絕對值值 （Y 軸顯示） 之間的圖表，請選取 [**相對**或**絕對**頂端的 [圖表]。

3.  若要變更的時間範圍指標圖表會顯示，選取**6 小時**、 **24 小時的時間**或**7 天**頂端的圖表。



## <a name="how-to-configure-logging"></a>如何︰ 設定記錄

針對每個儲存可用的服務使用您儲存的帳戶 （blob、 表格和佇列中），您可以為已讀取要求、 撰寫的要求，及/或刪除要求]，儲存診斷記錄，並可以為每個服務設定資料保留原則。

1. 在[Azure 入口網站](https://portal.azure.com)中，按一下 [**儲存**]，然後按一下以開啟儀表板儲存體帳戶名稱。

2. 按一下 [**設定**]，然後使用在鍵盤上的向下箭號，向下**記錄**捲動。

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. 每個服務 （blob、 表格和佇列中），設定下列項目︰

    - 要求登入的類型︰ 閱讀邀請、 撰寫要求及刪除要求。

    - 若要保留記錄的資料的日數。 輸入零是如果您不想設定保留原則。 如果您沒有設定保留原則，這是您要刪除的記錄。

4. 按一下 [**儲存**]。

診斷記錄會儲存在名為在您儲存帳戶 $logs blob 容器。 存取 $logs 容器的相關資訊，請參閱[關於儲存分析記錄](http://msdn.microsoft.com/library/azure/hh343262.aspx)。
