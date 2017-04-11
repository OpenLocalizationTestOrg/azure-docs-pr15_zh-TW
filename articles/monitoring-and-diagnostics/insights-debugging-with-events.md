<properties
    pageTitle="檢視事件及稽核記錄"
    description="瞭解如何查看所有的 Azure 訂閱中發生的事件。"
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>檢視事件及稽核記錄

執行 Azure 資源的所有作業完全都稽核 Azure 資源管理員，從建立及刪除要授與或撤銷存取。 您可以瀏覽 Azure 入口網站中，這些記錄，您也可以使用[REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) ，以程式設計方式存取完整的事件。

## <a name="browse-the-events-impacting-your-azure-subscription"></a>瀏覽影響 Azure 訂閱的事件

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下**瀏覽]** ，然後選取 [**稽核記錄**。  
    ![瀏覽中心](./media/insights-debugging-with-events/Insights_Browse.png)
3. 這會開啟刀，顯示所有影響您的訂閱的任何過去 7 天的事件。 在頂端是圖表，顯示 [依層級]，並在下面記錄的完整清單︰ ![所有事件](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] 您只能在 Azure 入口網站檢視指定訂閱 500 最新的事件。

4. 您可以按一下任何的記錄項目，以查看其組成的事件。 例如，當您在資源群組部署項目，許多不同的資源可能會建立或修改。 每個項目，您可以看到︰
    * 事件-的**層級**，例如，可能就只是要追蹤 （**資訊**），或當項目有任何錯誤，您需要瞭解 （**錯誤**）。
    * **狀態**-最後狀態通常會**成功**或**失敗**，但也可能會**接受**較長時間執行作業。
    * *當*事件發生。
    * *誰*會執行作業的任何人。 並非所有作業由使用者都執行，部分都執行的後端服務讓它們不會有**來電者**。
    * **相互關聯識別碼**的事件-這是這組作業的唯一識別碼。

5. 從該處您可以移至 [詳細資料刀，以查看該事件的細節。

    ![資源群組](./media/insights-debugging-with-events/Insights_EventDetails.png)

    **失敗**事件，此頁面通常會包括**子狀態**和**屬性**] 區段包含有用的偵錯的詳細資料。

## <a name="filter-to-specific-logs"></a>篩選特定的記錄

若要查看套用至特定的實體，或特定類型的事件，您可以按一下 [**篩選**] 命令來篩選稽核記錄刀。 您也可以使用篩選刀若要變更**時間範圍**的稽核記錄刀。

一旦您按一下此命令時，會開啟新的刀︰

![篩選](./media/insights-debugging-with-events/Insights_EventFilter.png)

有四種類型的篩選︰

1. 訂閱
2. 依**資源群組**
3. 依**資源類型**
4. **資源**的特定-此，您必須過去您感興趣的資源的完整*資源識別碼*

此外，您也可以篩選事件由誰執行事件，或事件的層級。

一次您已選擇想要查看，請按一下刀底部的 [**更新**] 按鈕。

## <a name="monitor-events-impacting-specific-resources"></a>監視事件影響特定的資源

1. 按一下 [上**瀏覽**以尋找您感興趣的資源。 您也可以查看所有記錄檔的整個**資源群組**。
2. 在資源的刀向下捲動，直到您找到 [**事件**] 方塊。  
    ![事件磚](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. 按一下以查看篩選只選取資源的事件的圖磚上。 若要變更的時間範圍，或套用更具體的篩選，您可以使用 [**篩選**] 命令。

## <a name="next-steps"></a>後續步驟

* [收到警示通知](insights-receive-alert-notifications.md)時事件發生。
* [監視服務指標](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
* 若要找出 Azure 遇到效能降低或服務中斷時[追蹤服務健康狀況](insights-service-health.md)。  
