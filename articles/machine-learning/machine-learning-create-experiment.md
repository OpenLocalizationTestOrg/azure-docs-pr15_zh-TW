<properties
    pageTitle="機器學習 Studio 中的簡單實驗 |Microsoft Azure"
    description="在此機器學習教學課程會帶領您完成輕鬆資料 science 符合原先的假設。 我們將預測使用迴歸演算法汽車的價格。"
    keywords="線性迴歸、 機器學習演算法、 機器學習課程、 預測模型技術 （英文）、 資料 science 實驗實驗"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>機器學習教學課程： 在 Azure 機器學習 Studio 中建立您第一個資料 science 實驗

此機器學習教學課程會帶領您完成的簡單資料 science 實驗。 我們將建立預測例如根據不同的變數汽車價格進行線性迴歸模型和技術規格。 若要這樣做，我們將使用 Azure 機器學習 Studio 來開發與逐一查看上簡單預測性分析試驗。

*預測性分析*使用目前資料，以預測未來結果的資料 science 的類型。 預測性分析非常簡單範例，觀賞資料 Science 適合初學者影片 4︰[預測簡單模型解答](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md)(執行階段： 7:42)。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>機器學習 Studio 如何協助？

機器學習 Studio 容易設定項實驗中使用拖放模組預先排定與預測模型技術 （英文）。 若要執行實驗及預測答案，您將使用機器學習 Studio 來*建立模型*、*訓練模型*，以及*分數測試模型*。

輸入機器學習 Studio： [https://studio.azureml.net](https://studio.azureml.net)。 如果您已登入電腦學習 Studio 之前，請按一下 [**以下登入**]。 否則請按一下 [**註冊**並選擇可用和付費選項。

如需一般機器學習 Studio 的詳細資訊，請參閱[機器學習 Studio 是什麼？](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>若要建立實驗的五個步驟

在學習課程這台機器，您將執行建置符合原先的假設機器學習 Studio 才能建立、 訓練、 及分數在模型中的五個基本步驟：

- 建立模型
    - [步驟 1： 取得資料]
    - [步驟 2： 前置處理資料]
    - [步驟 3： 定義功能]
- 訓練模型
    - [步驟 4： 選擇並套用學習演算法]
- 分數和測試模型
    - [步驟 5： 預測新的汽車經銷商價格]

[步驟 1： 取得資料]: #step-1-get-data
[步驟 2： 前置處理資料]: #step-2-preprocess-data
[步驟 3： 定義功能]: #step-3-define-features
[步驟 4： 選擇並套用學習演算法]: #step-4-choose-and-apply-a-learning-algorithm
[步驟 5： 預測新的汽車經銷商價格]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>步驟 1： 取得資料

有一些您可以選擇的機器學習 Studio 所含的範例資料集和您可從許多來源匯入資料。 此範例中，我們將會使用包含的範例資料集、**汽車價格資料 （原始）**。
此資料集包含項目數量的個別汽車，包括建立、 模型、 技術規格以及價格等資訊。

1. 依序按一下 [ **+ 新增**機器學習 Studio 視窗底部開始新的實驗、 選取**實驗**，然後再選取**空白試驗**。 選取預設的實驗名稱上方的畫布，並命名為有意義，例如**汽車價格預測**。

2. 左邊實驗的畫布是資料集和模組的調色盤。 在此尋找標示**汽車價格資料 （（原始））**的資料集的調色盤頂端的 [搜尋] 方塊中輸入**汽車**。

    ![調色盤搜尋][screen1a]

3. 將資料拖曳至實驗畫布。

    ![資料集][screen1]

若要查看此資料的外觀，按一下底部的汽車經銷商的資料集，輸出連接埠，然後選取**視覺效果**。

![單元輸出連接埠][screen1c]

變數中的資料集顯示為欄與每一個執行個體汽車顯示為 [資料列。 右欄欄 （欄 26 和標題"價格"） 是我們要嘗試預測的目標變數。

![資料集視覺化][screen1b]

關閉視覺效果視窗按一下"**x**"右上角。

## <a name="step-2-preprocess-data"></a>步驟 2︰ 前置處理資料

資料集通常需要一些這之前可以分析。 您可能已經注意到遺失出現在各種資料列的表格欄的值。 這些遺失值需要讓模型可以正確地分析的資料清除。 在我們的案例，我們將會移除任何已遺失值的資料列。 而且、**正規化激增**欄都有遺失值、 大比例讓我們將該資料行完全排除模型。

> [AZURE.TIP] 清除中輸入的資料遺失的值是使用大部分模組的必要條件。

我們將會先移除**正規化激增**] 欄中，並接著我們將會移除已遺失資料的任何資料列。

1. 要尋找[選取欄中的資料集]的模組調色盤頂端的搜尋方塊中輸入 [**選取欄**[select-columns]模組，然後拖曳以符合原先的假設畫布並將其連線至**汽車價格資料 （原始）**資料集的輸出連接埠。 本單元可讓我們選取我們想要包含或排除在模型中的資料的欄。

2. 選取 [[選取欄中的資料集][select-columns]模組和在 [**內容**] 窗格中按一下 [**啟動] 欄中選取器**。

    - 在左側，按一下 [**使用規則**
    - **開始使用**，按一下 [**所有欄**。 這會指示[選取欄中的資料集][select-columns]通過 （除外我們即將排除） 中的所有欄。
    - 從下拉式清單中，選取**排除**與**資料行名稱**，然後按一下 [文字] 方塊內。 會顯示欄的清單。 選取**正規化激增**，同時也會新增至該文字方塊。
    - 按一下關閉欄選取器的核取記號 (OK) 按鈕。

    ![下列位置選取欄][screen3]

    [內容] 窗格**中的資料集選取**欄指出它將會從**正規化激增**以外的資料集傳遞到所有欄。

    ![選取欄中的資料集屬性][screen4]

    > [AZURE.TIP] 您可以新增至模組的註解連按兩下模組，然後輸入文字。 這可協助您快速掌握模組執行實驗中。 在此例中，按兩下 [[選取欄中的資料集][select-columns]模組和類型註解"排除正規化激增。 」

3. 拖曳[全新遺失資料][clean-missing-data]實驗的模組畫布並將其連線至[選取的欄中的資料集][select-columns]模組。 在 [**內容**] 窗格中，選取 [移除已遺失值的資料列清除資料**清理模式**下的 [**移除整個資料列**。 按兩下模組和輸入註解"移除遺漏值列 」。

    ![全新遺失資料內容][screen4a]

4. 執行符合原先的假設下實驗畫布中按一下 [**執行**。

實驗完成時，所有模組都有綠色的核取記號表示它們都成功完成。 請注意也**已經完成執行**狀態的右上角。

![第一個項實驗中執行][screen5]

我們已完成此點實驗中所有已清除資料。 若要檢視已清除資料集、 按一下左邊的輸出的連接埠的[全新遺失資料][clean-missing-data]單元 ("已清理 dataset") 並選取 [**視覺效果**。 請注意**正規化激增**欄不再包含，並沒有任何遺失的值。

既然此資料是 clean，我們已經準備好可指定我們要預測模型中使用哪些功能。

## <a name="step-3-define-features"></a>步驟 3： 定義功能

在機器學習*功能*是個別值得注意屬性您感興趣的某個項目。 在我們的資料集的每一列代表一個汽車和每一欄是該汽車的功能。

尋找優建立預測模型的功能集需要實驗和您想要解決此問題的相關知識。 某些功能會較佳的預測的目標比其他人。 此外，某些功能已與其他功能 (例如-市/鎮-mpg 與公路 mpg)、 強式相互關聯，讓他們不會將太多新資訊新增至模型，並可移除。

我們建置在我們的資料集使用的功能的子集的模型。 您可以回來並選取不同的功能、 再次執行實驗並查看是否您要取得較佳的結果。 但是若要啟動，讓我們嘗試下列功能：

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. 拖曳另一個[選取資料行中資料集][select-columns]實驗的模組畫布並將其連線至左邊的輸出連接埠的[全新遺失資料][clean-missing-data]模組。 按兩下 [模組，然後輸入 「 預測的選取功能。 」

2. 在 [**內容**] 窗格中，按一下 [**啟動] 欄中選取器**。

3. 按一下 [**規則**]。

4. 下**開始與**按一下**任何欄**、，然後選取**包含**和**資料行名稱**[篩選] 列中。 輸入我們的資料行名稱的清單。 這會導向至通過我們指定的欄的模組。

    > [AZURE.TIP] 執行項實驗中，我們已確保我們的資料欄定義傳遞從資料集[全新遺失資料]透過[clean-missing-data]模組。 這表示連線其他模組同時也會從資料集的資訊。

5. 按一下核取記號 (OK) 按鈕。

![下列位置選取欄][screen6]

這會產生將使用中的下一個步驟的學習演算法中的資料集。 稍後，您可以傳回並再試一次使用不同的選取範圍的功能。

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>步驟 4： 選擇並套用學習演算法

既然此資料是準備好，建構預測模型包含訓練和測試。 我們將訓練模型及然後先測試以查看如何關閉它能夠預測價格模型使用我們的資料。 現在請不要擔心為什麼我們需要訓練然後先測試模型。

*分類*及*迴歸*兩種監督機器學習技術 （英文）。 分類預測來自組已定義的類別，例如 （紅色、 藍色，或綠色） 色彩的答案。 迴歸來預測數字。

因為我們想要預測價格，也就是數字，我們會使用迴歸模型。 此範例中，我們將訓練簡單*線性迴歸*模型，並在下一個步驟中，我們將測試其。

1. 我們會使用我們資料訓練與由分割成不同的訓練與測試組測試。 選取並拖曳[分隔資料][split]實驗的模組畫布並將其連線至最後一個[選取集中的資料行]的輸出[select-columns]模組。 設定 0.75**起算的列中的第一個輸出資料集**。 如此一來，我們將使用之資料的 75%訓練此模型中，按住回測試的 25%。

    > [AZURE.TIP] 透過變更**隨機種子**參數，您可以產生訓練與測試其他隨機的範例。 此參數會控制虛擬亂數產生器的植入。

2. 執行實驗。 這可讓[選取欄中的資料集][select-columns]與[分割資料][split]模組將欄定義傳遞給我們將下一步] 新增的模組。  

3. 若要選取的學習演算法，展開**電腦學習**類別模組調色盤中的畫布的左邊，然後展開**初始化模型**。 這會顯示模組可以用來初始化機器學習演算法的數個的類別。

    這項實驗中，選取 [[線性迴歸][linear-regression]模組底下**迴歸**類別 （您也可以找到模組︰ [搜尋] 方塊中輸入 「 線性迴歸"），並將其拖曳至實驗畫布。

4. 尋找並拖曳[訓練模型][train-model]實驗畫布的模組。 [線性迴歸]的輸出連線的左邊的輸入連接埠[linear-regression]模組。 訓練資料輸出 （左連接埠） 的[分割資料]連線的右邊的輸入連接埠[split]模組。

5. 選取[訓練模型][train-model]單元中，按一下 [**內容**] 窗格中的 [**啟動] 欄中選取器**，然後選取 [**價格**] 欄。 這是我們的模型將要預測的值。

    ![選取 「 價格 」 資料行][screen7]

6. 執行符合原先的假設。

結果是可用來分數進行預測的新範例受過訓練的迴歸模型。

![套用機器學習演算法][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>步驟 5： 預測新的汽車經銷商價格

現在，我們已經過訓練使用 75%的我們資料模型，我們可用以分數完善查看資料的其他 25%我們模型的功能。

1. 尋找並拖曳[分數模型][score-model]實驗的模組畫布及[訓練模型]的輸出連線的左邊的輸入連接埠[train-model]模組。 測試資料 （由右連接埠） 的輸出[分割資料]連線的右邊的輸入連接埠[split]模組。  

    ![分數模型模組][screen8a]

2. 若要執行實驗並檢視[分數模型]的輸出[score-model]單元中，按一下輸出連接埠]，然後選取 [**視覺效果**。 輸出顯示價格的預測的值和測試資料從已知的值。  

3. 最後，若要測試結果的品質，請選取並拖曳[評估模型][evaluate-model]實驗的模組畫布，並連線至的[分數模型]輸出的左邊的輸入連接埠[score-model]模組。 (有兩個輸入連接埠因為[評估模型][evaluate-model]模組可用來比較兩個模型。)

4. 執行符合原先的假設。

若要檢視的輸出[評估模型][evaluate-model]單元中，按一下輸出連接埠]，然後選取 [**視覺效果**。 下列的統計資料顯示為我們的模型：

- **意思是絕對錯誤**(MAE)： 平均值絕對錯誤 （*錯誤*是預測以及實際值的差異）。
- **根 Mean 平方和的錯誤**(RMSE)： 平均值平方和錯誤的測試資料集上進行的預測的平方根。
- **相對的絕對錯誤**︰ 絕對錯誤相對於實際值與所有的實際值的平均的絕對差異的平均。
- **相對的平方和錯誤**︰ 相對於實際值與所有的實際值的平均的平方和差異的平方和錯誤的平均。
- **相關係數的判斷**︰ 也稱為**R 平方值**，這是表示一種模型完善適合資料統計評量。

各項錯誤的統計資料，較小愈好。 較小的值會指出預測更密切符合實際值。 **相關係數的判斷**，愈其值的其中一個 (1.0) 愈好預測。

![評估結果][screen9]

最後一個實驗應該看起來如下：

![機器學習教學課程： 完成線性迴歸實驗使用預測模型技術 （英文）。][screen10]

## <a name="next-steps"></a>後續步驟

既然您已完成的第一部機器學習教學課程並已設定在實驗，您可重複嘗試改善模型。 例如，您可以變更用於您預測的功能。 您可以修改的[線性迴歸]屬性或者[linear-regression]演算法或完全嘗試不同的演算法。 您甚至可以新增學習一次實驗的演算法的多部電腦和比較兩個使用[評估模型][evaluate-model]模組。

> [AZURE.TIP] 使用**SAVE AS** ] 按鈕下實驗畫布複製您實驗的任何反覆運算。 您可以看到所有重複項目實驗畫布底下按一下**檢視執行歷程記錄**。 請參閱[管理實驗 Azure 機器學習 Studio 中的重複項目][runhistory]如需詳細資訊。

[runhistory]: machine-learning-manage-experiment-iterations.md

當您滿意您模型時，可以將其部署為要用來預測汽車經銷商價格使用新資料的 web 服務。 請參閱 ＜ [Deploy Azure 機器學習 web 服務][publish]如需詳細資訊。

[publish]: machine-learning-publish-a-machine-learning-web-service.md

針對預測模型 （英文） 技術的建立更廣泛且詳細逐步解說、 訓練、 計分，以及部署模型中，請參閱 ＜ [Develop 預測性解決方案使用 Azure 機器學習][walkthrough]。

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
