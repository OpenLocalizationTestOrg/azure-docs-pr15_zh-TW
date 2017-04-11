<properties
    pageTitle="解譯電腦學習模型產生 |Microsoft Azure"
    description="如何選擇最佳的參數為演算法使用，並以視覺化方式檢視的成績模型輸出。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />


# <a name="interpret-model-results-in-azure-machine-learning"></a>解譯模型中 Azure 電腦學習的結果

本主題說明如何以視覺化效果呈現與判讀 Azure 電腦學習 Studio 中的預測結果。 您有訓練模型，而且完成預測使用其 （「 計分模型 」） 之後，您需要瞭解與判讀預測結果。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

有四種主要電腦學習中 Azure 電腦學習模型︰

* 分類
* 叢集
* 迴歸分析
* Recommender 系統

使用這些模型上方的預測的模組是︰

* [分數模型][score-model]分類和迴歸模組
* [指派給叢集][assign-to-clusters]叢集模組
* [分數 Matchbox Recommender] [score-matchbox-recommender]建議系統

這份文件會說明如何解讀預測結果的每個這些模組。 如需這些模組的概觀，請參閱[如何選擇要最佳化中 Azure 電腦學習演算法的參數](machine-learning-algorithm-parameters-optimize.md)。

本主題地址預測解譯但不是模型評估。 如需有關如何評估模型的詳細資訊，請參閱[如何評估 Azure 電腦學習模型效能](machine-learning-evaluate-model-performance.md)。

如果您是新使用者，Azure 電腦學習，需要建立簡單的實驗，即可開始使用的協助，請參閱[建立簡單的實驗中 Azure 電腦學習 Studio](machine-learning-create-experiment.md) Azure 電腦學習 Studio 中。

## <a name="classification"></a>分類 ##
有兩個的子類別的分類問題︰

* 只有兩個類別 （兩個類別或二進位分類） 的問題
* 具有兩個以上的類別 （多類別分類） 的問題

Azure 電腦學習有不同的模組處理每一種類型的分類，但理解及其預測結果的方法很類似。

### <a name="two-class-classification"></a>兩個類別分類###
**範例實驗**

兩個類別分類問題的範例是虹彩花朵的分類。 任務是分類虹彩花朵根據其功能。 虹彩資料集提供在 Azure 電腦學習熱門[虹彩資料集](http://en.wikipedia.org/wiki/Iris_flower_data_set)的子集包含只有兩年花卉 species （類別 0 和 1） 的執行個體。 四種功能的每個花朵 （sepal 長度、 sepal 寬度、 瓣花瓣長度和瓣花瓣寬度）。

![虹彩實驗的螢幕擷取畫面](./media/machine-learning-interpret-model-results/1.png)

圖 1。 虹彩兩個類別分類問題實驗

圖 1 所示已解決這個問題，請執行實驗。 兩個類別提高決策樹模型有訓練，而且分數。 現在您可以以視覺化方式呈現預測結果從[分數模型][score-model]模組即可[分數模型]的輸出連接埠[score-model]模組，然後按一下 [**視覺效果**。

![成績模型模組](./media/machine-learning-interpret-model-results/1_1.png)

如此會開啟計分結果圖 2] 中所示。

![虹彩兩個類別分類實驗的結果](./media/machine-learning-interpret-model-results/2.png)

圖 2。 以視覺化方式呈現的成績模型結果中兩個類別分類

**結果解譯**

結果資料表中有六個資料行。 左側的四個資料行是四個功能。 右邊的兩個資料行，計分標籤和計分機率是預測結果。 計分機率欄顯示機率花朵屬於正數的類別 (類別 1)。 例如，欄 (0.028571) 所代表的意義有的第一個數字是 0.028571 第一年花卉屬於類別 1 的機率。 計分標籤] 欄會顯示每個花朵的預測的類別。 這根據計分機率欄。 如果的花朵 scored 的機率大於 0.5，它會為類別 1 的預測。 否則，它是與類別 0 的預測。

**Web 服務的出版物**

預測結果有了解並判定音效之後，實驗可以發佈為 web 服務，讓您可以將其部署中各種不同的應用程式，然後呼叫該以取得在任何新的虹彩花朵課程預測。 若要瞭解如何變更計分的實驗訓練實驗，並將其發佈為 web 服務，請參閱[發佈 Azure 電腦學習 web 服務](machine-learning-walkthrough-5-publish-web-service.md)。 此程序提供計分的實驗圖 3 中所示。

![計分實驗的螢幕擷取畫面](./media/machine-learning-interpret-model-results/3.png)

圖 3。 計分虹彩兩個類別分類問題實驗

現在，您需要設定的輸入與輸出 web 服務。 輸入是[分數模型]的正確輸入連接埠[score-model]，這是虹彩花朵功能輸入。 輸出的選項取決於是否您感興趣的預測的類別 （計分標籤）、 scored 的機率，或兩者。 在此範例中，則會假設您有興趣兩者。 若要選取所要的輸出資料行，請使用 [[選取資料集的資料行][select-columns]模組。 按一下 [[選取資料集的資料行][select-columns]，請按一下 [**啟動] 欄選擇器**]，然後選取**分數標籤**和**計分的機率**。 設定[資料集中的 [選取資料行]的輸出連接埠之後[select-columns]並再次執行，您應準備好要發佈為 web 服務的計分的實驗，即可**發佈 WEB 服務**。 最終的實驗看起來像圖 4。

![虹彩兩個類別分類實驗](./media/machine-learning-interpret-model-results/4.png)

圖 4。 虹彩兩個類別分類問題的最後一個分項實驗

執行 web 服務後，當您輸入的測試執行個體的某些功能值時，結果就會傳回兩個數字。 第一個數字是 scored 的標籤，而第二個 scored 的機率。 此花朵 0.9655 機率為類別 1 預測。

![測試解譯分數模型](./media/machine-learning-interpret-model-results/4_1.png)

![計分測試結果](./media/machine-learning-interpret-model-results/5.png)

圖 5。 Web 服務的虹彩兩個類別分類的結果

### <a name="multi-class-classification"></a>多個類別分類
**範例實驗**

在這項實驗中，您可以執行字母辨識任務為 multiclass 分類的範例。 類別器嘗試預測特定字母 （類別） 根據擷取自手寫的圖像部分手寫屬性值。

![字母辨識範例](./media/machine-learning-interpret-model-results/5_1.png)

訓練資料，還有 16 手寫字母圖像中擷取的功能。 26 字母表單 26 類別。 圖 6 顯示實驗會訓練字母辨識 multiclass 分類模型及預測的相同設定測試資料集的功能。

![字母辨識 multiclass 分類實驗](./media/machine-learning-interpret-model-results/6.png)

圖 6。 字母辨識 multiclass 分類問題實驗

視覺化[分數模型]的結果[score-model]模組即可[分數模型]的輸出連接埠[score-model]模組]，然後按一下**視覺效果**，您應該會看到如下圖所示圖 7] 中的內容。

![成績模型的結果](./media/machine-learning-interpret-model-results/7.png)

圖 7。 以視覺化方式呈現的成績模型中多個類別分類的結果

**結果解譯**

左 16 個資料行代表測試設定的功能值。 名稱等計分機率類別 」 XX 「 只要的資料行，例如計分機率資料行在兩個類別的情況下。 即會顯示機率的相對應的項目落在特定類別。 例如，第一個項目，有 0.003571 機率是"A，"是"B，"等 0.000451 的機率。 最後一欄 （計分標籤） 會位於計分標籤相同兩個類別的大小寫。 它會為對應的項目的預測類別選取的最大的 scored 機率的課程。 例如的第一個項目，scored 的標籤是"F"因為它沒有為"F"(0.916995) 的最大的機率。

**Web 服務的出版物**

您也可以取得 scored 的標籤每一個項目和 scored 標籤的機率。 基本邏輯是找到所有 scored 機率之間的最大的機率。 若要這麼做，您需要使用[執行 R 指令碼][execute-r-script]模組。 R 程式碼會顯示在 [圖 8] 中，實驗的結果會顯示在 [圖 9。

![R 程式碼範例](./media/machine-learning-interpret-model-results/8.png)

圖 8。 用於擷取 「 計分標籤與標籤的相關聯的機率 R 程式碼

![實驗結果](./media/machine-learning-interpret-model-results/9.png)

圖 9。 最終分項實驗中的字母辨識 multiclass 分類問題

之後發佈執行 web 服務和輸入某些輸入的功能的值，例如圖 10 其傳回的結果看起來。 此手寫字母，其解壓縮的 16 功能，是要 0.9715 機率為 「 T 」 的預測。

![測試解譯分數模組](./media/machine-learning-interpret-model-results/9_1.png)

![測試結果](./media/machine-learning-interpret-model-results/10.png)

圖 10。 Web 服務的 multiclass 分類的結果

## <a name="regression"></a>迴歸分析

迴歸問題是不同的分類問題。 分類問題，在您嘗試預測不連續的類別，例如的類別虹彩花朵屬於。 但您可以看到在下列範例中的迴歸問題，您嘗試預測連續的變數，例如汽車的價格。

**範例實驗**

使用您的範例迴歸汽車價格預測。 您嘗試預測根據其功能，包括撥打、 燃料類型、 內容類型及磁碟機滾輪汽車的價格。 實驗會顯示在圖 11。

![汽車價格迴歸實驗](./media/machine-learning-interpret-model-results/11.png)

圖 11。 汽車價格迴歸問題實驗

視覺化[分數模型][score-model]模組，結果看起來像圖 12。

![計分結果汽車價格預測問題](./media/machine-learning-interpret-model-results/12.png)

圖 12。 計分汽車價格預測問題的結果

**結果解譯**

計分標籤是此計分結果中的 [結果] 欄。 數字為每個汽車的預測的價格。

**Web 服務的出版物**

您可以發佈至 web 服務的迴歸實驗，並汽車價格預測的兩個類別分類使用案例與相同的方式。

![計分實驗汽車價格迴歸問題](./media/machine-learning-interpret-model-results/13.png)

圖 13。 計分實驗的汽車價格迴歸問題

執行 web 服務，傳回的結果看起來像圖 14。 此汽車的預測的價格是 $15,085.52。

![測試解譯計分模組](./media/machine-learning-interpret-model-results/13_1.png)

![計分模組結果](./media/machine-learning-interpret-model-results/14.png)

圖 14。 Web 服務問題造成的汽車價格迴歸分析

## <a name="clustering"></a>叢集

**範例實驗**

現在就讓我們建立叢集的實驗再次使用虹彩資料集。 這裡您可以篩選出在資料集中的類別標籤，使其只功能可以用於叢集。 在此虹彩使用 [大小寫，請指定叢集兩個訓練程序，這表示您想要將兩個類別叢集花朵時數。 實驗會顯示在圖 15。

![嘗試虹彩叢集問題](./media/machine-learning-interpret-model-results/15.png)

圖 15。 嘗試虹彩叢集問題

叢集不同於 [分類]，訓練資料集沒有地上真實標籤本身。 將不同的叢集叢集群組訓練資料集執行個體。 訓練課程過程中，模型標籤項目，學習其功能的差異。 之後，訓練的模型可進一步分類未來的項目。 有兩個部分，我們感興趣內叢集問題的結果。 第一部分標籤訓練資料集與第二個分類訓練模型新的資料集。

按一下左側的輸出連接埠[訓練叢集模型]的視覺化結果的第一個部分[train-clustering-model]，然後按一下 [**視覺效果**。 圖 16 顯示視覺效果。

![叢集結果](./media/machine-learning-interpret-model-results/16.png)

圖 16。 以視覺化方式呈現叢集訓練資料集的結果

第二部分，叢集訓練叢集模型，以新的項目的結果會顯示在圖 17。

![以視覺化方式呈現叢集結果](./media/machine-learning-interpret-model-results/17.png)

圖 17。 以視覺化方式呈現上新的資料集叢集結果

**結果解譯**

雖然的兩個部分的結果是不同的實驗階段，他們會尋找相同，並會被解譯以相同的方式。 前四個資料行的功能。 最後一欄，指派]，是預測的結果。 指定數相同的項目會位於相同叢集，也就是使用者共用 （這項實驗中使用的預設 Euclidean 距離公制） 的一些方式相似的預測。 您指定為 2 的叢集數目，因為 0 或 1，會標示為工作分派中的項目。

**Web 服務的出版物**

您可以發佈至 web 服務叢集實驗，然後呼叫該叢集預測與兩個類別分類的相同方式使用大小寫。

![計分實驗虹彩叢集問題](./media/machine-learning-interpret-model-results/18.png)

圖 18。 計分虹彩叢集問題的實驗

執行的 web 服務之後，傳回的結果看起來像圖 19。 此花朵是位於叢集 0 的預測。

![測試解譯計分模組](./media/machine-learning-interpret-model-results/18_1.png)

![計分模組結果](./media/machine-learning-interpret-model-results/19.png)

圖 19。 Web 服務的虹彩兩個類別分類的結果

## <a name="recommender-system"></a>Recommender 系統
**範例實驗**

對於 recommender 系統，您可以使用餐廳建議問題的範例︰ 您可以根據其評等記錄的客戶建議餐廳。 輸入的資料是由三個部分組成︰

* 客戶從餐廳評等
* 客戶功能資料
* 餐廳功能資料

有幾個方法，我們可以處理[訓練 Matchbox Recommender] [train-matchbox-recommender]模組中 Azure 電腦學習︰

- 預測評等，指定的使用者和項目
- 建議的項目指定的使用者
- 尋找有關特定使用者的使用者
- 尋找特定項目相關的項目

您可以選擇您想要從**Recommender 預測類型**] 功能表中的四個選項中選取。 在這裡您可以逐步瞭解這四個案例。

![Matchbox recommender](./media/machine-learning-interpret-model-results/19_1.png)

一般的 Azure 電腦學習實驗 recommender 系統看起來像圖 20。 如需如何使用這些 recommender 系統模組的資訊，請參閱[訓練 matchbox recommender] [train-matchbox-recommender]和[分數 matchbox recommender][score-matchbox-recommender]。

![Recommender 系統實驗](./media/machine-learning-interpret-model-results/20.png)

圖 20。 Recommender 系統實驗

**結果解譯**

**預測評等，指定的使用者和項目**

選取下**Recommender 預測類型**的**評等預測**時，您提出 recommender 系統來預測的評等，指定的使用者和項目。 視覺效果的[分數 Matchbox Recommender] [score-matchbox-recommender]輸出看起來像圖 21。

![成績等級預測 recommender 系統的結果](./media/machine-learning-interpret-model-results/21.png)

圖 21。 以視覺化方式呈現評等預測 recommender 系統分數的結果

前兩個資料行是所輸入的資料提供使用者項目組。 第三欄是使用者特定項目的預測評等。 例如，在第一列中，客戶 U1048 預測至工資率餐廳 135026 為 2。

**建議的項目指定的使用者**

選取下**Recommender 預測類型**的**項目建議**，您要請求 recommender 系統建議的項目指定的使用者。 若要選擇在這個案例中的最後一個參數是*建議的項目選取範圍*。 **從評等項目 （模型評估）**也主要的模型評估訓練程序時的選項。 預測階段，選擇 [**從所有項目**。 視覺效果的[分數 Matchbox Recommender] [score-matchbox-recommender]輸出看起來像圖 22。

![分數 recommender 系統項目建議的結果](./media/machine-learning-interpret-model-results/22.png)

圖 22。 以視覺化方式呈現項目建議 recommender 系統的分數的結果

六個資料行中的第一個所提供的輸入資料，代表指定的使用者識別碼的建議，項目。 其他五個資料行代表使用者以遞減的相關建議的項目。 例如，在第一列中，最建議的餐廳客戶 U1048 是 134986，後面接著 135018、 134975、 135021 及 132862。

**尋找有關特定使用者的使用者**

選取下**Recommender 預測類型**的**相關的使用者**，您要請求 recommender 系統來尋找相關的使用者，以指定使用者。 相關的使用者已有類似的喜好設定的使用者。 若要選擇在這個案例中的最後一個參數是*相關的使用者選項*。 **從使用者的評等項目 （模型評估）**也主要的模型評估訓練程序時的選項。 此預測階段，請選擇 [**從所有使用者**]。 視覺效果的[分數 Matchbox Recommender] [score-matchbox-recommender]輸出看起來像圖 23。

![分數 recommender 系統相關的使用者的結果](./media/machine-learning-interpret-model-results/23.png)

圖 23。 以視覺化方式呈現相關的使用者 recommender 系統的分數的結果

六個資料行中的第一個顯示特定的使用者識別碼所需尋找相關的使用者所提供的輸入資料。 其他五個資料行以遞減順序的相關性存放使用者的預測相關的使用者。 例如，在第一列中，客戶 U1048 最相關的客戶是 U1051，後面接著 U1066、 U1044、 U1017 及 U1072。

**尋找特定項目相關的項目**

選取下**Recommender 預測類型**的**相關項目**時，您提出 recommender 系統來尋找特定項目相關的項目。 相關項目是最可能喜歡的相同使用者的項目。 若要選擇在這個案例中的最後一個參數是*相關的項目選取範圍*。 **從評等項目 （模型評估）**也主要的模型評估訓練程序時的選項。 預測階段，我們可以選擇**從所有項目**。 視覺效果的[分數 Matchbox Recommender] [score-matchbox-recommender]輸出看起來像圖 24。

![分數相關項目 recommender 系統的結果](./media/machine-learning-interpret-model-results/24.png)

圖 24。 以視覺化方式呈現相關項目 recommender 系統的分數的結果

六個資料行中的第一個代表指定的項目所提供的輸入資料，尋找相關的項目，所需的識別碼。 其他五個資料行以遞減順序而言相關性儲存的項目預測相關的項目。 例如，在第一列中，項目的 135026 最相關的項目是 135074，後面接著 135035、 132875、 135055 及 134992。

**Web 服務的出版物**

發佈 web 服務以取得預測這些實驗的程序很類似的四個案例的每個項目。 以下我們的範例需要第二個案例 （建議項目指定的使用者）。 您可以遵循與其他三個的相同程序。

儲存為訓練模式的訓練的 recommender 系統和篩選輸入的資料以單一使用者識別碼] 資料行的要求，您可以連結與圖 25 實驗，發佈為 web 服務。

![計分餐廳建議問題的實驗](./media/machine-learning-interpret-model-results/25.png)

圖 25。 計分餐廳建議問題的實驗

執行 web 服務，傳回的結果看起來像圖 26。 使用者 U1048 五個建議的餐廳是 134986、 135018、 134975、 135021 及 132862。

![Recommender 系統服務的範例](./media/machine-learning-interpret-model-results/25_1.png)

![範例實驗結果](./media/machine-learning-interpret-model-results/26.png)

圖 26。 Web 服務問題造成的餐廳建議


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
