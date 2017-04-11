<properties 
    pageTitle="什麼是 Azure 電腦學習 Studio？ |Microsoft Azure"
    description="Azure 毫升 Studio，快速建立模型從演算法和模組準備使用文件庫的拖放工具的概觀。"
    keywords="azure 電腦學習，azure 毫升，毫升 studio"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>什麼是 Azure 電腦學習 Studio？

Microsoft Azure 電腦學習 Studio 是共同作業、 拖放的工具，您可以使用來建立、 測試和部署預測狀況分析資料的解決方案。 電腦學習 Studio 發佈為可以輕鬆地使用自訂的應用程式或 Excel 等的 BI 工具的 web 服務的模型。

電腦學習 Studio 是符合資料科學、 預測分析、 雲端的資源及您的資料。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>電腦學習 Studio 互動式工作區

若要開發預測分析模型，您通常是使用一或多個來源的資料、 轉換與分析透過各種不同的資料管理和統計函數，該資料，產生一組結果。 開發像這樣的模型是反覆運算的程序。 修改各種函數和其參數時，結果減少鍵，直到您滿意您有訓練、 有效的模型。

**Azure 電腦學習 Studio**可讓您輕鬆建立、 測試和持續預測分析模型互動式、 生動的工作區。 您拖放***資料集***和分析***模組***到互動式畫布中，以表單***嘗試***，您在電腦學習 Studio 中執行的連線。 若要持續模型設計，您可以編輯實驗、 儲存複本，並執行一次。 您準備好時，您可以轉換程式的***訓練嘗試******預測實驗***中，然後將它發佈為***web 服務***，讓其他人可以存取您的模型。

>[AZURE.TIP] 若要下載及列印的圖表，可讓電腦學習 Studio 的功能的概觀，請參閱[Azure 電腦學習 Studio 功能的概觀圖表](machine-learning-studio-overview-diagram.md)。

有必要，請只以視覺化方式連線資料集和模組，以建構預測分析模型沒有程式設計。

![Azure 毫升 Studio 圖表︰ 建立實驗閱讀各種來源的資料、 撰寫計分資料、 撰寫模型。][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>開始使用電腦學習 Studio

當您第一次輸入[電腦學習 Studio](https://studio.azureml.net)您會看到 [**首頁**] 頁面。 從這裡開始，您可以檢視文件、 視訊、 網路研討會，並尋找其他實用的資源。

有三個索引標籤上方︰**家用版**（您開始） **Studio**及**圖庫**。

### <a name="studio"></a>Studio

按一下 [ **Studio** ] 索引標籤，將會要求您使用您的 Microsoft 帳戶或您的公司或學校帳戶登入]。 登入之後，您會看到下列索引標籤左邊︰

- **專案**的實驗、 資料集，筆記本，與其他資源代表單一專案的集合
- **實驗**實驗的已建立、 執行及儲存為草稿
- **WEB 服務**-從您的實驗部署您的 Web 服務
- **筆記本**您所建立的 Jupyter 筆記本
- **資料集**您上傳到 Studio 的資料集
- **訓練模型**訓練實驗中，在 Studio 中儲存模型
- **設定**-設定，您可以用來設定您的帳戶和資源的集合。

### <a name="gallery"></a>圖庫

按一下 [**組件庫**] 索引標籤，您會被重新導向至 Cortana 智慧庫]。 [圖庫是位置的資料科學家及開發人員社群可以共用使用 Cortana 智慧套件的元件所建立的解決方案的位置。

如需有關圖庫的詳細資訊，請參閱[共用及探索 Cortana 智慧圖庫中的解決方案](machine-learning-gallery-how-to-use-contribute-publish.md)。

## <a name="components-of-an-experiment"></a>實驗的元件

實驗組成，可提供資料給分析模組，連接在一起以建構預測分析模型資料集。 具體來說，有效的實驗有下列特性︰

- 實驗具有至少有一個資料集和模組
- 資料集可能僅能連線至模組
- 模組可能連線到資料集或其他模組
- 必須資料流程一些連線模組的所有輸入的連接埠。
- 所有需要的每一個模組參數必須設定

您可以建立實驗中從頭開始，或者您可以使用現有的範例實驗另存為範本。 如需詳細資訊，請參閱[使用範例實驗來建立新的實驗](machine-learning-sample-experiments.md)。

如需建立簡單的實驗的範例，請參閱[建立簡單的實驗 Azure 電腦學習 Studio 中](machine-learning-create-experiment.md)。

建立預測分析解決方案的更完整的逐步解說，請參閱[開發 Azure 電腦學習的預測方案](machine-learning-walkthrough-develop-predictive-solution.md)。

### <a name="datasets"></a>資料集

具有已上傳至電腦學習 Studio 這樣就可以使用模型程序中的資料，則資料集。 範例資料集的數字隨附於電腦學習 Studio 您嘗試使用，而且您可以上傳多個資料集，視需要。 以下是包含的資料集的部分範例︰

- **各種汽車 MPG 資料**-加侖 (MPG) 值以柱、 威力等的數字區分的汽車每英哩。
- **Breast cancer 資料**-Breast cancer 診斷資料。
- **樹系引發資料**-東北葡萄牙樹系 fire 大小。

當您建立的實驗，您可以從清單中選擇可用的資料集的左邊的畫布。

在電腦學習 Studio 中所包含的範例資料集的清單，請參閱[使用 Azure 電腦學習 Studio 中範例資料集](machine-learning-use-sample-datasets.md)。

### <a name="modules"></a>模組

模組是演算法，您可以執行您的資料。 電腦學習 Studio 中有訓練、 分數，以及驗證程序範圍是從資料輸入函數的模組的數字。 以下是包含模組的一些範例︰

- [轉換成 ARFF] [ convert-to-arff] -將序列化的.NET 資料集屬性關聯的檔案格式 (ARFF)。
- [計算國小統計資料][ elementary-statistics] -計算平均值、 標準差等國小統計資料。
- [線性迴歸][ linear-regression] -建立線上漸層深度型線性迴歸模型。
- [分數模型][ score-model] -得到訓練的分類或迴歸模型。

當您建立的實驗，您可以從清單中選擇可用模組左邊的畫布。  

模組可能會有一組可用來設定模組的內部演算法的參數。 當您選取畫布上模組時，模組的參數會顯示在右側的畫布上的 [**屬性**] 窗格中。 您可以修改微調模型的窗格中的參數。

某些說明瀏覽可用的電腦學習演算法的大型文件庫，請參閱[如何選擇 Microsoft Azure 電腦學習的演算法](machine-learning-algorithm-choice.md)。

## <a name="deploying-a-predictive-analytics-web-service"></a>部署預測分析 web 服務

預測分析資料模型後，您可以將其部署為直接從電腦學習 Studio web 服務。 如需此程序的詳細資訊，請參閱[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
