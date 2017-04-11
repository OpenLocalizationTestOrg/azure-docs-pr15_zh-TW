<properties
    pageTitle="開始使用 Azure 監視器 |Microsoft Azure"
    description="開始使用掌握您的資源的操作，並依據資料採取 Azure 監視器。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>開始使用 Azure 監視器

Azure 監視器為平台服務，提供一個來源監視 Azure 資源。 Azure 監視器，您可以以視覺化方式呈現、 查詢、 路由傳送、 封存的資料夾，或採取動作的指標，然後從 Azure 中的資源記錄。 您可以使用此使用監視器入口網站刀，[監視器 PowerShell Cmdlet](./insights-powershell-samples.md)、[跨平台 CLI](insights-cli-samples.md)或[Azure 監視器 REST Api](https://msdn.microsoft.com/library/dn931943.aspx)的資料。 本文中，我們會逐步執行幾個 Azure 監視器，示範使用入口網站的主要元件。

1. 在入口網站中，瀏覽至 [**更多服務**，尋找 [**監視器**] 選項。 按一下 [星形] 圖示以新增至我的最愛] 清單的這個選項，以便一律是從左導覽列可輕鬆存取]。

    ![監視服務] 清單中](./media/monitoring-get-started/monitor-more-services.png)

2. 按一下以開啟**監視器**刀的 [**監視器**] 選項。 此刀結合所有監控設定和資料成一個合併檢視。 將第一次開啟**活動記錄**一節。

    ![監視器刀導覽](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] 上方所顯示的**服務通知**和**通知群組**選項才會顯示已加入私人預覽這些功能的人。

    Azure 監視器有三種監視資料的基本類別︰**活動記錄****指標**，與**診斷記錄**。

3. 按一下 [以確保會顯示 [活動的記錄] 區段中的 [**活動登入**]。

    ![活動記錄刀](./media/monitoring-get-started/monitor-act-log-blade.png)

    [**活動記錄**](./monitoring-overview-activity-logs.md)會說明執行您的訂閱中的資源的所有作業。 使用活動記錄，您可以決定 ' 什麼，人員，以及何時 」 的任何建立、 更新或刪除作業，在您的訂閱中的資源。 例如，活動記錄會告訴您，時停止 web 應用程式，誰已停止。 活動記錄事件會儲存在平台上，才能查詢 90 天。
   
    您可以建立並儲存查詢以一般篩選，然後將其釘選至入口網站的儀表板最重要的查詢，讓您知道永遠是否符合您準則的事件發生。

4. 在最後一週，篩選特定的資源群組檢視，然後按一下 [**儲存**] 按鈕。

    ![儲存活動記錄查詢](./media/monitoring-get-started/monitor-act-log-save.png)

5. 現在，按一下 [**固定**] 按鈕。

    ![按一下 [釘選活動記錄檔](./media/monitoring-get-started/monitor-act-log-pin.png)

    大多數的檢視，在此逐步解說，都可以釘選到 [儀表板。 這可協助您在您的服務上建立單一來源的操作資料的資訊。 

6. 返回您的儀表板。 您現在可以看到您的儀表板中顯示的查詢 （及結果的數字）。 這是如果您想要快速查看最近中發生您的訂閱，例如任何高設定檔的動作。 新的角色指派給或 VM 已遭刪除。

    ![釘選到儀表板的活動記錄](./media/monitoring-get-started/monitor-act-log-db.png)

7. 返回 [**監視器**] 方塊，然後按一下 [**指標**] 區段。 您必須選取篩選，然後選取 [使用刀頂端的下拉式選項的資源。

    ![篩選資源的指標](./media/monitoring-get-started/monitor-met-filter.png)

    Azure 的所有資源都發出[**指標**](./monitoring-overview-metrics.md)。 此檢視結合所有指標放大鏡單一窗格中，您可以輕鬆地了解如何執行您的資源。

8. 選取資源之後, 所有可用的指標出現刀左側。 您可以一次選取指標圖表多個度量和修改圖形類型和時間範圍。 您也可以檢視此資源上設定的所有公制通知。

    ![公制刀](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] 在您的資源，讓[應用程式的深入見解](../application-insights/app-insights-overview.md)及/或 Windows 或 Linux Azure 診斷，才有一些指標。

9. 當您滿意與圖表，您可以使用 [**固定**] 按鈕，以將其釘選至您的儀表板。

10. 返回**監視器**刀，然後按一下 [**診斷記錄**。

    ![診斷記錄刀](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**診斷記錄**](monitoring-overview-of-diagnostic-logs.md)，就發出的記錄*，*提供該特定資源的作業相關資料的資源。 例如，網路安全性群組規則計數器及邏輯應用程式工作流程記錄檔是兩種類型的診斷記錄。 這些記錄可以是儲存在儲存帳戶、 傳送至 [事件] 中心內，及/或傳送至[記錄分析](../log-analytics/log-analytics-overview.md)。 記錄檔分析有 Microsoft 的操作智慧的進階搜尋和提醒。
   
    在入口網站，您可以檢視，並在您的訂閱識別有啟用診斷記錄中篩選的所有資源的清單。

11. 按一下 [診斷記錄刀中的資源。 如果診斷記錄儲存在儲存帳戶，您會看到每小時的記錄，您可以直接下載的清單。

    ![一個診斷記錄](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    您也可以按一下 [**診斷設定**，可讓您設定或修改您的設定保存儲存帳戶串流到事件，或傳送記錄檔分析工作區。

    ![啟用診斷記錄](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    如果您已設定診斷記錄至記錄的分析，您可以搜尋這些**記錄搜尋**的區段中的入口網站。

12. 瀏覽至 [**通知**] 區段的監視器刀。

    ![針對公用通知刀](./media/monitoring-get-started/monitor-alerts-nopp.png)

    這裡，您可以在 Azure 資源管理所有[**通知**](./monitoring-overview-alerts.md)。 這包含指標、 活動記錄事件 （在私人預覽版本）、 應用程式的深入見解 web 測試 （位置），以及應用程式的深入見解主動診斷通知。 傳送電子郵件或 HTTP 張貼至 webhook URL]，即可觸發通知。
   
13. 按一下 [建立提醒的 [**新增公制的提醒**]。

    ![新增公制的提醒](./media/monitoring-get-started/monitor-alerts-add.png)

    然後您可以將通知固定儀表板，輕鬆地在任何時候查看其狀態。

14. [監視器] 區段也包含[應用程式的深入見解](../application-insights/app-insights-overview.md)應用程式和[記錄分析](../log-analytics/log-analytics-overview.md)管理解決方案的連結。 這些其他 Microsoft 產品擁有深層整合 Azure 監視器。

15. 如果您不使用應用程式的深入見解或記錄分析，可能會是 Azure 監視器有與您的目前的監控、 記錄，警示產品合作關係。 請參閱我們的[合作夥伴網頁](./monitoring-partners.md)的完整清單，以及如何整合的指示。

遵循這些步驟，釘選所有相關的並排的儀表板，您可以建立全面涵蓋所有內容檢視應用程式與基礎結構，像這樣︰

![Azure 監視器儀表板](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>後續步驟
- 閱讀[Azure 監視器的概觀](./monitoring-overview.md)
