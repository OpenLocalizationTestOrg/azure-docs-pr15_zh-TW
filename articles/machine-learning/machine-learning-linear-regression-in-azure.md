<properties 
    pageTitle="在電腦學習使用線性迴歸 |Microsoft Azure" 
    description="在 Excel 和 Azure 電腦學習 Studio 中的線性迴歸模型的比較" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>Azure 電腦學習中使用線性迴歸

> *溫 Baroni*和*精靈 Boatman*是在 Microsoft 的資料深入見解卓越中心企業解決方案架構。 本文中，這些描述將現有的迴歸分析套件移轉至雲端的方案，使用 Azure 電腦學習使用經驗。  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>目標

開始使用兩個目標記住我們專案︰  

1. 若要改善我們的組織每月收入估算的精確度使用預測狀況分析  
2. 若要確認、 最佳化，請增加速度，使用 Azure 毫升和小數位數的結果。  

一樣，我們的組織移到每月的收入預測程序。 我們小型小組中的商務分析師需要使用電腦學習支援程序，並改善預測的精確度。  小組所花費的幾個月收集多個來源的資料，以及執行識別鍵屬性相關服務銷售預測的統計分析資料屬性。  下一個步驟就是開始原型迴歸統計模型，在 Excel 中的資料。  在幾週內我們已 outperforming 的目前欄位及預測程序的財務 Excel 迴歸模型。 此變得比較基準預測結果。  


然後原本的下一個步驟來找出如何預測效能可能會改善 Azure 毫升 Azure 毫升移我們的預測狀況分析。


## <a name="achieving-predictive-performance-parity"></a>結案預測的效能不一致性致歉

我們的第一個優先順序已達到 Azure 毫升和 Excel 的迴歸模型之間的不一致性致歉。  指定完全相同的資料，而且相同分割的訓練課程及測試我們想要達到預測的效能不一致性致歉 Excel 和 Azure 毫升之間的資料。   一開始失敗。 Excel 模型 outperformed Azure 毫升模型。   了解基底工具中的 [設定 Azure 毫升不足，已失敗。 同步處理後 Azure 毫升產品小組使用，我們獲得更好了解設定我們的資料集，需要基底，而且只兩個模型之間的不一致性致歉。  

### <a name="create-regression-model-in-excel"></a>在 Excel 中建立的迴歸模型
我們 Excel 迴歸用來在 Excel 分析工具箱中找到的標準的線性迴歸模型。 

我們會計算*意義絕對百分比錯誤*，並作為模型效能量值。  所需以使用 Excel 的工作模型的 3 個月。  我們引進的學習到 Azure 毫升實驗的最終了解需求有幫助的。

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>建立可比較實驗中 Azure 電腦學習  
我們會依照這些步驟來建立我們實驗中 Azure 毫升︰  

1.  上傳為 csv 檔案至 Azure 毫升 （很小的檔案） 的資料集
2.  建立新的實驗，並用於[資料集中的 [選取資料行][select-columns]模組，以選取 [在 Excel 中使用相同的資料功能   
3.  使用[分割的資料][split]模組 （*相對運算式*模式） 將資料分成完全相同的訓練設定為已在 Excel 中已完成  
4.  嘗試使用[線性迴歸][linear-regression]模組 （僅限預設選項），記錄，並比較的結果，我們 Excel 迴歸模型

### <a name="review-initial-results"></a>檢閱初始的結果
首先，Excel 模型清楚 outperformed Azure 毫升模型︰  

|   |Excel|Azure 毫升|
|---|:---:|:---:|
|效能|   |  |
|<ul style="list-style-type: none;"><li>調整 R 平方</li></ul>| 」 0.96 |N/A|
|<ul style="list-style-type: none;"><li>係數 <br />判定</li></ul>|N/A|   0.78<br />（低精確度）|
|意義絕對的錯誤 |  $9。 5 M|  $ 19.4 M|
|意義絕對錯誤 （%）|   6.03%|  12.2%

我們執行時我們的程序和結果開發人員及其資料科學家 Azure 毫升小組，他們快速提供一些實用的秘訣。  

* 當您使用[線性迴歸][linear-regression]中 Azure 毫升模組，提供兩種方法︰
    *  線上漸層深度︰ 可能會更適合的大規模問題
    *  一般的最小平方︰ 這是多數人在他們聆聽線性迴歸時，要考慮的方法。 小型資料集，一般的最小平方可能是較佳的選擇。
*  請考慮調整 L2 Regularization 粗細參數以提升效能。 根據預設，且我們的小型資料集設定為 0.001，我們將它設定為 0.005 以提升效能。    

### <a name="mystery-solved"></a>解決神秘 ！
我們套用建議，我們會達成相同的比較基準效能中使用 Excel 的 Azure 毫升為︰   

|| Excel|Azure 毫升 （縮寫）|Azure 毫升以最小平方和|
|---|:---:|:---:|:---:|
|標記的值  |實際工時 （數值）|相同|相同|
|是型學習者  |Excel]-> [資料分析]-> [迴歸分析|線性迴歸分析。|線性迴歸|
|是型學習者選項|N/A|預設值|一般的最小平方和<br />L2 = 0.005|
|資料集|26 個資料列 3 的功能、 1 的標籤。   所有的數字。|相同|相同|
|分割︰ 訓練|Excel 訓練測試 8 最後一個資料列] 上的第一次 18 資料列上。|相同|相同|
|分割︰ 測試|套用到最後一個 8 列的 Excel 迴歸公式|相同|相同|
|**效能**||||
|調整 R 平方|」 0.96|N/A||
|決定係數|N/A|0.78|0.952049|
|意義絕對的錯誤 |$9。 5 M|$ 19.4 M|$9。 5 M|
|意義絕對錯誤 （%）|<span style="background-color: 00FF00;">6.03%</span>|12.2%|<span style="background-color: 00FF00;">6.03%</span>|

此外，Excel 係數與比較適合 Azure 訓練模型中的功能加權︰

||Excel 係數|Azure 功能粗細|
|---|:---:|:---:|
|Intercept/壓到最低|19470209.88|19328500|
|功能 A|0.832653063|0.834156|
|功能 B|11071967.08|11007300|
|功能 C|25383318.09|25140800|

## <a name="next-steps"></a>後續步驟

我們想要使用 Excel 中的 Azure 毫升 web 服務。  我們商務分析師依賴 Excel，我們需要呼叫 Azure 毫升 web 服務的一列的 Excel 資料，並讓該傳回 Excel 預測的值的方式。   

我們也要最佳化我們模型中，使用 [選項] 及 [演算法 Azure 毫升提供。

### <a name="integration-with-excel"></a>使用 Excel 的整合
我們的解決方案是從訓練模型建立 web 服務 operationalize 我們 Azure 毫升迴歸模型。  在幾分鐘內建立 web 服務，並我們無法直接從 Excel 的預測的營收值傳回。    

*Web Services 儀表板*區段包含可下載的 Excel 活頁簿。  活頁簿隨附預先格式化 web 服務 API 和結構描述資訊內嵌。   當您按一下 [*下載 Excel 活頁簿*時，它會開啟，您可以將其儲存至本機電腦。    

![][1]
 
開啟的活頁簿，請將預先定義的參數複製到 [藍色] 區段中參數，如下所示。  一旦輸入參數會與 Excel 呼叫 AzureML web 服務預測計分標籤會顯示綠色的預測值區段中。  活頁簿會繼續建立預測訓練模型的輸入參數的所有資料列項目所根據的參數。   如需有關如何使用此功能的詳細資訊，請參閱[使用 Excel 中 Azure 電腦學習 Web 服務](machine-learning-consuming-from-excel.md)。 

![][2]
 
### <a name="optimization-and-further-experiments"></a>最佳化與進一步實驗
現在，我們使用我們 Excel 模型了比較基準，則我們直接移至最佳化我們 Azure 毫升線性迴歸模型。  我們使用模組[篩選型功能選擇][filter-based-feature-selection]改善我們的初始資料的選取範圍的項目，而且協助我們達成 4.6%的效能提升意義絕對的錯誤。   為未來專案中，我們將使用此功能無法儲存我們週在 [重複的資料屬性，以尋找最右邊的功能，可用於 modelling。  

接下來我們計畫包含[Bayesian]等其他演算法[bayesian-linear-regression]或[提高決策樹][boosted-decision-tree-regression]比較效能我們實驗中。    

如果您想要嘗試使用迴歸分析，良好的資料集，以嘗試是能源效率迴歸範例資料集，其中包含許多的數字的屬性。 資料集提供在毫升 Studio 中的範例資料集的一部分。  您可以使用各種不同的學習模組預測加熱載入或製冷載入。  下圖是效能比較不同的迴歸學習針對能源效率目標變數 Cooling 負載的資料集預測︰ 

|模型|意義絕對的錯誤|根平均值平方錯誤|相對絕對的錯誤|相對平方錯誤|決定係數
|---|---|---|---|---|---
|提高決策樹|0.930113|1.4239|0.106647|0.021662|0.978338
|線性迴歸 （漸層深度）|2.035693|2.98006|0.233414|0.094881|0.905119
|類神經網路迴歸分析|1.548195|2.114617|0.177517|0.047774|0.952226
|線性迴歸 （一般最小平方）|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>重要心得 

我們很多，從經驗執行 Excel 迴歸和 Azure 電腦學習實驗平行。 在 Excel 中建立的比較基準模型，並使用 Azure 毫升[線性迴歸]模型比較[linear-regression]幫助我們瞭解 Azure 毫升和我們發現商機以提升資料的選取項目和模型的效能。         

我們也找到，建議您要使用[篩選依據] 功能選取][filter-based-feature-selection]加速未來的預測專案。  將功能選擇套用至您的資料，您可以在 Azure 毫升中建立的改良的模型，更好的整體效能。 

可傳送預測分析預測從 Azure 毫升 excel systemically 允許大幅增加成功至主要的商務使用者對象提供結果的能力。     


## <a name="resources"></a>資源
為協助您使用的迴歸會列出一些資源︰  

* 在 Excel 中的迴歸分析。  如果您從未已嘗試在 Excel 中的迴歸分析，在此教學課程方便︰ [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* 迴歸與預測。  Tyler Chessman 所撰寫的部落格文章說明如何執行時間序列預測在 Excel 中，其中包含線性迴歸好入門的描述。 [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   一般的最小平方線性迴歸︰ 問題、 問題及陷阱。  簡介和迴歸討論︰ [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
