<properties
    pageTitle="在 Azure 電腦學習 Studio 建立文字分析模型 |Microsoft Azure"
    description="如何在 Azure 電腦學習 Studio 中文字的前置處理、 N 克或功能雜湊使用模組中建立文字分析資料模型"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>在 Azure 電腦學習 Studio 建立文字分析資料模型

您可以使用 Azure 電腦學習建立並 operationalize 文字分析資料模型。 這些模型可協助您解決，例如，文件分類或舉動分析問題。

在文字分析項實驗中，您通常可以︰

 1. 清除和前置處理文字資料集
 2. 擷取數值功能向量預先處理文字
 3. 訓練分類或迴歸模型
 4. 分數和驗證模型
 5. 部署模型至生產

在此教學課程中，您將瞭解這些步驟時我們逐步瞭解使用 Amazon 活頁簿檢閱資料集的舉動分析模型 (請參閱本文的參考資料 」 傳記、 Bollywood，發展方塊和 Blenders︰ 網域配接舉動分類為 「 John Blitzer、 標記 Dredze 和泰 Pereira;關聯的計算語意 (ACL)，2007年。）此資料集包含檢閱分數 （1 到 2 或 4-5） 和自由格式的文字。 目標是要預測檢閱分數︰ 低 (1-2） 或由高 (4-5)。

您可以找到實驗 Cortana 智慧庫的內容在本教學課程︰

[預測活頁簿檢閱](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[預測活頁簿檢閱-預測實驗](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>步驟 1︰ 清理及預先處理文字資料集

我們開始實驗到公式為兩個類別分類問題分類的高低值區除以檢閱分數。 我們使用 [編輯中繼資料] (https://msdn.microsoft.com/library/azure/dn905986.aspx) 和 [群組分類 Values] (https://msdn.microsoft.com/library/azure/dn906014.aspx) 模組。

![建立標籤](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

然後，我們會清除使用 [預先處理文字] (https://msdn.microsoft.com/library/azure/mt762915.aspx) 模組的文字。 清理減少資料集，協助您尋找最重要的功能，並改善最終模型的精確度雜音。 我們移除 stopwords-常見的單字，例如 「 」 或 「 」-和數字、 特殊字元，重複的字元、 電子郵件地址和 Url。 我們也將文字轉換成小寫、 lemmatize 文字，並偵測句子界限，然後以 「 | | | 「 預先處理文字符號。

![前置處理文字](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

如果您要使用自訂清單的 stopwords 嗎？ 您可以將它中做為選用的輸入。 您可以取代子字串，請使用自訂 C# 語法規則運算式，並移除文字的一部份︰ 名詞、 動作或形容詞。

前置處理完畢之後，我們將訓練分割的資料，並測試設定。

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>步驟 2︰ 擷取數值功能向量預先處理文字

若要建立文字資料模型，您通常必須將自由格式的文字轉換成數值功能向量。 在此範例中，我們會使用 [擷取文字 N 文法檢查功能] 轉換文字資料，例如格式 (https://msdn.microsoft.com/library/azure/mt762916.aspx) 模組。 本單元會採用資料行的空格分隔的文字，並計算字典的文字或 N-克的字詞，會出現在您的資料集。 然後，它會計算每個單字的時間，或 N 文法檢查，會出現在每一筆記錄，，並建立這些功能向量數計算。 在本教學課程中，我們設定 N 文法檢查大小為 2，因此我們功能向量包含單一字或句子的兩個後續。

![擷取 N 克](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

我們套用 TF * IDF （字詞頻率反文件頻率） 加權 N 文法檢查來計算。 這種方式新增一筆記錄中經常出現，但很少，跨整個資料集的文字粗細。 其他選項包含二進位，TF，然後以圖形表示權衡。

通常這類文字功能都有高的維度。 例如，如果您中華 100000 的唯一文字，您功能的空間有 100000 維度] 下方，或多如果 N 克使用。 擷取 N 文法檢查功能模組，可讓您一組選項，以減少維度。 您可以選擇以排除簡短或長，或也很或太經常有大量的預測值的文字。 在本教學課程中，我們會排除在少於 5 記錄或記錄的 80%以上顯示的 N 克。

此外，您可以使用功能選擇，以選取與您的預測目標最相關的這些功能。 我們使用卡方功能選項選取 1000年的功能。 您可以檢視所選的文字或 N 克詞彙解壓縮 N 克模組的右輸出] 即可。

為另一個方法，使用擷取 N 文法檢查功能，您可以使用功能雜湊模組。 請注意，雖然該 [功能雜湊] (https://msdn.microsoft.com/library/azure/dn906018.aspx) 並沒有內建功能選取範圍的功能或 TF * IDF 權衡。

## <a name="step-3-train-classification-or-regression-model"></a>步驟 3︰ 訓練分類或迴歸模型

現在完成數值功能的資料行轉換的文字。 讓我們使用選取資料行中的資料集將其排除資料集仍會包含前一個階段，從字串資料行。

然後使用 [兩個類別後勤迴歸] (https://msdn.microsoft.com/library/azure/dn905994.aspx) 來預測我們的目標︰ 高或低檢閱分數。 此時，文字分析問題，具有已轉換成一般分類問題。 您可以使用 Azure 電腦學習中提供的工具來改善模型。 例如，您可以嘗試使用不同的相關性，若要瞭解如何正確的結果，讓，或 hyperparameter 調整，以改善精確度。

![訓練和分數](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>步驟 4︰ 分數和驗證模型

如何驗證訓練的模型？ 我們對測試資料集分數，並在評估精確度。 不過，模型學 N 克和其加權訓練資料集的詞彙。 因此，我們應該使用該詞彙和這些粗細時從測試資料，而非重新建立詞彙擷取的功能。 因此，我們計分分支的實驗新增擷取 N 文法檢查功能模組、 訓練分支從連線的輸出詞彙及將詞彙模式設為唯讀。 我們也停用的篩選的 N 克依頻率設定為 [1 的執行個體，為 100%的最大值的最小值，並關閉] 功能選取範圍。

已轉換數值功能的資料行的文字中的資料行測試資料之後，例如訓練分支中的上一個階段我們排除字串資料行。 然後我們使用進行預測的分數模型模組以及評估精確度的評估模型模組。

## <a name="step-5-deploy-the-model-to-production"></a>步驟 5︰ 生產部署模型

模型幾乎已部署至生產。 部署 web 服務為，自由格式的文字字串做為輸入，並傳回預測 [高] 或 [低]。 它使用學的 N 文法檢查詞彙轉換功能，並從這些功能來預測訓練後勤迴歸模型的文字。 

若要設定預測的實驗，我們先儲存為資料集，N 文法檢查詞彙和訓練後勤迴歸模型訓練分支的實驗。 然後，我們會儲存實驗建立預測實驗實驗圖使用 [另存新檔]。 我們移除分割的資料模組和訓練分支實驗。 我們連線先前儲存的 N 文法檢查詞彙和模型到擷取 N 文法檢查功能及分數模型模組中，分別。 我們也會移除評估模型模組。

我們在之前前置處理文字模組，若要移除標籤] 資料行的資料集模組中插入選取的欄，並取消選取分數模組中的 「 新增成績資料集] 資料行] 選項。 如此一來，web 服務不要求，嘗試預測，標籤和回應回應的輸入功能的用途。

![預測實驗](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

現在我們可以發佈為 web 服務及使用要求回應或批次執行 Api 呼叫的實驗。

## <a name="next-steps"></a>後續步驟

深入瞭解文字分析模組，從 [MSDN 文件] (https://msdn.microsoft.com/library/azure/dn905886.aspx)。
