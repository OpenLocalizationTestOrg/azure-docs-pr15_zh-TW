<properties
    pageTitle="建立類型與模型品質︰ 建議 API |Microsoft Azure"
    description="Azure 電腦學習建議-快速入門指南"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>建立類型與模型品質 #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>支援的建立類型 ##

建議 API 目前支援兩種建立類型︰*推薦*] 和 [ *FBT*。 每個內建使用不同的演算法，各有不同的優點。 這份文件說明這些建置和技術比較模型產生的品質。

如果您有不這麼做，我們建議您先完成的[快速入門指南](cognitive-services-recommendations-quick-start.md)。

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>建議建立類型 ###

建議建立類型使用矩陣 factorization 提供建議。 它會產生[潛在功能](https://en.wikipedia.org/wiki/Latent_variable)向量根據您的交易描述每個項目，並再使用這些潛在向量比較類似的項目。

如果您訓練購買 electronics 存放區中的項目為基礎的模型，提供 Lumia 650 電話與模型輸入模型會傳回一組似乎經常犯的可能購買 Lumia 650 電話的人員可購買的項目。 項目可能無法互補。 在此範例中，可能是模型會傳回其他電話，因為喜歡 Lumia 650 可能像其他電話。

建議建立具有兩個功能，可讓您更吸引人︰

**支援建議建立*低溫項目*位置 * *

沒有重大的使用方式的項目稱為低溫的項目。 例如，如果您收到的電話，您永遠不會賣出之前，系統無法推斷上交易了解單獨使用這項產品的建議。 這表示系統應該瞭解產品本身的相關資訊。

如果您想要使用低溫的項目位置，您需要提供功能的每個項目目錄] 中的資訊。 下列是什麼類別目錄的前幾行看起來可能像 （記事金鑰 = 值格式的功能）。

>6CX 00001、 曲面 Pro2、 Surface、 輸入 = 儲存空間的硬體 = 128 GB 的記憶體 = 4 G、 製造商 = Microsoft

>73 H 00013，喚醒 Xbox 360 遊戲，，類型 = 軟體，語言英文、 評等的 = = 成熟

>WAH 0F05、 xbox 360 版 Minecraft 遊戲、 * 類型 = 軟體，語言西班牙文、 評等的 = = 青春

您也需要設定下列建立參數︰

| 建立參數         | 備忘稿
|------------------     |-----------
|*useFeaturesInModel*     | 設**為 true**。  指出是否功能可以用來強化建議模型。
|*allowColdItemPlacement*   | 設**為 true**。 指出是否建議也應該推低溫的項目透過功能相似性。
| *modelingFeatureList*   | 在建議建立用來強化建議功能名稱的逗點分隔清單。 例如，「 語言，儲存 」 的前面的範例。

**建議建立支援使用者建議**

建議建立支援[使用者建議](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)。 這表示它能根據其交易歷程記錄的使用者的個人化的建議。 使用者建議，您可能會提供的使用者識別碼] 或 [最近的歷程記錄的交易針對該使用者進行。

您可能要套用使用者建議的傳統範例是在 [登入 [歡迎] 頁面上的一個。 那里升階適用於特定使用者的內容。

您也可能會想要套用的建議建立類型，查看使用者時。 此時，您有使用者即將購買的項目清單，而且您可以根據目前的市場籃的建議。

#### <a name="recommendations-build-parameters"></a>建議建立參數

| 名稱  |   描述 |    輸入， <br>  有效的值 <br> （預設值）
|-------|-------------------|------------------
| *NumberOfModelIterations* |   模型執行重複次數會反映整體計算時間，以及模型精確度。 高數字，提供更精確的模型，但計算時間耗費的時間。  |   整數， <br>  若要 50 10 <br>(40)
| *NumberOfModelDimensions* |   維度的數目與模型會嘗試尋找您的資料中的功能數目。 增加維度的數目，可讓更有效地將較小的叢集微調的結果。 不過，太多維度會防止模型尋找項目之間的關聯性。 |  整數， <br> 若要 40 10 <br>(20) |
| *ItemCutOffLowerBound* |  定義的項目，即可被視為模型時，應該位於的使用方式點最小值的數目。 |        整數， <br> 2 或更多 <br> (2) |
| *ItemCutOffUpperBound* |  定義的項目，即可被視為模型時，應該位於使用點的數目上限。 |  整數， <br>2 或更多<br> (2147483647) |
|*UserCutOffLowerBound* |   定義交易使用者必須先執行視為模型的組件最小的數的字。 | 整數， <br> 2 或更多 <br> (2)
| *UserCutOffUpperBound* |  定義的交易使用者必須先執行考慮的模型中的最大數目。 | 整數， <br>2 或更多 <br> (2147483647)|
| *UseFeaturesInModel* |    指出是否功能可以用來強化建議模型。 |     布林值<br> 預設:，則為 True
|*ModelingFeatureList* |    在建議建立用來強化建議功能名稱的逗點分隔清單。 重要的功能而定。 |    字串，最多 512 個字元
| *AllowColdItemPlacement* |    指出是否建議也應該推低溫的項目透過功能相似性。 | 布林值 <br> 預設值︰ False
| *EnableFeatureCorrelation*    | 表示是否功能可以使用的原因。 | 布林值 <br> 預設值︰ False
| *ReasoningFeatureList* |  逗點分隔的用於弄句子，例如建議說明的功能名稱清單。 重要客戶的功能而定。 | 字串，最多 512 個字元
| *EnableU2I* | 啟用個人化的建議，也稱為使用者將項目 (U2I) 的建議。 | 布林值 <br>預設:，則為 True
|*EnableModelingInsights* | 定義離線評估是否應該執行收集模型獲得深入見解 （也就是精確度和多樣性指標）。 如果設為 true，資料的子集不會用於因為它會需要保留以進行測試模型的訓練課程。 進一步瞭解[離線評估](#OfflineEvaluation)。 | 布林值 <br> 預設值︰ False
| *SplitterStrategy* | 如果啟用模型的深入見解設為*true*時，這是如何分割的資料供評估之用。  | 字串、 *RandomSplitter*或*LastEventSplitter* <br>預設︰ RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>FBT 建立類型 ###

經常 bought 分成一組 (FBT) 建立會計算兩個或三個不同的產品共同發生次數共同分析。 然後會排序依據相似性函數 （**共同的項目**， **Jaccard**，**請拿起**） 的集合。

將**Jaccard**並**拿起**視為正規化共同出現的方式。  這表示，才會傳回的項目他們種子項目與購買的位置。

在 Lumia 650 電話範例中，只有當電話 X 的購買 Lumia 650 電話為相同的工作階段，將會傳回電話 X。 這可能是不太，因為我們預期的項目會傳回; Lumia 650 的補充例如，螢幕保護裝置或 power 介面卡的 Lumia 650。

目前，購買相同的工作階段，如果發生具有相同的使用者識別碼和時間戳記的交易假設兩個項目。

FBT 建置不支援低溫的項目，因為定義所預期購買相同的交易中的兩個項目。 雖然 FBT 建置可以傳回的項目 (triplets)，不支援個人化的建議因為他們接受輸入的單一種子項目。


#### <a name="fbt-build-parameters"></a>FBT 建立參數

| 名稱  |   描述 |       輸入，  <br> 有效的值 <br> （預設值）
|-------|---------------|-----------------------
| *FbtSupportThreshold* | 傳統方式是模型。 次數共同要考慮的模型的項目。 |  整數， <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | 範圍經常集中的項目數。| 整數  <br> 2-3 <br> (2)
| *FbtMinimalScore* | 常用的設定應有傳回的結果中所包含的最小分數。 更好。 | 點兩下 <br> 0 及以上 <br> (0)
| *FbtSimilarityFunction* | 定義建置所使用的相似性函數。 **請拿起**偏袒 serendipity**共同的項目**偏袒預測，與**Jaccard**會危害兩者之間。 | 字串  <br>  <i>cooccurrence 增益 jaccard</i><br> 預設︰ <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>建立評估並選取範圍 ##

本指南也許可以協助您決定是否應該使用建議建置或 FBT 建置，但不提供對於在情況下無法使用其中的位置。 此外，即使您知道您想要使用 FBT 建立類型，您可能仍要選擇**Jaccard**或**拿起**以相似性函數。

若要選取兩個不同組建之間的最佳方式是在真實世界 （線上評估） 測試及追蹤轉換率，不同的建置。 無法根據建議按下滑鼠按鈕以轉換率，實際的數字購買建議顯示，或甚至在實際購買金額時所顯示的不同的建議。 您可以選擇根據您的商務目標您轉換工資率公制。

在某些情況下，您可能要評估的模型離線，先將其放在生產環境中。 雖然離線評估不線上評估取代，它可以做為度量單位。

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>離線評估  ##

離線計算的目標是要預測精確度 （購買其中一個建議的項目的使用者數目） 和多樣性的建議 （建議使用的項目數目）。
精確度和多樣性指標評估的一部分，系統會找出使用者的範例，分割為兩個群組的交易為這些使用者︰ 訓練資料集和測試資料集。

> [AZURE.NOTE]若要使用離線的度量，您必須加上時間戳記您使用的資料。
> 時間資料，才能分割正確之間的訓練與測試資料集的使用方式。

> 此外，離線評估可能不會產生的小型使用檔案的結果。 若要徹底，評估應該 1000 測試資料集內的使用狀況點的最小值。

<a name="Precision"></a>
### <a name="precision-at-k"></a>在-k 的精確度 ###
下表代表精確度，k 離線評估的輸出。

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|百分比 |   13.75 | 18.04   | 21 |  24.31 | 26.61
|測試中的使用者 |    10000 |    10000 |    10000 |    10000 |    10000
|考慮的使用者 | 10000 |    10000 |    10000 |    10000 |    10000
|使用者不會被視為 | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
在上一個資料表中， *k*代表客戶所顯示的建議數目。 表格顯示如下: 「 如果測試期間只有一個建議顯示為 [客戶]，只 13.75 的使用者想要購買的建議。 」 這個陳述式根據假設模型已經訓練購買資料。 另一種方法假設這是在 1 精確度 13.75。

您會發現當客戶顯示更多項目，往上移購買的建議的項目之客戶的可能性。 針對上述實驗中，機率幾乎會加倍 26.61%時，建議您使用 5 個項目。

#### <a name="percentage"></a>百分比
顯示百分比至少一個*k*建議互動的使用者。 計算百分比是除以總的數字的使用者視為互動至少有一個建議的使用者數目。 請參閱使用者的詳細資訊。

#### <a name="users-in-test"></a>測試中的使用者
此列中的資料代表使用者測試資料集的總數。

#### <a name="users-considered"></a>考慮的使用者
如果系統，則建議使用訓練資料集產生模型為基礎的*k*項目，只會被視為使用者。

#### <a name="users-not-considered"></a>使用者不會被視為
此列中的資料代表不會被視為任何使用者。 至少沒有收到使用者*k*建議的項目。

使用者不會被視為 = 使用者測試 – 使用者視為

<a name="Diversity"></a>
### <a name="diversity"></a>多樣性 ###
多樣性指標測量建議的項目的類型。 下表代表多樣性離線評估的輸出。

|百分位數的值區 |    0 90|  90-99| 99 100
|------------------|--------|-------|---------
|百分比        | 34.258 | 55.127| 10.615


總建議的項目︰ 100000

建議您唯一的項目︰ 954

#### <a name="percentile-buckets"></a>百分位數的值區
每個百分位數的連結會以 （最小值和最大值的範圍是介於 0 到 100） 的範圍。 關閉 100 的項目是最常用項目，且最常用靠近 0 的項目。 例如，如果 10.6 99 100 個百分位數的值的百分比值，這表示 10.6 百分比的建議傳回前一個百分比最常用項目。 百分位數的值區最小值，而最大值是獨佔式，除了 100。
#### <a name="unique-items-recommended"></a>建議您唯一的項目
建議公制的唯一項目會顯示不同的評估傳回的項目數目。
#### <a name="total-items-recommended"></a>建議的項目總計
項目總計建議公制顯示的建議的項目數。 一些可能重複項目。

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>離線評估指標 ###
當您選取要使用哪個建立，可能很有用的精確度和多樣性離線指標。 建立時間做為分別 FBT 或建議的一部分建立參數︰

-   *EnableModelingInsights*建立參數設**為 true**。
-   （選擇性） 選取 [ *splitterStrategy* (任一*RandomSplitter*或*LastEventSplitter*)。
*RandomSplitter*分割訓練的使用情況資料，並測試設定根據指定的*randomSplitterParameters*測試百分比和隨機植值。
*LastEventSplitter*分割訓練的使用情況資料和測試根據每位使用者的最後一個交易的設定。

這樣會觸發建置訓練課程中使用的資料子集，並會使用其餘的資料來計算評估指標。  建立完成後，以取得輸出的評估，您必須洽詢[取得建立指標 API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f)，分別*modelId*和*buildId*傳遞。

 以下是範例評估 JSON 輸出效果。


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
