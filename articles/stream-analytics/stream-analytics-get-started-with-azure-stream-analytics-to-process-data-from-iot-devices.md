<properties
    pageTitle="開始使用 Azure 資料流分析程序資料從 IoT 裝置。 |Microsoft Azure"
    description="IoT 感應器標記與資料流與資料流分析及處理即時資料"
    keywords="iot 解決方案 iot 快速入門"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>開始使用 Azure 資料流分析程序資料從 IoT 裝置

在本教學課程中，您將學習如何建立資料流處理邏輯從網際網路的項目 (IoT) 裝置收集的資料。 我們會示範如何建立您的方案，快速且經濟使用實際的網際網路的項目 (IoT) 使用案例。

## <a name="prerequisites"></a>必要條件

-   [Azure 訂閱](https://azure.microsoft.com/pricing/free-trial/)
-   從[GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)可下載範例查詢和資料檔案

## <a name="scenario"></a>案例

Contoso 是公司工業自動化空格中的，具有完全自動化其製造程序。 在此植物機器有感應器也能發出的即時資料流。 在此案例中，生產 floor 管理員想有感應器資料查詢模式，並在其上採取動作的即時深入資訊。 我們會使用感應器資料串流分析查詢語言 (SAQL)，以尋找資料的內送的資料流從感興趣的模式。

以下是從德州感應器標籤裝置產生資料。

![德州感應器標籤](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

內容的資料 JSON 格式，類似下列所示︰


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

在真實案例中，您可能會有數百產生的事件，以資料流這些感應器。 最好是閘道裝置想執行[Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)或[Azure IoT 集線器](https://azure.microsoft.com/services/iot-hub/)發送這些事件的程式碼。 您的資料流分析工作會內嵌的事件集線器這些事件，然後針對資料流時執行查詢即時分析。 然後，您可以傳送結果至其中一個[支援輸出](stream-analytics-define-outputs.md)。

以便於使用，此發生的入門的指南可提供的範例資料檔案，這從實感應器標籤裝置擷取。 您可以執行查詢的範例資料，並查看結果。 在後續的教學課程，您將學習如何將您的工作連線到輸入輸出，並將其部署到 Azure 服務。

## <a name="create-a-stream-analytics-job"></a>建立資料流分析工作

1. 在[Azure 入口網站](http://portal.azure.com)中，按一下加號，然後輸入**資料流分析**右邊的 [文字] 視窗中。 在結果清單中，然後選取 [**資料流分析工作**]。

    ![建立新的資料流分析工作](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. 輸入唯一的工作名稱，然後確認該訂閱已正確的項目，為您的工作。 然後建立新的資源群組，或選取現有訂閱。

3. 然後選取您的工作的位置。 處理速度和縮減成本資料的建議傳輸為資源群組，並想要的儲存空間帳戶選取同一個位置。

    ![建立新的資料流分析工作詳細資料](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] 您應該建立每個地區一次此儲存帳戶。 將共用此儲存在該區域中建立的所有資料流分析作業。

4. 核取 [將您的工作放在您的儀表板，然後按一下 [**建立**] 方塊。

    ![建立進行中的工作](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. 您應該會看到 「 部署啟動...」 在瀏覽器視窗的右上角顯示。 推出它會變更為已完成的視窗，如下所示。

    ![建立進行中的工作](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>建立 Azure 資料流分析查詢

建立您的工作後，有時間加以開啟，並建立查詢。 您可以輕鬆地存取您的工作，，按一下方塊。

![工作磚](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

在 [**工作拓撲**窗格按一下以移至 [查詢編輯器] 中的 [**查詢**] 方塊。 [**查詢**編輯器] 可讓您輸入的連入事件資料執行轉換 T SQL 查詢。

![查詢方塊](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>查詢︰ 封存原始資料

最簡單格式是查詢的封存所有的輸入的資料，以指定輸出傳遞查詢。 從 [ [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)下載範例資料檔案，您的電腦上的位置。 

1. 貼上 PassThrough.txt 檔案中的查詢。 

    ![測試輸入資料流](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. 按一下 [輸入] 旁的三個點，然後選取 [**上傳檔案的範例資料**] 方塊。

    ![測試輸入資料流](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. 在右側開啟結果的一個窗格，在選取的 HelloWorldASA InputStream.json 資料檔案從您下載的位置，然後按一下窗格底部的**[確定**]。

    ![測試輸入資料流](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. 再按一下左上角的視窗] 區域中的 [**測試**] 齒輪，對範例資料集的測試查詢 [處理程序。 結果] 視窗隨即會開啟查詢] 下方，為處理完畢。

    ![測試結果](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>篩選根據條件的資料的查詢︰

現在就讓我們嘗試篩選依據的條件結果。 我們想要顯示結果來自 「 sensorA。 」 的事件 查詢位於 Filtering.txt 檔案。

![篩選資料流](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

請注意區分大小寫的查詢比較字串值。 按一下即可執行查詢的 [**測試**] 齒輪。 查詢傳回 389 登出 1860年事件的列。

![第二個查詢測試輸出結果](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>若要觸發商務工作流程的查詢︰ 提醒

現在就讓我們讓我們的查詢詳細。 針對每個類型的感應器中，我們要監視平均溫度每 30 秒的時間和平均溫度大於 100 度時，才會顯示結果。 我們會撰寫下列查詢，然後按一下 [**測試**，以查看結果。 查詢位於 ThresholdAlerting.txt 檔案。

![30 秒篩選查詢](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

您現在應該會看到包含只 245 列和感應器名稱結果 temperate 平均值大於 100。 此查詢會**dspl**，這是 30 秒**解決視窗**上方的感應器名稱] 中，依群組事件的資料流。 暫時查詢必須狀態如何我們想要進行的時間。 藉由使用**時間戳記 BY**子句，我們已指定**OUTPUTTIME**欄關聯所有暫時計算的次數。 詳細資訊，請參閱[管理時間](https://msdn.microsoft.com/library/azure/mt582045.aspx)及[視窗化函數](https://msdn.microsoft.com/library/azure/dn835019.aspx)的 MSDN 文章。

### <a name="query-detect-absence-of-events"></a>查詢︰ 偵測不存在的事件

我們可以撰寫查詢以尋找輸入事件缺乏？ 感應器傳送資料，然後再並未傳送事件的下一分鐘，讓我們來尋找最後一次。 查詢位於 AbsenseOfEvent.txt 檔案。

![偵測不存在的事件](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

以下我們使用相同的資料流 （自我聯結）**左外部**聯結。 **內部**聯結，只有在找到相符項目時，就會傳回一個結果。  **左外部**聯結，如果事件從左側的聯結是不相符，就會傳回的資料列的右側的所有資料行中有 NULL。 這項技巧是非常實用，若要尋找的事件不存在。 如需有關[加入](https://msdn.microsoft.com/library/azure/dn835026.aspx)我們 MSDN 文件，請參閱。

## <a name="conclusion"></a>結束時

此教學課程的目的，是將示範如何撰寫不同的資料流分析查詢語言查詢，請參閱在瀏覽器中的結果。 不過，這是才剛開始。 您可以執行遠較串流分析。 資料流分析支援各種不同的輸入與輸出及甚至可以使用函數中 Azure 電腦學習，使其強大的工具來分析資料流。 您可以開始瀏覽更多關於串流分析使用我們的[學習地圖](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/)。 如需有關如何撰寫查詢的詳細資訊，請閱讀關於[一般查詢模式](./stream-analytics-stream-analytics-query-patterns.md)的文件。
