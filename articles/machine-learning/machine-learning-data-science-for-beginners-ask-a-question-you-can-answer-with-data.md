<properties
   pageTitle="提出問題，您可以回答資料-制定的問題 |Microsoft Azure"
   description="瞭解如何在公式中的資料科學初學者影片 3 的資料科學問題。 包含比較分類和迴歸問題的相關資訊。"
   keywords="資料科學問題制定問題、 迴歸問題、 分類問題、 明顯的問題"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="ask-a-question-you-can-answer-with-data"></a>提出您的資料，您可以回答的問題

## <a name="video-3-data-science-for-beginners-series"></a>初學者系列的影片 3︰ 資料科學

瞭解如何在公式中的資料科學初學者影片 3 的資料科學問題。 這段影片中包含的分類和迴歸演算法的比較。

若要取得善用數列，請觀看所有。 [移至清單的視訊](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>本系列中的其他影片

*資料科學初學者*是五個簡短的視訊中的資料科學的快速簡介。

  * 影片 1: [5 問題資料科學答案](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *（5 分鐘 14 秒）*
  * 影片 2:[是您的資料供資料科學？](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *（4 min 56 秒）*
  * 影片 3︰ 提出您的資料，您可以回答的問題
  * 影片 4︰[預測答案的簡單的模型](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *（7 min 42 秒）*
  * 影片 5︰[複製執行資料科學將其他人的工作](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *（3 min 18 秒）*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>字幕︰ 提出您的資料，您可以回答的問題

歡迎使用一系列中的第三個視訊 」 資料科學初學者 」。  

在此項目，您會收到一些秘訣，公式的資料，您可以回答的問題。

您可能會更多不在這段影片中，取得您第一次觀賞這一系列中的兩個舊版視訊: 「 5 問題資料科學可以回答 「 及 」 是您的資料已準備好資料科學 」？

## <a name="ask-a-sharp-question"></a>井字發問

我們已討論如何資料科學的預測問題的回答使用 （也稱為 「 類別或標籤 」） 的名稱和數字的程序。 但不是能任何問題。它必須是*井字問題。*

模糊問題沒有待名稱或數字。 必須井字問題。

假設您找到神奇燈與 genie 會說回答您要求的任何問題。 但惡作劇 genie，，他會嘗試，使其 answer 模糊與令人他能立即使用。 您想要利用 pin，因此 airtight 他無法用來協助，但您想要知道，告訴您的問題。

如果您要發問模糊，等 」 所要的 [我的股票圖？ 」、 genie 可能回答 「 價格會變更 」。 這是 truthful 的答案，但不是非常實用。

但如果您是要發問井字，例如 「 我的股票圖銷售價格是什麼下週？ 」，genie 無法協助但讓您在特定回答，並預測銷售價格。

## <a name="examples-of-your-answer-target-data"></a>答案的範例︰ 目標資料

當您規劃您的問題時，請檢查是否有答案的範例資料中。

如果我們問題是 「 我的股票圖銷售價格是什麼下週？ 」 然後，我們具備確保我們的資料包含股票價格歷程記錄。

如果我們問題 」 中我艦隊的汽車移至第一次失敗 」？ 然後，我們具備確保我們的資料包含前一個失敗的相關資訊。

![目標資料-答案的範例。 制定資料科學問題。](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

這些範例答案稱為 「 目標 」。 目標是什麼我們嘗試預測未來的資料點，無論您是類別或數字。

如果您沒有安裝任何目標資料，必須取得部分。 您無法接聽您的問題沒有它。

## <a name="reformulate-your-question"></a>/Baseless 您的問題

有時候您可以重新編輯您的問題，以取得更多實用的回應。

問題 」 是此資料點 A 或 B？ 」 預測的類別 （或名稱或標籤） 的項目。 若要接聽它，我們使用*分類演算法*。

問題 」 多少？ 」 或者 「 如何多嗎？ 」 預測金額。 若要回答，我們使用*迴歸演算法*。

若要查看如何我們可以轉換為這些，我們來看看問題] 」 的新聞報導是最感興趣此讀者 」？ 它會要求話-從某一個選項來預測換句話說 」 是此 A B 或 C 或 D？ 」 -並想要使用的分類演算法。

但是，此問題可能會更容易接聽如果您為重述 」 方式感興趣是這個讀取此清單上的每個本文？ 」 現在您可以授與每篇文章的數字的分數，然後輕鬆找出最高分數文件。 這是將迴歸問題分類問題進而或多少？

![/Baseless 您的問題。 迴歸問題與分類問題。](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

您可以提出問題是何種演算法線索方式可讓您回答。

您可以找到密切相關的演算法的新聞報導這個範例來說-類似的特定系列。 您可以 /baseless 讓您使用演算法可讓您最有用的回答的問題。

但是，最重要的是，問過清晰-您可以使用資料回答的問題。 並確定您有正確的資料回答。

我們已討論的資料，您可以回答一些基本準則的提出問題。

請務必取出 」 資料科學的初學者 」 從 Microsoft Azure 電腦學習中的其他影片。


## <a name="next-steps"></a>後續步驟

  * [請嘗試電腦學習 Studio 第一個資料科學體驗](machine-learning-create-experiment.md)
  * [Microsoft Azure 上取得電腦學習的簡介](machine-learning-what-is-machine-learning.md)
