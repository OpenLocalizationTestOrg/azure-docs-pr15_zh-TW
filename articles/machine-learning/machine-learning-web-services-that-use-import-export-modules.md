<properties
    pageTitle="部署 Azure 毫升 web 服務的資料匯入及匯出資料的模組 |Microsoft Azure"
    description="瞭解如何傳送和接收來自 web 服務的資料使用的資料匯入與匯出資料的模組。"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>使用資料匯入及匯出資料的模組的部署 Azure 毫升 web 服務 

當您建立預測的實驗時，通常是新增 web 服務輸入與輸出。 當您部署實驗時，消費者可以傳送及接收到的輸入與輸出的 web 服務的資料。 某些應用程式，消費者的資料可能可以從資料摘要或已存在於的外部資料來源，例如 Azure Blob 儲存體。 在下列情況下，不需要讀取和寫入使用 web 服務的輸入與輸出的資料。 不過，他們可以使用批次執行服務 (BES) 資料來源的資料使用匯入資料模組和寫入使用匯出資料模組不同的資料位置計分的結果。

資料匯入與匯出資料模組，可以從讀取和寫入數字的資料提供的位置，例如透過 HTTP 登錄區查詢、 Azure SQL 資料庫、 Azure 資料表儲存體、 Azure Blob 儲存體，資料摘要的網址或將內部部署的 SQL 資料庫。

本主題會使用 「 範例 5︰ 訓練，測試，評估為二進位類別︰ 限制級的資料集 」 範例，並假設資料集已載入 censusdata SQL Azure 資料表。

## <a name="create-the-training-experiment"></a>建立訓練實驗 
 
當您開啟 「 範例 5︰ 訓練，測試，評估為二進位類別︰ 限制級的資料集 」，它會使用範例限制級人口普查收入二進位分類資料集的範例。 然後實驗畫布上的看起來類似下列圖像。

![初始的實驗的設定。](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

閱讀從 SQL Azure 資料表的資料︰

1.  刪除資料集模組。
2.  在 [元件] 搜尋方塊中，輸入匯入。
3.  [結果] 清單中，實驗畫布上新增*匯入資料*模組。
4.  連線*資料匯入*模組*Clean 缺少的資料*在模組的輸入的輸出。
5.  在 [屬性] 窗格中，選取 [**資料來源**] 下拉式清單中**Azure SQL 資料庫**。
6.  在 [**資料庫伺服器名稱****的資料庫名稱**、**使用者名稱**及**密碼**] 欄位中，輸入資料庫的適當的資訊。
7.  在 [資料庫查詢] 欄位中，輸入下列查詢。

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  底部的實驗畫布，按一下 [**執行**]。

## <a name="create-the-predictive-experiment"></a>建立預測實驗

下一步您設定預測實驗的部署 web 服務。

1.  在實驗畫布底部，按一下 [**設定 Web 服務**並選取**預測 Web 服務 [建議]**。
2.  移除預測實驗的*Web 服務輸入*資訊和*Web 服務輸出模組*。 
3.  在 [元件] 搜尋方塊中，輸入匯出。
4.  [結果] 清單中，實驗畫布上新增*匯出資料*模組。
5.  連線*分數模型*模組*匯出資料*在模組的輸入的輸出。 
6.  在 [屬性] 窗格中，選取 [ **Azure SQL 資料庫**資料目的地下拉式清單中。
7.  在 [**資料庫伺服器名稱****資料庫名稱**、**伺服器的使用者帳戶名稱**和**伺服器的使用者帳戶密碼**] 欄位中，輸入資料庫的適當的資訊。
8.  在 [**要儲存的資料行清單以逗號分隔**] 欄位中，輸入分數標籤。
9.  在**資料表格名稱] 欄位中**，輸入 dbo。ScoredLabels。 如果資料表不存在，則會建立時執行實驗或 web 服務要求。
10. 在**資料表的資料行清單以逗號分隔**] 欄位中，輸入 ScoredLabels。

當您撰寫的通話的最後一個 web 服務的應用程式時，您可能會想要在執行階段指定不同的輸入的查詢或目的資料表。 若要設定這些輸入與輸出，您可以使用 Web 服務參數功能來設定 [*匯入資料*模組*資料來源*] 屬性] 和 [*匯出資料*模式資料的目的地屬性。  如需 Web 服務參數的詳細資訊，請參閱[AzureML Web 服務參數的項目](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/)上的 Cortana 智慧及電腦學習部落格。

若要設定匯入查詢和目的資料表的 Web 服務參數︰

1.  在 [*匯入資料*模組的 [屬性] 窗格中，按一下 [頂端圖示右上角**資料庫查詢**欄位選取**設成 web 服務參數**。
2.  在 [*匯出資料*模組的 [屬性] 窗格中，按一下 [頂端圖示右上角**資料表格名稱**] 欄位選取**設成 web 服務參數**。
3.  底部的 [*匯出資料*模組屬性窗格中，在 [ **Web 服務參數**] 區段中，按一下 [資料庫查詢，然後將其重新命名查詢。
4.  按一下 [**資料資料表的名稱**，然後將其重新命名**資料表**。

當您完成時，實驗應該看起來類似下列圖像。
 
![最終的實驗的外觀。](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

現在您可以部署實驗的 web 服務。

## <a name="deploy-the-web-service"></a>部署 web 服務 
您可以部署至傳統或新的 web 服務。

### <a name="deploy-a-classic-web-service"></a>部署傳統的 Web 服務

若要部署為傳統的 Web 服務，並建立使用該應用程式︰

1.  在實驗畫布底部，按一下 [執行]。
2.  當執行完成時，請按一下 [**部署 Web 服務**，然後選取**部署 Web 服務 [傳統]**。
3.  在 web 服務儀表板，找出您的 API 金鑰。 複製並儲存，以供日後使用。
4.  在 [**預設的結束點**] 資料表中，按一下**批次執行**連結来開啟 API 的 [說明] 頁面。
5.  在 Visual Studio 中，建立 C# 主控台應用程式。
6.  在 API 說明頁面上，尋找 [**範例程式碼**區段底部的頁面。
7.  複製並貼上到 Program.cs 檔案的 C# 程式碼範例，移除所有參考 blob 儲存體。
8.  使用先前儲存的 API 金鑰來更新*apiKey*變數的值。
9.  找出要求宣告及更新 Web 服務的參數傳遞給*資料匯入*與*匯出資料*模組的值。 在此情況下，您會使用原始的查詢，但定義新的表格名稱。

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. 執行應用程式。 

執行完成時，新的資料表會新增至包含計分結果的資料庫。

### <a name="deploy-a-new-web-service"></a>部署新的 Web 服務

若要做為新的 Web 服務部署，並建立使用該應用程式︰

1.  底部的實驗畫布，按一下 [**執行**]。
2.  當執行完成時，請按一下 [**部署 Web 服務**，然後選取**[新增] 部署 Web 服務**。
3.  在部署實驗頁面上，輸入您的 web 服務的名稱並選取一個價格的方案，然後按一下**部署**。
4.  在 [**快速入門**] 頁面上，按一下 [**消耗**]。
5.  在 [**範例**] 區段中，按一下 [**批次**。
6.  在 Visual Studio 中，建立 C# 主控台應用程式。
7.  複製並貼到 Program.cs 檔案的 C# 程式碼範例。
8.  [**基本消耗資訊**] 區段中的**主索引鍵**更新*apiKey*變數的值。
9.  找出*scoreRequest*宣告及更新 Web 服務的參數傳遞給*資料匯入*與*匯出資料*模組的值。 在此情況下，您會使用原始的查詢，但定義新的表格名稱。

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. 執行應用程式。 
 

