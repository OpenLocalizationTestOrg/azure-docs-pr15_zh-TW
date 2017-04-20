<properties
    pageTitle="部署機器學習 web 服務 |Microsoft Azure"
    description="如何將訓練實驗轉換成預測性實驗、 準備進行部署，然後部署為 Azure 機器學習 web 服務。"
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
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>

# <a name="deploy-an-azure-machine-learning-web-service"></a>部署 Azure 機器學習 web 服務

Azure 機器學習可讓您建立、 測試及部署預測性分析解決方案。

從高階點-的-檢視，這是三個步驟：

- **[建立訓練實驗]**Azure 機器學習 Studio 是您用以訓練及測試預測性分析模型使用您所提供的訓練資料共同作業的視覺開發環境。
- **[將它轉換成預測性實驗]**-一次您模型受過訓練與現有的資料與您準備好要用它來分數新資料、 準備及簡化的預測符合原先的假設。
- **將其部署為 web 服務**-您可以部署預測實驗做為[新]的或[傳統]Azure web 服務。 使用者可以將資料傳送到您的模型及接聽您的模型預測。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>建立訓練實驗

若要訓練預測性分析模型，您使用 Azure 機器學習 Studio 建立訓練實驗其中包含各種模組載入訓練資料、 準備視資料、 套用機器學習演算法，及評估結果。 您可以在符合原先的假設逐一查看並嘗試不同的機器學習演算法比較及評估結果。

建立及管理訓練實驗的程序被談到更徹底。 如需詳細資訊，請參閱下列文章：

- [在 Azure 機器學習 Studio 中建立簡易實驗](machine-learning-create-experiment.md)
- [開發與 Azure 機器學習預測性解決方案](machine-learning-walkthrough-develop-predictive-solution.md)
- [訓練資料匯入 Azure 機器學習 Studio](machine-learning-data-science-import-data.md)
- [管理 Azure 機器學習 studio 實驗重複項目](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>轉換成預測性實驗的訓練實驗

一旦您模型受過訓練您準備好將訓練實驗轉換成預測項實驗中分數新資料。

將轉換為預測性實驗，（英文） 受過訓練的模型準備好要部署為計分 web 服務。 Web 服務的使用者可以將輸入的資料傳送至您的模型及模型會傳送回預測結果。 當您將轉換成預測項實驗中，請記住您預期您要使用其他人的模型的方式。

若要可將訓練實驗轉換成預測項實驗中，按一下實驗畫布的底部的 [**執行**] 按一下 [**設定 Web 服務**]，然後再選取**預測的 Web 服務**。

![轉換成計分實驗](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

如需如何執行這項轉換的詳細資訊，請參閱[轉換至預測性實驗機器學習訓練實驗](machine-learning-convert-training-experiment-to-scoring-experiment.md)。

下列步驟說明部署預測項實驗中做為新的 web 服務。 您也可以部署符合原先的假設為傳統 web 服務。

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>部署預測符合原先的假設為新的 web 服務

既然預測符合原先的假設已準備，可以將其部署為 Azure web 服務。 使用 web 服務，使用者可以將資料傳送至您的模型及模型會傳回其預測。

若要部署預測符合原先的假設，按一下 [實驗畫布的底部的 [**執行**]。 一旦完成執行後項實驗中，按一下 [**部署 Web 服務**並選取 [**部署 Web 服務[新增]**。  機器學習 Web 服務入口網站的 [部署] 頁面隨即開啟。

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>機器學習 Web 服務入口網站部署符合原先的假設頁面

在 [部署實驗] 頁面上輸入 web 服務的名稱。
選取定價計劃。 如果您有現有的定價計劃可以選取，否則您必須建立服務的新價格計劃。

1.  **價格計劃**中向下放下、 選取現有的計劃或選取 [**選取新計劃**] 選項。
2.  在**計劃名稱**] 中輸入會識別您 bill 在計劃的名稱。
3.  選取一個或**每月的規劃層**。 計劃層預設的預設地區和 web 服務的計劃来部署至該區域。

按一下 [**部署**] 和 [web 服務會開啟的 [**快速入門**] 頁面。

[Web 服務快速入門] 頁面可讓您存取及指導上將執行後建立 web 服務的最常見工作。 從這裡，您可以輕鬆地存取的測試頁面和消耗] 頁面。

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>測試您的 web 服務

若要測試新的 web 服務，下方按一下 [**測試 web 服務**的一般工作。 在 [測試] 頁面上您可以測試您的 web 服務以要求回應服務 (RR) 或批次執行服務 (BES)。

RR 測試 」 頁面會顯示輸入、 輸出以及任何您已定義的實驗的全域參數。 若要測試之 web 服務，您可以手動輸入的輸入適當的值或提供逗點分隔值 (CSV) 格式化的檔案包含測試值。

從清單中檢視模式使用 RR、 測試、 輸入輸入適當的值並按一下 [**測試要求回應**。 預測結果顯示在輸出欄左方。

![部署 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

若要測試您 BES，請按一下 [**批次**。 批次測試] 索引標籤上 [您的輸入下按一下 [瀏覽並選取 [包含適當的範例值的 CSV 檔案。 如果您沒有 CSV 檔案，而且您建立預測實驗使用機器學習 Studio，您可以下載的預測實驗的資料集並使用它。

若要下載資料集，開啟 [電腦學習 Studio]。 開啟您預測性實驗並以滑鼠右鍵按一下您實驗的輸入。 從 [內容] 功能表中選取**的資料集**，然後選取**下載**。

![部署 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

按一下 [**測試**]。 批次執行工作的狀態會顯示**測試批次工作**下右邊。

![部署 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

在 [**設定**] 頁面上您可以變更標題] 的描述、 更新儲存帳戶機碼，並讓您 web 服務範例資料。

![設定 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

一旦您已部署的 web 服務，您可以：

- **存取**其透過 web 服務 API。
- **管理**其透過 Azure 機器學習 web 服務入口網站或 Azure 傳統的入口網站。
- **更新**如果您的模型變更。

### <a name="access-the-web-service"></a>存取 web 服務

一旦您部署從機器學習 Studio 您 web 服務時，您可以將資料傳送到服務並以程式設計方式收到回應。

**消耗**頁面提供您需要存取 web 服務的所有資訊。 例如，API 機碼被提供給允許授權的服務權限。

如需存取機器學習 web 服務的詳細資訊，請參閱[如何使用已部署的 Azure 機器學習 web 服務](machine-learning-consume-web-services.md)。

### <a name="manage-your-new-web-service"></a>管理新的 web 服務

您可以管理傳統 web services 機器學習 Web 服務入口網站。 從[主要入口網站] 頁面上](https://services.azureml-test.net/)，按一下 [ **Web 服務**]。 從 web 服務] 頁面上，您可以刪除或複製一個服務。 若要檢視特定的服務，按一下 [服務] 和 [**儀表板**。 若要監視之 web 服務相關聯的批次工作，按一下 [**批次要求記錄檔**]。

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>部署預測符合原先的假設為傳統 web 服務

既然預測符合原先的假設已足以準備，可以將其部署為 Azure web 服務。 使用 web 服務，使用者可以將資料傳送至您的模型及模型會傳回其預測。

若要部署預測符合原先的假設，[實驗畫布的底部的 [**執行**和 [**部署 Web 服務**。 Web 服務設定，並會放在 web 服務儀表板。

![部署 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

您可以測試 web 服務中的電腦學習 Web 服務入口網站或機器學習 Studio。

若要測試之要求的回應 web 服務，請按一下 [web 服務儀表板中的 [**測試**] 按鈕。 若要要求您輸入資料服務彈出] 對話方塊。 這些是計分實驗所預期的資料行。 輸入的資料集，然後按一下 [**確定]**。 Web 服務所產生的結果會顯示在儀表板的底端。

您可以按一下 [**測試**預覽] 連結來顯示先前在 [新的 web 服務] 區段中，Azure 機器學習 Web 服務入口網站中測試您的服務。

若要測試批次執行服務，請按一下 [**測試**預覽] 連結。 批次測試] 索引標籤上 [您的輸入下按一下 [瀏覽並選取 CSV 檔案含有適當的範例值。 如果您沒有 CSV 檔案，而且您建立預測實驗使用機器學習 Studio，您可以下載的預測實驗的資料集並使用它。

![測試的 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

在 [**設定**] 頁面上您可以變更服務的顯示名稱並指定其描述。 供您管理 web 服務的[Azure 傳統入口網站](http://manage.windowsazure.com/)中顯示的名稱和描述。

您可以提供您輸入的資料、 輸出資料及 web 服務參數的說明每個資料欄底下**輸入結構描述**、**輸出結構描述**和**Web 服務參數**輸入一個字串。 Web 服務所提供的範例程式碼文件中使用這些描述。

您可以啟用診斷您看見存取 web 服務時任何失敗的記錄。 如需詳細資訊，請參閱[啟用機器學習 web 服務的記錄](machine-learning-web-services-logging.md)。

![設定 web 服務](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

您也可以在 Azure 機器學習 Web 服務入口網站類似顯示先前在 [新的 web 服務] 區段中的程序設定 web 服務端點。 是不同的選項，您可以新增或變更服務說明、 啟用記錄及啟用範例資料進行測試。

### <a name="access-the-web-service"></a>存取 web 服務

一旦您部署從機器學習 Studio 您 web 服務時，您可以將資料傳送到服務並以程式設計方式收到回應。

儀表板提供來存取 web 服務所需的所有資訊。 例如 API 金鑰提供給允許授權的存取服務，和 API 說明頁面提供可協助您開始撰寫程式碼。

如需存取機器學習 web 服務的詳細資訊，請參閱[如何使用已部署的 Azure 機器學習 web 服務](machine-learning-consume-web-services.md)。

### <a name="manage-the-web-service"></a>管理 web 服務

有各種您可執行的動作來監視 web 服務。 您可以更新，並將其刪除。 您也可以新增其他端點除了當您部署時所建立的預設端點傳統 web 服務。

如需詳細資訊，請參閱[管理 Azure 機器學習工作區](machine-learning-manage-workspace.md)] 及 [[管理 web 服務使用 Azure 機器學習 Web 服務入口網站](machine-learning-manage-new-webservice.md)。

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>更新 web 服務

您可以對您的 web 服務、 例如更新其他訓練資料模型進行變更及部署再試一次，覆寫原始 web 服務。

若要更新 web 服務，開啟您用來部署 web 服務並依序按一下 [ **SAVE AS (英文)**進行編輯的副本原始預測實驗。 進行變更，然後按一下 [**部署 Web 服務**。

因為您已部署了這項實驗中的之前，系統會要求您是否要覆寫 （傳統 Web 服務） 或更新現有服務 （新的 web 服務）。 按一下**[是]**或 [**更新**停止現有的 web 服務和部署新預測符合原先的假設部署在其位置。

> [AZURE.NOTE] 如果您在原始的 web 服務進行組態變更，例如輸入一個新的顯示名稱或描述] 中，您將需要再次輸入那些值。

更新 web 服務的一個選項是以程式設計方式重新訓練模型。 如需詳細資訊，請參閱[重新訓練機器學習模型以程式設計方式](machine-learning-retrain-models-programmatically.md)。


<!-- internal links -->
[建立訓練實驗]: #create-a-training-experiment
[轉換成預測性實驗]: #convert-the-training-experiment-to-a-predictive-experiment
[新]: #deploy-the-predictive-experiment-as-a-new-Web-service
[傳統]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
