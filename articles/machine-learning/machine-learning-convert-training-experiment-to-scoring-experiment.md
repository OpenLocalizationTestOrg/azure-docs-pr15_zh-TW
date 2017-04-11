<properties
    pageTitle="將電腦學習訓練實驗轉換成預測的實驗 |Microsoft Azure"
    description="如何將電腦學習訓練實驗，用於訓練您的預測狀況分析模型，可以部署 web 服務為預測實驗，轉換。"
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
    ms.date="08/19/2016"
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>將電腦學習訓練實驗轉換成預測的實驗

Azure 電腦學習可讓您建立、 測試和部署預測分析的解決方案。

一旦您建立及上*訓練嘗試*訓練預測分析資料模型，逐一查看並您準備好要用來分數新資料，您需要進行準備及簡化計分實驗。 您可以部署這個*預測實驗*Azure web 服務為，好讓使用者可以傳送資料至您的模型和收到您的模型預測。

將轉換為預測的實驗，您收到訓練的模型準備好要部署為 web 服務。 Web 服務的使用者會傳送給您的模型的輸入的資料和模型會傳送回預測結果。 因此當您將轉換成預測的實驗會想要請記住您希望您的模型，供其他人如何。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

將訓練實驗轉換成預測的實驗的程序包含三個步驟︰

1.  儲存您已在訓練，電腦學習模型，然後取代電腦學習演算法和[訓練模型][train-model]模組中使用您儲存的訓練模型。
2.  修剪實驗只有在所需的計分的模組。 訓練課程實驗包含模組所需的訓練課程，但不是需要的訓練，並準備好使用計分模型後的數的字。
3.  定義位置實驗中您要接受來自 web 服務使用者資料和資料將會傳回。

## <a name="set-up-web-service-button"></a>設定 Web 服務] 按鈕

執行實驗 （**執行**] 按鈕實驗畫布底部） 之後，**設定 Web 服務**] 按鈕 （選取 [**預測 Web 服務**選項） 會為您將訓練實驗轉換成預測的實驗的三個步驟︰

1.  它會將儲存訓練的模型，如模組**訓練模型**的區段中模組調色盤 （實驗畫布中的左邊），然後會取代電腦學習演算法和[訓練模型][train-model]儲存訓練模型的模組。
2.  它會移除清楚不需要的模組。 在我們的範例，其中包括[分割的資料][split]，第 2[分數模型][score-model]，以及[評估模型][evaluate-model]模組。
3.  它會建立 Web 服務輸入與輸出模組，並將它們新增在實驗中的預設位置。

例如，以下試驗訓練兩個類別提高決策樹模型使用範例人口普查資料︰

![訓練課程實驗][figure1]

在這項實驗模組執行基本上四個不同的功能︰

![模組函數][figure2]

當您將這個訓練實驗轉換預測的實驗時，這些模組部分不再需要或有不同的用途︰

- **資料**在此範例資料集中的資料不會使用期間計分-web 服務的使用者會提供資料以為分數。 不過，從這個資料集，例如資料類型的中繼資料會使用訓練的模型。 因此，您需要將資料集預測實驗中，讓它可以提供此中繼資料。

- **準備**-根據資料要送計分，這些模組可能，或可能需要處理的內送的資料。

    舉例來說，在此範例中的範例資料集可能遺失的值，其包含訓練模型不需要的欄。 因此[Clean 遺失資料][clean-missing-data]模組已處理遺漏的值，並[在資料集中的 [選取資料行]包含[select-columns]模組所包含的資料流中排除這些額外的資料行。 如果您知道會送出到 web 服務計分的資料不會遺失的值，然後您可以移除[Clean 遺失資料][clean-missing-data]模組。 不過，自[資料集中的 [選取資料行][select-columns]模組協助定義被計分的功能一組，需要保持該模組。

- **訓練**-模型具有訓練成功後，您將其儲存為單一訓練的模型模組。 然後，您會取代這些個別模組儲存訓練模型。

- **成績**-在此範例中，[分割] 模組用來將資料流分割成一組測試資料與訓練資料。 在預測實驗這，不需要您可以移除]。 同樣地，第 2[分數模型][score-model]模組和[評估模型][evaluate-model]模組用來比較結果來自測試資料，讓這些模組也不需要在預測實驗中。 剩餘[的成績模型][score-model]模組，然而，需要傳回透過 web 服務為分數結果。

以下是我們的範例按一下 [**設定 Web 服務**後的外觀︰

![已轉換的預測實驗][figure3]

這可能是足以準備要部署 web 服務為實驗。 不過，您可能會想執行一些額外的實驗的特定工作。

### <a name="adjust-input-and-output-modules"></a>調整輸入與輸出模組

在您訓練項實驗中，您可以使用一組訓練資料，且然後並未取得資料的表單，電腦學習演算法需要一些處理。 如果您希望收到透過 web 服務的資料不會需要此處理，您可以移至不同的節點的**Web 服務輸入的模組**實驗中。

例如，預設**設定 Web 服務**將頂端的資料流程，如圖所示的**Web 服務輸入**模組。 不過，如果輸入的資料不會需要此處理，然後您可以手動將過去的資料處理模組**Web 服務輸入**︰

![移動 web 服務輸入][figure4]

不含任何前置處理，將會立即透過 web 服務所提供的輸入的資料通過直接將分數模型模組。

同樣地，依預設**設定 Web 服務**將資料流程底部的 Web 服務輸出模組。 在此範例中，web 服務會傳回給使用者的[分數模型]輸出[score-model]模組，包括完成輸入的資料向量以及計分結果。

不過，如果您想要傳回不同的例如，計分的結果和不整個向量輸入的資料集，然後您可以插入[資料集中的 [選取資料行][select-columns]排除計分結果以外的所有欄的模組。 然後將**Web 服務輸出**模組移到[資料集中的 [選取資料行]的輸出[select-columns]模組︰

![移動 web 服務輸出][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>新增或移除其他資料處理模組

如果您知道不需要期間計分實驗中有多個模組，這些是可以移除。 例如，因為我們已移動至點的**Web 服務輸入**模組資料處理模組後，我們可以移除[Clean 遺失資料][clean-missing-data]從預測實驗的模組。

我們預測實驗現在看起來像這樣︰

![移除額外的模組][figure6]

### <a name="add-optional-web-service-parameters"></a>新增 Web 服務的選擇性參數

在某些情況下，您可能會想要允許您的 web 服務的使用者存取服務時，變更的模組行為。 *Web 服務參數*可讓您執行此動作。

常見的範例設定[匯入資料][import-data]模組，讓部署的 web 服務的使用者存取 web 服務時，指定不同的資料來源。 設定[匯出資料]或者[export-data]模組，讓您可以指定不同的目的地。

您可以定義 Web 服務參數，並關聯到一或多個模組參數，而且您可以指定是否有必要或選用。 Web 服務的使用者可以再提供值這些參數時存取服務和模組動作會隨之修改。

如需 Web 服務參數的詳細資訊，請參閱[使用 Azure 電腦學習 Web 服務參數] [ webserviceparameters]。

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>部署預測實驗的 web 服務

現在，充分準備預測的實驗，可以將其部署 Azure web 服務。 使用 web 服務，使用者可以將資料傳送給您的模型，並且模型會傳回預測。

如需完成的部署程序的詳細資訊，請參閱[部署 Azure 電腦學習 web 服務][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
