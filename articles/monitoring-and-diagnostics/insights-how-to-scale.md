<properties
    pageTitle="手動或自動調整執行個體計數 |Microsoft Azure"
    description="瞭解如何調整 Azure 服務。"
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

# <a name="scale-instance-count-manually-or-automatically"></a>手動或自動調整執行個體計數

在[Azure 入口網站](https://portal.azure.com/)中，您可以手動設定您的服務執行個體數目，或您可以設定讓它的參數會自動根據指定的小數位數。 這通常稱為*延展*或**。

縮放比例根據執行個體計數之前，您應該考慮的縮放比例會影響**價格層**除了執行個體計數。 不同價格層可以有不同的數字核心和記憶體，而且，因此不會有較佳的效能，針對相同的執行個體數目 （這是*設定刻度*] 或 [*小數位數，向下的*）。 本文特別涵蓋*的時幅*] 和 [*出*。

您可以調整在入口網站中，您也可以使用[REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) ，若要手動] 或 [自動調整比例。

> [AZURE.NOTE] 本文將說明如何在[http://portal.azure.com](http://portal.azure.com)入口網站中建立自動縮放設定。 在此入口網站中建立的自動縮放設定無法編輯其 [傳統] 入口網站 ([http://manage.windowsazure.com](http://manage.windowsazure.com))。

## <a name="scaling-manually"></a>手動調整

1. 在[Azure 入口網站](https://portal.azure.com/)中，按一下 [**瀏覽]**，然後瀏覽至您想要不按比例縮放，例如**應用程式服務方案**的資源。

2. [**小數位數**] 方塊中**作業**會告訴您的縮放比例狀態︰**關閉**時您會調整以手動方式**在**會調整的一或多個效能度量。
    ![縮放比例] 方塊](./media/insights-how-to-scale/Insights_UsageLens.png)

3. 按一下 [圖磚上將您移至**比例**刀。 按一下頂端的 [縮放比例刀可以看到自動調整大小動作的歷程記錄的服務。  
    ![縮放比例刀](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] 在此圖表會顯示由自動調整大小所執行的動作。 如果您以手動方式調整執行個體計數，變更會不會反映在此圖表中。

4. 您可以手動調整數字的**執行個體**] 滑桿。
5. 按一下 [**儲存**] 命令，您可以調整該數字的執行個體幾乎可以立即。

## <a name="scaling-based-on-a-pre-set-metric"></a>縮放比例根據預先設定的度量單位

如果您想要自動調整的執行個體數目根據度量單位，請選取您想要的公制**依比例縮放**下拉式清單中。 例如，**應用程式服務方案**，您可以調整的**CPU 百分比**。

1. 當您選取 [度量單位，您會看到滑桿，及/或，輸入您想要不按比例縮放之間的執行個體數目的 [文字] 方塊︰

    ![縮放比例刀 CPU 百分比](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    自動調整大小會不會採用您的服務設定，無論您載入界限上方或下方。

2. 第二，您可以選擇公制的目標範圍。 例如，如果您選擇**[CPU 百分比**，您可以設定目標平均 cpu 過所有執行個體中您的服務。 平均 CPU 超過您所定義的最大值時，會發生出小數位數] 中的比例同樣的會發生時平均值 CPU 降到最低。

3. 按一下 [**儲存**] 命令。 自動調整大小會檢查以確定您在您公制是在執行個體範圍和目標每隔幾分鐘。 當您的服務收到其他流量時，您會收到多個執行個體，而不執行任何動作。

## <a name="scale-based-on-other-metrics"></a>根據其他標準比例

您可以調整不會出現在 [**縮放**] 下拉式清單中，，而且可以更有一組複雜的延展並規則中不按比例縮放的預設值是根據的指標。

### <a name="adding-or-changing-a-rule"></a>新增或變更規則

1. 在 [**小數位數**] 下拉式清單中選擇**排程和效能的規則**︰![效能規則](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. 如果您之前有自動調整大小，在您會看到您的確切規則的檢視。

3. 若要縮放為基礎另一個公制按一下 [**新增規則**] 列。 您也可以按一下現有的資料列，若要從您之前有您想要縮放的公制公制變更。
![新增規則](./media/insights-how-to-scale/Insights_AddRule.png)

4. 現在，您需要選擇哪個公制您想要縮放。 在選擇度量單位，有幾個考量的事項︰
    * *資源*公制是來自。 一般而言，這將會按比例縮放的資源相同。 不過，如果您想要不按比例縮放的儲存空間佇列深度，資源是您要縮放的佇列。
    * *單位名稱*本身。
    * 公制*時間彙總*。 這是如何資料合併*工期*。

5. 選擇您公制後選擇 [公制和運算子的臨界值]。 例如，您可能會說出**大於** **80%**。

6. 然後選擇您想要採取的動作。 有幾個其他類型的動作︰
    * 增加或減少-這會新增或移除您所定義的執行個體中**值**的數目
    * 增加或減少 percent-這會變更的執行個體計算百分比。 例如，您可以將 25 放在 [**值**] 欄位中，而 2 如果您目前 8 的執行個體，將會新增。
    * 增加或減少到-這會將執行個體計數設定為您所定義的**值**。

7. 最後，您可以選擇炫下-若要再次縮放前一個的縮放比例動作之後等候此規則應該多久。

8. 設定您的規則後按**[確定]**。

9. 一旦您設定了所有您想要的規則，請務必按下 [**儲存**] 命令。

### <a name="scaling-with-multiple-steps"></a>縮放比例與多個步驟

上述範例是很基本。 不過，如果您想要更積極相關縮放比例設定 （或向下），您甚至可以新增多個小數位數規則，相同的度量。 例如，您可以定義兩個小數位數規則 CPU 百分比︰

1. 藉由擴展 1 的執行個體如果 CPU 百分比 60%
2. 擴充的 3 個執行個體如果 CPU 百分比上方鍵入 [85%

![多個小數位數的規則](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

使用此其他規則，如果您載入超過刻度動作]，再鍵入 [85%會收到而不是一個兩個額外的執行個體。

## <a name="scale-based-on-a-schedule"></a>[排定的縮放比例


根據預設，當您建立刻度規則一律會套用。 當您的設定檔標題上按一下時，您可以看到的︰

![設定檔](./media/insights-how-to-scale/Insights_Profile.png)

不過，您可能要有更積極縮放、 日或週、 期間比在週末。 您甚至可以關閉您的服務，完全關閉工作時間。

1. 若要這樣做，在您的設定檔中，選取 [**週期**]，而不是 [**一律**並選擇您想要套用的設定檔的時間。

2. 例如，有週適用於設定檔，[**天**] 下拉式清單中取消核取**星期六**和**星期日**。

3. 若要套用的日期期間的設定檔，請將的**開始時間**為您想要從開始的時間。
    ![預設循環](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. 按一下**[確定]**。

5. 接下來，您必須新增您想要在其他時間套用設定檔。 按一下 [**新增設定檔**資料列]。
    ![下班](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. 您新增]，第二個設定檔的名稱，例如無法呼叫該**工作關閉**。

7. 接著再選取 [**週期]** ，然後選擇您要在這段時間的執行個體計算範圍。

8. 使用預設設定檔中，選擇 [**天**您想要套用至]，此設定檔的**開始時間**天。

>[AZURE.NOTE] 自動調整大小會使用您選取不論**時區**日光節約規則。 不過，在日光節約時間的 UTC 位移會顯示基底時區位移，而不日光節約 UTC 位移。

9. 按一下**[確定]**。

10. 現在，您需要新增任何您想要套用在您的第二個設定檔的規則。 按一下 [**新增規則**]，然後您可以在其中建立相同的規則，您有期間的預設設定檔。
    ![新增至 [關閉工作的規則](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. 請務必建立兩個延展的規則，並不按比例縮放，或在設定檔的執行個體計算會只放大 （或 [減少）。

12. 最後，按一下 [**儲存**]。

## <a name="next-steps"></a>後續步驟

* [監視服務指標](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
* [啟用監控和診斷](insights-how-to-use-diagnostics.md)以您的服務收集詳細的頻率高的度量。
* [接收提醒通知](insights-receive-alert-notifications.md)作業事件發生時或指標交互臨界值。
* [監視應用程式效能](../application-insights/app-insights-azure-web-apps.md)如果您想要了解完全如何程式碼執行雲端中。
* [檢視事件及稽核記錄檔](insights-debugging-with-events.md)若要瞭解所有項目發生在您的服務。
* [監視器可用性和回應的任何網頁](../application-insights/app-insights-monitor-web-app-availability.md)與應用程式深入資訊，您可以瞭解如果您的頁面，讓已關閉。
