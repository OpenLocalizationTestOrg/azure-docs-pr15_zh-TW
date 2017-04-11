<properties
    pageTitle="Microsoft Azure 中的指標概觀 |Microsoft Azure"
    description="瞭解如何自訂監控 Azure 中的圖表。"
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Microsoft Azure 中的指標的概觀

所有的 Azure 服務追蹤主要的度量，讓您可以監視狀況、 效能、 可用性和您的服務的使用方式。 您可以在 Azure 的入口網站中檢視這些計量，您也可以使用[REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) ，以程式設計方式存取完整的指標。

針對某些服務，您可能需要開啟診斷才能查看任何指標。 其他人使用，例如虛擬機器中，您會收到一組基本的度量，，但需要啟用完整設定頻率高的度量。 請參閱[啟用監控和診斷](insights-how-to-use-diagnostics.md)若要深入瞭解。

## <a name="using-monitoring-charts"></a>使用監控圖表

您可以圖表指標的任何地方，在您選擇 [任何時間週期。

1. 在[Azure 入口網站](https://portal.azure.com/)中，按一下 [**瀏覽]**，然後您感興趣監控資源。

2. **監控**區段包含每個 Azure 資源最重要的指標。 在 web 應用程式，例如具有**要求與錯誤**，請為虛擬機器會有**CPU 百分比**和**磁碟讀取和寫入**︰ ![監控透鏡](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. 按一下任何圖表，會顯示**公制**刀。 在刀，該圖表，除了是顯示您的 （例如平均值、 最小值和最大值，在您選擇的時間範圍） 指標彙總的資料表。 下面是資源的通知規則。
    ![公制刀](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. 若要自訂的線條，按一下 [**編輯**] 按鈕，在圖表上，或**編輯圖表**上的命令公制刀]。

5. 在 [編輯查詢刀，您可以執行三個項目︰
    - 變更時間範圍
    - 切換之間的外觀橫條圖和折線圖
    - 選擇不同的 metics![編輯查詢](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. 變更時間範圍很簡單，只要選取不同的範圍 （例如**過去小時**），然後按一下 [刀底部的 [**儲存**的。 您也可以選擇 [**自訂**] 可讓您選擇過去 2 週的任何一段時間。 例如，您可以看到整個兩週，或，只要 1 小時昨天。 若要輸入在不同的小時的 [文字] 方塊中輸入。
    ![自訂的時間範圍](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. 下方的時間範圍，您通道選擇在圖表上顯示的指標的任何數字。

8. 當您按一下 [儲存您的變更會儲存為特定資源。 例如，如果您有兩個虛擬機器，變更其中一個圖表，並不會影響其他。

## <a name="creating-side-by-side-charts"></a>建立並排顯示圖表

與入口網站中功能強大的自訂，您可以新增任意數目所要的圖表。

1. 在**…**功能表頂端的刀按一下**新增方塊**︰  
    ![新增功能表](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. 然後，您可以選取 [選取圖表從畫面右側**庫**︰ ![圖庫](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. 如果您沒有看到您想要的度量，您可以隨時新增預設的度量，及**編輯**的其中一個圖表，顯示您所需要的度量。

## <a name="monitoring-usage-quotas"></a>監控使用量配額

大部分的指標顯示您趨勢一段時間，但某些資料，例如使用量配額具有臨界值的時間點資訊。

您也可以查看使用量配額之資源的配額刀上︰

![使用方式](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

像使用指標，您可以使用[REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)以程式設計方式存取完整的使用量配額。

## <a name="next-steps"></a>後續步驟

* [收到警示通知](insights-receive-alert-notifications.md)時公制交叉臨界值。
* [啟用監控和診斷](insights-how-to-use-diagnostics.md)以您的服務收集詳細的頻率高的度量。
* [自動調整執行個體計數](insights-how-to-scale.md)，以確定您的服務可用和回應。
* [監視應用程式效能](../application-insights/app-insights-azure-web-apps.md)如果您想要了解完全如何程式碼執行雲端中。
* 使用[應用程式深入資訊 JavaScript 應用程式和網頁](../application-insights/app-insights-web-track-usage.md)取得造訪的網頁瀏覽器的相關的用戶端分析。
* [監視器可用性和回應的任何網頁](../application-insights/app-insights-monitor-web-app-availability.md)與應用程式深入資訊，您可以瞭解如果您的頁面，讓已關閉。
