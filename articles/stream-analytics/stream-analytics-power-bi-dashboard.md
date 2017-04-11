<properties
    pageTitle="Power BI 儀表板上串流分析 |Microsoft Azure"
    description="使用即時串流 Power BI 儀表板來收集商務智慧功能及分析資料流分析工作的大量資料。"
    keywords="分析儀表板即時儀表板"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>串流分析及 Power BI: 即時分析儀表板串流資料

Azure 資料流分析，可讓您充分利用前置商務智慧工具，Microsoft Power BI 的其中一個。 瞭解如何使用 Azure 資料流分析來分析大量資料流資料與取得即時的 Power BI 分析儀表板中充分。

使用[Microsoft Power BI](https://powerbi.com/)快速建立即時儀表板。 [觀看影片示範如何](https://www.youtube.com/watch?v=SGUpT-a99MA)。

在本文中，瞭解如何使用 Power BI 作為輸出 Azure 資料流分析工作以建立您自己的自訂的商務智慧工具，並利用即時的儀表板。

## <a name="prerequisites"></a>必要條件

* Microsoft Azure 帳戶
* 使用資料串流串流分析工作輸入。 資料流分析接受輸入 Azure 事件集線器或 Azure Blob 儲存體。  
* Power BI 用的公司或學校帳戶

## <a name="create-azure-stream-analytics-job"></a>建立 Azure 資料流分析工作

從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**新增資料服務、 串流分析、 快速建立**]。

請指定下列的值，然後按一下 [**建立資料流分析作業**︰

* **工作名稱**-輸入作業名稱。 例如， **DeviceTemperatures**。
* **地區**-選取您想要位於工作的地區。 請考慮放在同一個區域，以確保最佳效能，避免造成區域之間的資料傳輸成本的工作及 [事件] 中心。
* **儲存帳戶**-選擇您想要使用儲存執行此區域內的所有資料流分析作業的監控資料的儲存空間帳戶。 您必須選擇現有的儲存空間帳戶或建立新的選項。

按一下 [清單資料流分析工作的左窗格中的 [**資料流分析**]。

![graphic1][graphic1]

> [AZURE.TIP] 新的工作會列出其狀態是 [**尚未開始**。 在頁面底部的 [**開始**] 按鈕會停用通知。 這是您必須設定工作輸入輸出、 查詢，等才能開始工作預期的行為。

## <a name="specify-job-input"></a>指定工作的輸入

在此教學課程中，我們會假設您使用的事件中樞的輸入與 JSON 序列化 utf-8 編碼。

* 按一下 [工作名稱]。
* 按一下 [**輸入**] 頁面的頂端，然後按一下 [**新增輸入**。 [開啟] 對話方塊會引導您的步驟設定您的輸入數字。
*   選取**資料流**]，然後按一下 [從左至右] 按鈕。
*   選取 [**事件] 中心內**，，然後按一下 [從左至右] 按鈕。
*   輸入或選取在第三個頁面上的下列值︰
  * **輸入別名**-輸入好記的名稱，輸入此工作。 請注意，您將使用此名稱在查詢中稍後。
  * 以資料流分析工作選取同一份訂閱中的 [事件] 中心內的命名空間位於**事件中心**-如果您建立的事件中心。
*   如果您的事件中心位於不同的訂閱，選取 [**使用事件中心從另一個訂閱**，並手動輸入資訊**服務匯流排命名空間**、**事件中心名稱**、**事件中心原則名稱**、**事件中心原則索引鍵**，以及**事件中樞的磁碟分割計數**。

> [AZURE.NOTE]  這個範例中使用的是 16 的磁碟分割區，預設數目。

* **事件中心名稱**-選取 [必須 Azure 事件中樞的名稱。
* **事件中心原則名稱**-選取事件中心原則的您使用的 [事件] 中心。 請確定此原則具有管理權限。
*   **事件中心消費者群組**– 您可以保留空白，或指定消費者群組事件中心上。 請注意，每個消費者] 群組中的事件中心可以只 5 讀者一次。 因此，據此決定您的工作的正確的消費者群組。 如果您將欄位保留空白，它會使用預設的消費者群組。

*   按一下 [從左至右] 按鈕。
*   指定下列的值︰
  * **事件序列化格式**JSON
  * **編碼**-UTF8
*   按一下 [新增此來源，並確認資料流分析，可以成功連線到 [事件] 中心中的 [檢查] 按鈕。

## <a name="add-power-bi-output"></a>新增 Power BI 輸出

1.  按一下 [**輸出**頂端的頁面]，然後按一下 [**新增輸出**。 您會看到 Power BI 列為輸出選項。

    ![graphic2][graphic2]  

2.  選取 [ **Power BI** ]，然後按一下 [從左至右] 按鈕。
3.  您會看到的畫面如下所示︰

    ![graphic3][graphic3]  

4.  在此步驟中，提供資料流分析工作輸出的公司或學校帳戶。 如果您已經 Power BI 帳戶，請選取 [**立即授權**]。 如果沒有，選擇 [**立即註冊**。 [以下是很好的部落格說明 Power BI 註冊的詳細資料](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx)。

    ![graphic11][graphic11]  

5.  接下來，您會看到的畫面如下所示︰

    ![graphic4][graphic4]  

提供為下列值︰

* **輸出別名**– 您可以將任何輸出別名，讓您輕鬆地參照。 此輸出別名是如果您決定要有多個工作的輸出特別有用。 在此情況下，您必須參照在您的查詢輸出。 例如，我們要使用的輸出別名值 = 「 OutPbi 」。
* **資料集名稱**-提供您想要的輸出擁有 Power BI 資料集名稱。 例如，我們要使用 「 pbidemo 」。
*   **資料表名稱**-提供表格名稱底下的 [您的 Power BI 輸出資料集。 例如，假設我們呼叫該 「 pbidemo 」。 目前，Power BI 輸出資料流分析工作可能只有一個資料表中資料集。
*   **工作區**– 選取您要將會建立資料集的 Power BI 租用戶中的工作區。

>   [AZURE.NOTE] 您應該不明確地建立這個資料集和資料表 Power BI 帳戶中。 他們會自動建立啟動您的資料流分析工作和工作 Power bi 中開始幫浦輸出的時間。 如果您的工作查詢無法傳回任何結果，不會建立資料表與資料集。

*   按一下**[確定]**，**測試連線**，以防您輸出現在已完成]。

>   [AZURE.WARNING] 也請注意，如果 Power BI 已經有的資料集和具有相同的名稱，為您提供此串流分析工作中的表格，現有資料將會覆寫。


## <a name="write-query"></a>撰寫的查詢

移至您的工作中的 [**查詢**] 索引標籤。 撰寫您要在 Power BI 的輸出的查詢。 例如，可能是下列 SQL 查詢等項目︰

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



啟動您的工作。 驗證您的事件集散地所收到的事件，以及查詢產生預期的結果。 如果您的查詢輸出 0 個資料列，Power BI 資料集和資料表將不會自動建立。

## <a name="create-the-dashboard-in-power-bi"></a>在 [Power BI 中建立儀表板

移至[Powerbi.com](https://powerbi.com)和您的公司或學校帳戶登入。 如果資料流分析工作查詢輸出結果，您會看到已建立您的資料集︰

![graphic5][graphic5]

建立儀表板，請移至 [儀表板] 選項，然後建立新的儀表板。

![graphic6][graphic6]

我們會標籤此範例中，「 示範儀表板]。

現在按一下您的資料流分析工作 (在本例中目前 pbidemo) 所建立的資料集。 您會移至頁面來建立這個資料集的上方圖表。 以下是但您可以建立的報表的其中一個範例︰

選取 Σ temp 和時間] 欄位。 他們會自動移至值] 和 [座標軸的圖表︰

![graphic7][graphic7]

使用此，您都會自動收到圖表如下︰

![graphic8][graphic8]

在 [值] 區段中，按一下向下暫存的下拉式清單並選擇**平均**溫度的和圖表**視覺效果**上，然後按一下選擇**折線圖**︰

![graphic9][graphic9]

您現在會收到一段時間的折線圖的平均值。  使用下列 [固定] 選項，您可以將固定這您先前建立的儀表板︰

![graphic10][graphic10]

現在當您檢視此固定的報表的儀表板，您會看到即時更新的報表。 試試看想變更您的事件 – 特殊圖文集 temp 或其他項目中的資料，您會看到即時反映在您的儀表板中的效果。

本教學課程示範如何建立的筆記，但一種資料集的圖表。 Power BI 可協助您建立其他商務智慧工具為您的組織。 另一個範例中的 Power BI 儀表板，觀看[開始使用 Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s)的影片。

如需進一步瞭解設定 Power BI 輸出，以及要使用 Power BI 群組，檢閱[了解資料流分析輸出](stream-analytics-define-outputs.md "了解資料流分析輸出") [Power BI] 區段](stream-analytics-define-outputs.md#power-bi)。 其他實用的資源，若要進一步瞭解建立儀表板使用 Power BI 是[Power BI 儀表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)。

## <a name="limitations-and-best-practices"></a>限制及最佳作法

Power BI 採用並行和處理量的限制式，如下所示︰ [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI 價格")

由於那些 Power BI 落本身最符合案例 Azure 資料流分析會大量的資料載入減少的位置。
我們建議使用 TumblingWindow 或 HoppingWindow，資料推入是最 1 推入/第二，且您的查詢落在處理量需求，– 您可以使用下列方程式來計算，讓您視窗秒數的值︰

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

做為範例 – 如果您有 1000 傳送資料每秒的裝置，您是在 Power BI Pro SKU 支援 1000000 的列小時且您想要在 Power BI 中取得每個裝置的平均資料您可以執行最多推入每個裝置的每個 4 秒鐘 （如下圖所示）︰  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

這表示我們想變更原始查詢︰

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>重新整理] 檢視中

常見的問題是 「 為何沒有儀表板自動更新中嗎？ 」。

若要達到此目標，在中使用 [問與答提出問題，例如 「 最大值的時間戳記哪裡今天 temp 」 和釘選至儀表板的磚。

### <a name="renew-authorization"></a>更新授權

您必須重新驗證您的 Power BI 帳戶之後建立或最後驗證您的工作,，已變更其密碼。 如果您的 Azure Active Directory (AAD) 租用戶，您也必須更新 Power BI 授權每個 2 週設定多重因素驗證 (MFA)。 此問題的症狀是沒有工作輸出是 「 驗證使用者錯誤 」 作業記錄中︰

![graphic12][graphic12]

同樣地，如果工作嘗試啟動時已過期權杖，會發生錯誤而工作開始將會失敗。 錯誤看起來會像下面︰

![中驗證錯誤](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

若要解決此問題，請停止您執行的工作並移至您的 Power BI 輸出。  按一下 [更新授權 」 連結，並重新啟動您的工作，以避免資料遺失的最後一個停止時間。

![中驗證更新](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

使用 Power BI 的授權會重新整理後，您會看到 [授權] 區域中的綠色警示︰

![中驗證更新](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
