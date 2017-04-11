<properties 
    pageTitle="為您的模型中 Azure 機器學習偵錯 |Microsoft Azure" 
    description="說明如何偵錯模型中 Azure 電腦學習如何。" 
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
    ms.date="09/09/2016" 
    ms.author="bradsev;garye" />

# <a name="debug-your-model-in-azure-machine-learning"></a>為您的模型中 Azure 機器學習偵錯

本文說明如何為您的 Microsoft Azure 電腦學習模型偵錯。 具體來說，其涵蓋為什麼下列兩個失敗案例可能發生時執行模型的可能原因︰

* [訓練模型][train-model]模組擲回錯誤 
* [成績模型][score-model]模組會產生不正確的結果 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-throws-an-error"></a>訓練模型模組擲回錯誤

![image1](./media/machine-learning-debug-models/train_model-1.png)

[訓練模型][train-model]模組需要下列 2 輸入︰

1. 從 Azure 電腦學習所提供的模型的集合類型的分類/迴歸分析模型
2. 訓練資料與指定的 [標籤] 資料行。 [標籤] 資料行指定預測的變數。 包含的資料行的其餘部分均功能。

本單元擲回一個錯誤，在下列情況︰

1. [標籤] 資料行指定錯誤因為標籤為選取多個資料行或不正確的欄索引。 例如，第二個案例會套用如果 30 的欄索引所使用了只有 25 的資料行輸入資料集。

2. 資料集並不會包含任何功能的資料行。 例如，如果輸入的資料集僅 1 的資料行，其標示為 [標籤] 資料行，會用來建立模型功能。 在此例中[訓練模型][train-model]模組會引發錯誤。

3. 輸入資料集 （功能或標籤） 的值，包含無限。


## <a name="score-model-module-does-not-produce-correct-results"></a>成績模型模組不會產生正確的結果

![image2](./media/machine-learning-debug-models/train_test-2.png)

中的一般訓練/測試圖表指導的學習，[分割的資料][split]模組原始資料集分為兩個部分︰ 用來訓練模型的組件和組件至分數程度訓練的模型執行的資料將其保留並未訓練上。 訓練的模型然後用於分數之後的結果會進行評估以判斷模型的精確度的測試資料。

[成績模型][score-model]模組需要兩個輸入︰

1. 從[訓練模型]訓練的模型輸出[train-model]模組
2. 計分資料集的模型已經不訓練上不

即使實驗成功，[分數模型]，可能會發生的[score-model]模組會產生不正確的結果。 多個案例可能會導致發生︰

1. 如果指定的標籤不需分類和迴歸模型訓練的資料，會產生不正確的輸出的[分數模型][score-model]模組。 這是因為迴歸需要一個連續的回應的變數。 在此情況下應該比較適合使用分類模型。 
2. 同樣地，如果分類模型訓練上有浮點數字標籤] 資料行中的資料集，它可能會產生非預期的結果。 這是因為分類需要只允許該範圍內的值限制，而且通常會比較小資料集的類別的不連續的回應變數。
3. 如果計分資料集不包含用來訓練在模型中[的成績模型]的所有功能[score-model]會產生錯誤。
4. [成績模型][score-model]會產生對應到資料列包含遺漏的值或無限值的任何其功能的計分資料集內的任何輸出。
5. [成績模型][score-model]可能會產生相同的輸出計分資料集內的所有資料列。 可能發生這個問題，例如，在當嘗試分類如果，選擇 [最小的數字的每個分葉節點的範例使用決策樹系多個訓練範例使用數。


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 
