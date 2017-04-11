<properties
    pageTitle="以程式設計方式重新訓練電腦學習模型 |Microsoft Azure"
    description="瞭解如何以程式設計方式重新訓練模型及更新 web 服務中 Azure 電腦學習使用新訓練的模型。"
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>以程式設計方式重新訓練電腦學習模型  

在此逐步解說，您將學習如何以程式設計方式重新訓練使用 C# 及電腦學習批次執行服務 Azure 電腦學習 Web 服務。

一旦您有重新培訓模型，下列逐步解說會顯示如何更新您的預測 web 服務中的模型︰

- 如果您部署傳統 web 服務機器學習 Web 服務入口網站中的，請參閱[重新訓練傳統 web 服務](machine-learning-retrain-a-classic-web-service.md)。 
- 如果您部署新的 web 服務時，請參閱[重新訓練新的 web 服務使用的電腦學習管理指令程式](machine-learning-retrain-new-web-service-using-powershell.md)。

如需訓練的程序的概觀，請參閱[重新訓練電腦學習模型](machine-learning-retrain-machine-learning-model.md)。

如果您想要開始您現有的基礎的新 Azure 資源管理員 web 服務，請參閱[重新訓練現有的預測 web 服務](machine-learning-retrain-existing-resource-manager-based-web-service.md)。

## <a name="create-a-training-experiment"></a>建立訓練實驗
 
例如，您會使用 「 範例 5︰ 訓練，測試，評估為二進位類別︰ 限制級的資料集 」 從 Microsoft Azure 電腦學習範例。 
    
若要建立實驗︰

1.  登入至 Microsoft Azure 電腦學習 Studio。 
2.  在右下角的儀表板，按一下 [**新增]**。
3.  從 Microsoft 範例中，選取 [範例 5]。
4.  若要重新命名項實驗中，按一下頂端的實驗畫布中，選取 [實驗名稱 」 範例 5︰ 訓練，測試，評估為二進位類別︰ 限制級的資料集]。
5.  輸入人口普查模型。
6.  底部的實驗畫布，按一下 [**執行**]。
7.  按一下 [ **web 服務設定**，然後選取**Retraining web 服務**。 

    ![初始的實驗。][2]

圖 2︰ 初始實驗。

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>建立預測的實驗及發佈為 web 服務  

接下來，您會建立 Predicative 實驗。

1.  在實驗畫布底部，按一下 [**設定 Web 服務**並選取**預測 Web 服務**。 這會儲存為訓練模式的模型，並新增 web 服務的輸入與輸出模組。 
2.  按一下 [**執行**]。 
3.  實驗執行完畢後，按一下 [**部署 Web 服務 [傳統]**或**[新增] 部署 Web 服務**]。

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>部署訓練實驗的訓練課程 web 服務

若要重新訓練訓練的模型，您必須部署訓練實驗 Retraining web 服務為您建立的。 這個 web 服務需要連線至*Web 服務輸出*模組*[訓練模型][train-model]*模組，能夠以產生新的訓練的模型。

1. 若要返回訓練實驗，請按一下左窗格中，實驗圖示，然後按一下 [實驗名為 「 人口普查模型。  
2. 在 [搜尋實驗項目搜尋] 方塊中，輸入 web 服務。 
3. 實驗畫布上拖曳*Web 服務輸入*模組，並將輸出連線到*Clean 遺失資料*模組。  如此一來，可確保訓練資料處理您的原始訓練資料相同的方式。
4. 實驗畫布上拖曳兩個*web 服務輸出*模組。 項目及其他*評估模型*模組的輸出連線*訓練模型*模組的輸出。 **訓練模型**的 web 服務輸出會提供我們新訓練的模型。 附加至**評估模型**的輸出傳回該模組輸出，也就是效能結果。
5. 按一下 [**執行**]。 

接下來，您必須為 web 服務產生的訓練的模型和模型評估結果的部署訓練實驗。 若要這麼做，您下一組動作是依據您使用傳統 web 服務的新的 web 服務。  
  
**傳統的 web 服務**

在實驗畫布底部，按一下 [**設定 Web 服務**並選取**部署 Web 服務 [傳統]**。 批次執行 Web 服務**儀表板**顯示 API 金鑰與 API 說明頁面。 批次執行方法可以用來建立訓練的模型。

**新的 web 服務**

在實驗畫布底部，按一下 [**設定 Web 服務**，然後選取**[新增] 部署 Web 服務**。 Web 服務 Azure 機器學習 Web 服務入口網站會開啟 [部署 web 服務] 頁面。 輸入您的 web 服務的名稱並選擇付款計劃，然後按一下 [**部署**。 批次執行方法可以用來建立訓練模型

一種情況，實驗完成執行後, 產生的工作流程應該看起來，如下所示︰

![結果工作流程後執行。][4]

圖 3︰ 結果後執行的工作流程。

## <a name="retrain-the-model-with-new-data-using-bes"></a>重新模型訓練以新的資料使用 BES

例如，您使用 C# 建立訓練應用程式。 您也可以使用 Python 或 R 範例程式碼，完成這項工作。

若要將來電重新訓練 Api:

1. 在 Visual Studio 建立 C# 主控台應用程式 (新專案]-> [Windows]-> [桌面]-> [主控台應用程式)。
2.  登入電腦學習 Web 服務入口網站。
3.  如果您正在使用傳統 web 服務，請按一下 [**傳統的 Web 服務**]。
    1.  按一下您正在使用的 web 服務。
    2.  按一下 [預設結束點]。
    3.  按一下 [**取用**]。
    4.  在頁面底部的 [**消耗**，在 [**範例**] 區段中，按一下 [**批次**]。
    5.  繼續執行本程序的步驟 5。
4.  如果您正在使用新的 web 服務，請按一下 [ **Web 服務**]。
    1.  按一下您正在使用的 web 服務。
    2.  按一下 [**取用**]。
    3.  在頁面底部的 [消耗，在 [**範例**] 區段中，按一下 [**批次**]。
5.  複製批次執行的範例 C# 程式碼，並將它貼到 Program.cs 檔案，並確認其命名空間保持不變。

新增註解中所指定的 Nuget 封裝 Microsoft.AspNet.WebApi.Client。 若要新增 Microsoft.WindowsAzure.Storage.dll 的參照，您可能必須安裝 Microsoft Azure 儲存服務的用戶端文件庫。 如需詳細資訊，請參閱[Windows 儲存服務](https://www.nuget.org/packages/WindowsAzure.Storage)。

### <a name="update-the-apikey-declaration"></a>更新 apikey 宣告

找出**apikey**宣告。

    const string apiKey = "abc123"; // Replace this with the API key for the web service

在 [**消耗**頁面的 [**基本消耗資訊**] 區段中，找出主索引鍵並將其複製到**apikey**宣告。

### <a name="update-the-azure-storage-information"></a>更新 Azure 儲存體資訊

BES 碼範例上傳至 Azure 儲存體從本機磁碟機 （例如 「 C:\temp\CensusIpnput.csv 」） 檔案、 程序，並將結果寫回到 Azure 儲存體。  

若要完成此工作，您必須擷取儲存體帳戶名稱、 金鑰]，然後容器資訊儲存帳戶傳統 Azure 入口網站與程式碼中的更新對應值。 

1. 傳統 Azure 入口網站登入。
1. 在左側導覽畫面中的欄中，按一下 [**儲存**]。
1. 從儲存的帳戶清單中，選取其中一個 retrained 的模型儲存。
1. 在頁面底部，按一下 [**管理便捷鍵**。
1. 複製儲存**Access 的主索引鍵**，並關閉對話方塊。 
1. 在頁面頂端，按一下 [**容器**]。
1. 選取現有的容器或建立新的項目並儲存名稱。

尋找*StorageAccountName* *StorageAccountKey*，與*StorageContainerName*宣告及更新您儲存從 Azure 入口網站中的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
您也必須確定輸入的檔案位於您指定在程式碼的位置。 

### <a name="specify-the-output-location"></a>指定輸出位置

指定時輸出位置中要求的內容，必須指定*RelativeLocation*中指定的檔案的副檔名為 ilearner。 

下列範例，請參閱︰

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] 您的輸出位置名稱可能會不同於在此逐步解說，根據您加入 web 服務輸出模組的順序。 由於您設定兩個輸出此訓練實驗，結果會包含兩者都是存放位置資訊。  

![訓練輸出][6]

圖 4︰ 重新訓練輸出。

## <a name="evaluate-the-retraining-results"></a>評估訓練的結果
 
當您執行應用程式時，包括存取評估結果所需的 URL] 與 [SA token。

您可以結合*BaseLocation*、 *RelativeLocation*和*SasBlobToken*從*output2*的輸出結果 （如訓練輸出圖所示），並貼在瀏覽器網址列中的完整的 URL，請參閱 retrained 模型的效能結果。  

檢查，判斷是否新訓練的模型會執行清楚取代現有的結果。

複製*BaseLocation* *RelativeLocation*，與*SasBlobToken*輸出結果，您會使用這些訓練的程序期間。

## <a name="next-steps"></a>後續步驟

如果您是**部署 Web 服務 [傳統]**，即可部署預測 web 服務，請參閱[重新訓練傳統 web 服務](machine-learning-retrain-a-classic-web-service.md)。

如果您按一下**[新增] 部署 Web 服務**部署預測 web 服務，請參閱[重新訓練新的 web 服務使用的電腦學習管理指令程式](machine-learning-retrain-new-web-service-using-powershell.md)。

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/