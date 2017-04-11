<properties
    pageTitle="使用 Azure 資料流分析及 Azure 電腦學習舉動分析 |Microsoft Azure"
    description="如何使用中的資料流分析工作的使用者定義函數及電腦學習"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>


<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>使用 Azure 資料流分析及 Azure 電腦學習舉動分析 #

本文是設計用來協助您快速設定簡單的 Azure 資料流分析工作，Azure 電腦學習整合。 我們會從 Cortana 智慧庫使用舉動分析電腦學習模型，以分析串流文字資料，並決定即時舉動分數。 本文中的資訊可協助您瞭解例如串流 Twitter 資料的即時舉動分析的案例、 分析記錄客戶聊天與支援人員，以及評估論壇、 部落格和影片，除了許多其他即時、 預測計分案例的註解。

本文提供與文字的範例 CSV 檔案做為輸入中 Azure Blob 儲存體，如下圖所示。 工作適用於舉動分析模型使用者定義函數 (UDF) 上的範例文字資料從 blob 存放區。 最後的結果會放置在另一個 CSV 檔案中的相同 blob 存放區。 

![資料流分析電腦學習](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

下圖示範這項設定。 更多的真實案例中，您可以使用串流 Twitter 資料從 Azure 事件集線器輸入取代 Blob 儲存體。 此外，您可以建置彙總舉動[Microsoft Power BI](https://powerbi.microsoft.com/)即時視覺的效果。    

![資料流分析電腦學習](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>必要條件

完成工作所示範本文中的必要條件如下所示︰

-   作用中的 Azure 訂閱。
-   部分中的資料，以 CSV 檔案。 您可以下載圖 1 從[GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv)，顯示檔案或建立您自己的檔案。 在本文中，我們假設您使用所提供的 GitHub 上下載。

在高的層級，完成工作所示本文中，您會執行下列動作︰

1.  CSV 輸入的檔案上傳至 Azure Blob 儲存體。
2.  從 Cortana 智慧庫新增舉動分析資料模型，到 Azure 電腦學習工作區。
3.  部署這個模型為電腦學習工作區中的 web 服務。
4.  建立資料流分析工作的函數，來判斷文字輸入舉動呼叫此 web 服務。
5.  啟動資料流分析作業，並觀察輸出。

## <a name="upload-the-csv-input-file-to-blob-storage"></a>CSV 輸入的檔案上傳至 Blob 儲存體

此步驟中，您可以使用任何 CSV 檔案，例如已指定為可供下載 GitHub 上。 您可以使用[Azure 儲存檔案總管](http://storageexplorer.com/)] 或 [Visual Studio 上傳檔案，或者您可以使用自訂程式碼。 我們使用 Visual Studio 所根據的範例。

1.  在 Visual Studio 中，按一下 [ **Azure** > **儲存** > **附加外部儲存空間**。 請輸入**客戶名稱**] 和 [**帳戶金鑰]**。  

    ![資料流分析電腦學習，伺服器總管](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  展開您所附加在步驟 1 中儲存空間、 按一下 [**建立 Blob 容器**，然後輸入邏輯的名稱。 您建立的容器之後，開啟以檢視其內容。 （它將會是空的此時）。  

    ![資料流分析電腦學習，建立 blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  若要上傳到 CSV 檔案，按一下 [**上傳 Blob**，再按一下 [**從本機磁碟的檔案**。  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>從 Cortana 智慧庫新增舉動分析資料模型

1.  從 [Cortana] 智慧庫下載[預測舉動分析資料模型](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)。  
2.  在電腦學習 Studio 中，按一下 [ **Studio 中開啟**]。  

    ![串流分析電腦學習，開啟電腦學習 Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  移至工作區，請登入。 選取最適合您自己的位置的位置。
4.  按一下 [在頁面底部的 [**執行**]。  
5.  順利執行程序之後，請按一下 [**部署 Web 服務**。
6.  舉動分析模型已可供使用。 若要驗證，按一下 [**測試**] 按鈕，然後輸入文字，例如 「 我喜歡 Microsoft 」。 測試應傳回的結果如下︰

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![資料流分析電腦學習，分析資料](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

在 [**應用程式**] 欄中，按一下連結的**Excel 2010 或舊版的活頁簿**以取得 API 金鑰與您將需要更新版本設定資料流分析工作的 URL。 （這個步驟，才能只使用電腦學習模型從另一個 Azure 帳戶工作區。 本文假設這是大小寫，地址這種情況下）。  

請注意所下載的 Excel 檔案的 web 服務 URL] 與 [access 鍵，如下所示︰  

![資料流分析電腦學習，快速檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>建立使用電腦學習模型資料流分析工作

1.  移至[Azure 入口網站](https://manage.windowsazure.com)。  
2.  按一下 [**新** > **資料服務** > **串流分析** > **快速建立**。 輸入您的工作的名稱，在 [**工作名稱**、 輸入適當的地區的**區域**] 中的工作，然後選取帳戶**地區監控儲存**帳戶。    
3.  建立工作時，[**輸入**] 索引標籤後按一下 [**新增輸入**。  

    ![資料流分析電腦學習，新增電腦學習輸入](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  在 [**新增輸入**精靈的第一個頁面上，按一下 [**資料流**]，然後再按 [**下一步**。 在下一個頁面上，按一下**Blob 儲存體**輸入，然後按 [**下一步**。  
5.  在精靈的 [ **Blob 儲存設定**] 頁面上提供您先前定義當您上傳資料儲存體帳戶 blob 容器名稱。 按一下 [**下一步**]。 如**事件序列化格式**，請按一下 [ **CSV**]。 接受預設值**序列化設定**] 頁面的其餘部分。 按一下**[確定]**。  
6.  在 [**輸出**] 索引標籤中，按一下 [**新增成果**。  

    ![資料流分析電腦學習，新增輸出](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  按一下 [ **Blob 儲存體**]，然後輸入相同的參數，除了容器。 **輸入**的值已設定為從名為 「 測試 」 容器讀取**CSV**檔案上傳所在位置。 **輸出**中，輸入 「 testoutput 」。 容器名稱必須不同。 請確認此容器存在。     
8.  按一下 [**下一步**設定的輸出**序列化設定**。 就跟**輸入**，按一下**CSV**，，然後按一下**[確定**] 按鈕。
9.  在 [**函數**] 索引標籤中，按一下 [**新增電腦學習函數**。  

    ![資料流分析電腦學習，電腦自動學習函數](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. 在**電腦學習 Web 服務設定]**頁面上，找到電腦學習工作區、 web 服務及預設結束點。 在本文中，套用的設定，以手動方式，以熟悉設定 web 服務的任何工作區中，只要您知道的 URL，並且有 API 金鑰。 輸入端點**URL**和**API 金鑰**。 按一下**[確定]**。    

    ![資料流分析電腦學習，電腦學習 web 服務](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. 在 [**查詢**] 索引標籤上，如下所示修改查詢︰    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. 按一下 [儲存查詢的 [**儲存**]。

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>啟動資料流分析作業，並觀察輸出

1.  按一下 [在 [工作] 頁面底部的 [**開始**]。
2.  在**啟動查詢] 對話方塊**中，按一下 [**自訂時間**，，然後選取前的一次您上傳至 Blob 儲存體的 CSV。 按一下**[確定]**。  

    ![資料流分析電腦學習、 自訂的時間](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  使用您用來上傳 CSV 檔案，例如 Visual Studio 的工具，請移至 Blob 儲存體。
4.  幾分鐘後工作開始時，輸出容器會建立，並以 CSV 檔案上傳到它。  
5.  在預設 CSV 編輯器中開啟的檔案。 應該顯示類似下列內容︰  

    ![資料流分析電腦學習、 CSV 檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>結論

本文將示範如何建立資料流分析工作的讀取串流文字資料，並套用舉動分析即時資料。 您可以完成所有目標，並不需要擔心複雜的建置舉動分析資料模型。 這是 Cortana 智慧套件的優點之一。

您也可以檢視 Azure 電腦自動學習函數相關指標。 若要這麼做，請按一下 [**監視器**] 索引標籤。 顯示三個函數相關的指標。  

- **函數要求**指示傳送到電腦學習 web 服務的要求的號碼。  
- **函數的事件**會指出在邀請中的事件的數目。 根據預設，每個電腦學習 web 服務的要求包含 1000 個事件。  
- **無法函數要求**指示電腦學習 web 服務失敗要求的數目。  

    ![資料流分析電腦學習，電腦學習監視器檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
