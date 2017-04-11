<properties
    pageTitle="資料流分析︰ 即時網路詐騙偵測 |Microsoft Azure"
    description="瞭解如何使用資料流分析建立即時網路詐騙偵測解決方案。 使用即時事件處理事件中心。"
    keywords="異常偵測網路詐騙偵測、 即時異常偵測"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>開始使用 Azure 資料流分析︰ 即時網路詐騙偵測

瞭解如何使用 Azure 資料流分析建立即時網路詐騙偵測的端對端解決方案。 顯示事件 Azure 事件集線器、 撰寫串流分析查詢彙總或提醒，並將結果傳送至資料的即時處理獲得深入見解輸出接收。 說明電信即時異常偵測，但範例技巧同樣適用於其他類型的網路詐騙偵測，例如信用卡或身分盜用案例。

串流分析是完全受管理的服務，提供低延遲可用性、 縮放、 複雜的事件處理串流雲端中的資料。 如需詳細資訊，請參閱[Azure 資料流分析的簡介](stream-analytics-introduction.md)。


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>即時案例︰ 電信和 SIM 網路詐騙偵測

電話公司有大量的資料來電。 公司需要從其資料下列動作︰

* 減少資料更容易管理金額，取得有關客戶使用方式的深入見解一段時間和地理區域。
* 即時偵測 SIM 網路詐騙 （從相同的身分識別，同時周圍，但在不同地理位置的多個通話），讓公司可以輕鬆地回應通知客戶或關閉服務。

標準的網際網路的項目 (IoT) 案例有大量遙測或從感應器的資料。 客戶是要彙總資料，或收到警告異常即時而定。

## <a name="prerequisites"></a>必要條件

- 從 Microsoft 下載中心下載[TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)
- 可省略︰ 程式碼，從[GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)事件產生器

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>建立 Azure 事件集線器輸入和消費者群組

範例應用程式會產生的事件，並傳送至即時處理事件集線器執行個體。 服務匯流排事件集線器是慣用的事件 ingestion 串流分析方法。 您可以進一步瞭解事件集線器[Azure 服務匯流排](/documentation/services/service-bus/)文件。

若要建立事件中心︰

1.  在[Azure 入口網站](https://manage.windowsazure.com/)中，按一下 [**新增** > **應用程式服務** > **服務匯流排** > **事件中心** > **快速建立**。 提供的名稱、 地區和新的或現有的命名空間，若要建立新的事件中心。  
2.  最佳作法是每一個資料流分析工作應該從單一事件中心消費者群組]。 我們會逐步引導您完成建立消費者群組稍後的程序。 [深入瞭解消費者群組](https://msdn.microsoft.com/library/azure/dn836025.aspx)。 若要建立消費者群組，請移至新建立的事件] 中心內，按一下 [**消費者群組**] 索引標籤，按一下 [**建立**]，底部的頁面]，然後提供您消費者群組的名稱。
3.  若要授與存取權 [事件] 中心內，我們需要建立共用的 access 原則。 按一下 [程式事件] 中心中的 [**設定**] 索引標籤。
4.  在 [**共用存取原則**] 底下，建立新的原則具有**管理**權限。

    ![共用 Access 原則，您可以在其中建立與管理權限原則。](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  按一下 [在頁面底部的 [**儲存**]。
6.  移至 [**儀表板**，按一下 [**連線資訊**]，底部的頁面，然後複製並儲存的連線資訊。

## <a name="configure-and-start-the-event-generator-application"></a>設定和啟動事件產生器應用程式

我們提供會產生範例傳入通話中繼資料，並將其推入事件集線器的用戶端應用程式。 您可以使用下列步驟，設定此應用程式。  

1.  下載[TelcoGenerator.zip 檔案](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)，然後將它解壓縮的目錄。

    > [AZURE.NOTE] Windows 可能會封鎖下載的 zip 檔案。 以滑鼠右鍵按一下檔案]，然後選取 [**內容**。 如果您看到 「 此檔案來自另一部電腦，可能會封鎖以協助保護您的電腦 」 郵件中，選取 [**解除封鎖**] 方塊，然後按一下套用 zip 檔案。

2.  將事件中心連線字串與名稱取代 telcodatagen.exe.config Microsoft.ServiceBus.ConnectionString 和 EventHubName 值。

    您從 Azure 入口網站複製連線字串會在結尾處的連線名稱。 請務必移除 」;EntityPath =<value>」 的 「 新增鍵 = 」 欄位。

3.  啟動應用程式。 使用狀況如下所示︰

    telcodatagen.exe [#NumCDRsPerHour] [SIM 卡片網路詐騙機率] [#DurationHours]

下列範例會產生與網路詐騙 20%機率 1000 事件的兩個小時。

    telcodatagen.exe 1000 .2 2

您會看到傳送到您的事件中樞的記錄。 某些鍵的欄位，我們會使用此即時網路詐騙偵測應用程式中定義這裡︰

| 記錄 | 定義 |
| ------------- | ------------- |
| CallrecTime | 時間戳記的號碼開始時間。 |
| SwitchNum | 電話切換用來連線通話。 |
| CallingNum | 電話號碼的來電顯示。 |
| CallingIMSI | 國際行動訂閱者的身分識別 (IMSI)。  來電者之間的唯一識別碼。 |
| CalledNum | 通話收件者的電話號碼。 |
| CalledIMSI | 國際行動訂閱者的身分識別 (IMSI)。  通話收件者的唯一識別碼。 |


## <a name="create-a-stream-analytics-job"></a>建立資料流分析工作
現在，我們已電信事件的資料流時，我們可以將資料流分析工作設定為分析即時這些事件。

### <a name="provision-a-stream-analytics-job"></a>佈建資料流分析工作

1.  在 Azure 入口網站中，按一下 [**新增** > **資料服務** > **串流分析** > **快速建立**。
2.  指定下列的值，然後再按一下 [**建立資料流分析作業**︰

    * **工作名稱**︰ 輸入作業名稱。

    * **地區**︰ 選取您要執行工作的地區。 請考慮放在同一個區域以確保較佳的效能，並確定，您將不會付款區域間傳送資料的工作及 [事件] 中心。

    * **儲存帳戶**︰ 選擇您想要用來儲存監視資料執行此區域內的所有資料流分析工作 Azure 儲存體帳戶。 您必須選擇現有的儲存空間帳戶或建立新的選項。

3.  按一下 [清單資料流分析工作的左窗格中的 [**資料流分析**]。

    ![資料流分析服務圖示](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    新的工作會顯示其狀態是 [**建立]**。 在頁面底部的 [**開始**] 按鈕會停用通知。 您可以開始工作之前，您必須設定工作輸入、 輸出和查詢。

### <a name="specify-job-input"></a>指定工作的輸入
1.  在資料流分析工作中，按一下頂端的頁面上，**輸入**，然後按一下 [**新增輸入**。 [開啟] 對話方塊會逐步引導您完成設定您的輸入幾個步驟。
2.  按一下 [**資料流**]，然後按一下 [從左至右] 按鈕。
3.  按一下 [**事件] 中心內**，，然後按一下 [從左至右] 按鈕。
4.  輸入或選取在第三個頁面上的下列值︰

    * **輸入 alias （別名)**: 輸入易記的名稱，例如*CallStream*，這項工作。 請注意，您將使用此名稱在查詢中更新版本。

    * **事件中心**︰ 如果您建立的事件中樞在同一份訂閱，以資料流分析工作，請選取 [命名空間中的 [事件] 中心。

        如果事件中心位於不同的訂閱，選取 [**使用事件中心從另一個訂閱**，並手動輸入的資訊**服務匯流排命名空間**、**事件中心名稱**、**事件中心原則名稱**、**事件中心原則機碼**及**事件中樞的磁碟分割計數**。

    * **事件中心名稱**︰ 選取 [事件] 中心內的名稱。

    * **事件中心原則名稱**︰ 選取您之前在本教學課程中建立的事件中心原則。

    * **事件中心消費者群組**︰ 輸入您之前在本教學課程中建立消費者群組的名稱。

5.  按一下 [從左至右] 按鈕。
6.  指定下列的值︰

    * **事件序列化格式**︰ JSON
    * **編碼**︰ UTF8
7.  按一下 [**檢查**] 按鈕新增此來源，並確認資料流分析，可以成功連線到 [事件] 中心。

### <a name="specify-job-query"></a>指定工作的查詢

資料流分析支援簡單的宣告查詢的模型，描述轉換即時處理。 若要進一步瞭解語言，請參閱[Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/dn834998.aspx)。 本教學課程中會協助您撰寫及測試您的通話資料的即時資料流上的多個查詢。

#### <a name="optional-sample-input-data"></a>可省略︰ 範例輸入的資料
若要驗證對實際工作資料的查詢，您可以使用**範例資料**功能，從您的資料流擷取事件，並建立。事件的 JSON 檔案以進行測試。  下列步驟說明如何進行此動作。 我們也提供範例[telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)檔案供測試之用。

1.  選取 [事件] 中心內輸入，然後按一下 [在頁面底部的**範例資料**。
2.  在開啟的對話方塊中，指定**開始時間**開始收集資料和**持續時間**到消耗多少其他資料。
3.  按一下 [**檢查**] 按鈕，開始輸入的範例資料。  可能需要幾分鐘，或產生的資料檔案的兩個。  完成程序後，按一下 [**詳細資料**，下載產生。JSON 檔案，然後將其儲存。

    ![下載並儲存 JSON 檔案已處理的資料](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>傳遞查詢

如果您想要保存每個事件，您可以使用傳遞查詢閱讀事件或郵件的內容中的所有欄位。 若要開始，請執行簡單的傳遞查詢事件中的所有欄位的專案。

1.  按一下 [從資料流分析作業頁面頂端的**查詢**。
2.  新增下列程式碼編輯器︰

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] 請確定輸入來源的名稱符合您先前指定的輸入名稱。

3.  在查詢編輯器] 底下，按一下 [**測試**]。
4.  提供的測試檔案。 使用您建立使用先前的步驟，或是使用[telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json)。
5.  按一下 [**檢查**] 按鈕，請參閱查詢定義下方顯示的結果。

    ![查詢定義結果](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>預測資料行

我們現在會較小集減少傳回的欄位。

1.  變更程式碼編輯器中的查詢︰

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  在查詢編輯器] 若要查看查詢結果中按一下 [**重新執行**]。

    ![在 [查詢編輯器中的成果。](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>內送的計數通話依地區︰ 解決視窗彙總

若要比較的每個地區來電數，我們將使用[TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx)取得來電分組**SwitchNum**每五秒的計數。

1.  變更程式碼編輯器中的查詢︰

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    此查詢會使用**時間戳記的**關鍵字裝載暫時計算使用中指定的時間戳記欄位。 如果未指定此欄位，想要使用的時間，每項事件到達 [事件] 中心執行視窗作業。 請參閱[「 到達時間與應用程式時間 」 中的資料流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)。

    請注意，您可以使用**System.Timestamp**屬性來存取時間戳記的每一個視窗的結尾。

2.  在查詢編輯器] 若要查看查詢結果中按一下 [**重新執行**]。

    ![Timestand 所用的查詢結果](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>使用自我聯結 SIM 網路詐騙偵測

若要識別潛在權利的使用方式，我們通話小於 5 的秒數來自從相同的使用者，但在不同的位置。  我們[加入](https://msdn.microsoft.com/library/azure/dn835026.aspx)通話事件的本身檢查這種情況下的資料流。

1.  變更程式碼編輯器中的查詢︰

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  在查詢編輯器] 若要查看查詢結果中按一下 [**重新執行**]。

    ![聯結的查詢結果](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>建立輸出接收

現在，我們已定義事件資料流時，輸入要內嵌事件，以及執行資料流，轉換的查詢事件中樞的最後一個步驟就是定義為工作輸出接收。 我們會寫入 Azure Blob 儲存體權利行為的事件。

若要建立 Blob 儲存體的容器，如果您已經沒有使用下列步驟。

1.  使用現有的儲存空間帳戶，或按一下即可建立新的儲存空間帳戶**新增 > 資料服務 > 儲存 > 快速建立**，然後依照指示進行。
2.  選取的儲存空間帳戶，按一下 [**容器**頂端的頁面]，再按一下 [**新增**。
3.  指定您容器的**名稱**並將其**存取****公用 Blob**。

## <a name="specify-job-output"></a>指定工作輸出

1.  在資料流分析工作中，按一下 [**輸出**頂端的頁面]，然後按一下**新增輸出。** [開啟] 對話方塊會逐步引導您完成設定您的輸出的幾個步驟。
2.  按一下 [ **BLOB 儲存體**]，然後按一下 [從左至右] 按鈕。
3.  輸入或選取在第三個頁面上的下列值︰

    * **輸出 alias （別名)**: 輸入此工作輸出易記的名稱。
    * **訂閱**︰ 如果您建立 Blob 儲存體在同一份訂閱，以資料流分析工作，請按一下 [**使用目前的訂閱的儲存空間帳戶**。 如果您儲存在不同的訂閱，請按一下 [**使用另一個訂閱的儲存空間帳戶**，並手動輸入資訊**儲存帳戶**、**儲存帳戶金鑰]**，以及**容器**。
    * **儲存帳戶**︰ 選取的儲存體帳戶名稱。
    * **容器**︰ 選取容器的名稱。
    * **檔案名稱首碼**︰ 輸入寫入 blob 輸出時要使用的檔案首碼。

4.  按一下 [從左至右] 按鈕。
5.  指定下列的值︰

    * **事件序列化格式**︰ JSON
    * **編碼**︰ UTF8

6.  按一下 [**檢查**] 按鈕，新增這個來源，並確認資料流分析可以成功連線到您的儲存空間帳戶。

## <a name="start-job-for-real-time-processing"></a>啟動即時處理工作

因為工作輸入查詢，與輸出所有已指定，我們已準備好開始進行即時網路詐騙偵測串流分析工作。

1.  從工作的**儀表板**中，按一下 [在頁面底部的 [**開始**]。
2.  在開啟的對話方塊中，按一下**工作的開始時間**]，然後按一下對話方塊底部的 [**檢查**] 按鈕。 工作狀態會變更為**開始**，並會很快就會變更為**執行**。

## <a name="view-fraud-detection-output"></a>檢視網路詐騙偵測輸出

使用的工具，例如[Azure 儲存檔案總管](http://storageexplorer.com/)或[Azure 檔案總管](http://www.cerebrata.com/products/azure-explorer/introduction)來檢視權利事件與寫入即時輸出。  

![偵測網路詐騙︰ 即時檢視權利的事件](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>取得支援
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
