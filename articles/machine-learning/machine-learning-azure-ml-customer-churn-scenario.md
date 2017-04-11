<properties
    pageTitle="分析客戶變換使用電腦學習 |Microsoft Azure"
    description="開發來分析及計分客戶變換的整合式的模型的案例研究"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>分析客戶變換使用 Azure 電腦學習

##<a name="overview"></a>概觀
本主題提供客戶變換分析專案使用 Azure 電腦學習 Studio 內建的參考實作。 同時討論相關聯的一般模型，而解決工業客戶變換的問題。 我們也測量的使用電腦學習建立模型的精確度，我們評估進一步開發的指示。  

### <a name="acknowledgements"></a>通知

這項實驗開發，經過 Serge Berger 與 microsoft 本金資料科學家 Roger Barga，前身為 Microsoft Azure 電腦學習的產品經理。 Azure 說明文件小組非常感謝確認其專業知識，謝謝共用此白皮書 （英文）。

>[AZURE.NOTE] 無法可公開使用這個實驗所用的資料。 如需如何建立電腦學習模型變換分析的範例，請參閱︰ [Cortana 智慧圖庫](http://gallery.cortanaintelligence.com/)中的 [[電話變換模型範本](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>客戶變換的問題
企業和所有企業版︰ 類股消費者市場中有處理變換。 有時變換過多，而且會影響原則決策。 傳統的解決方案是預測高傾向 churners 和地址需求透過指引服務，行銷活動，或套用特殊 dispensations。 從產業和更特定的消費者叢集到另一個產業 （例如，電信） 中，這種方法可以視情況而定。

常見的因素是企業需要這些特殊客戶保留努力最小化。 因此，自然的方法就是分數變換機率每一位客戶和地址的前 n 個項目。 重要客戶可能獲利的文件。例如，在更複雜的情況下，profit 函數會採用期間的特殊 dispensation 候選選取範圍。 不過，這些考量是策略的用來處理變換全方位的組件。 企業也有層級和互動及好像客戶分割的成本，考慮帳戶風險 （及相關聯的風險了）。  

##<a name="industry-outlook-and-approaches"></a>產業 outlook 和方法
複雜的處理變換是產業的成熟的符號。 典型的範例是電信產業訂閱者經常從一個提供者，切換到另一個已知的位置。 此自動變換是主要考量。 此外，提供者所累積嚴重瞭解*變換驅動程式*，這些因素切換的磁碟機客戶。

例如，聽筒或裝置選項是變換行動電話為企業中的已知驅動程式。 如此一來，常用的原則是新的訂閱和付費升級現有客戶的完整價格 subsidize 話筒的價格。 若要取得新的折扣，接著，具有系統提示您提供者，若要調整其策略湧入到另一提供者的客戶以往導致此原則。

很話筒方案是變換的使很快模型目前話筒模型為基礎的因素。 此外，行動電話不是只建造裝置。這也是方式陳述式 （考慮 iPhone），且這些社交預測一般電信資料集的範圍。

最後的結果的模型就是您無法只要消除已知的原因變換制訂音效原則。 事實上，連續模型策略，包括傳統的模型，以量化類別 （例如決策樹） 的變數是**必要欄位**。

使用大型資料集的客戶，組織執行大型資料分析 （尤其是根據大型資料變換偵測） 為有效問題的方法。 您可以找到更多相關問題的建議，ETL] 區段中的變換大型資料的方法。  

##<a name="methodology-to-model-customer-churn"></a>模型客戶變換的方法
一般問題的解決程序來解決客戶變換描繪數字 1-3:  

1.  風險模型可讓您考慮動作會影響機率和風險。
2.  操作模型可讓您如何操作的層級可能會影響變換和客戶的機率存留時間值 (CLV)。
3.  此分析本身來擴大積極行銷活動的目標客戶進行最佳化優惠的區段質化分析。  

![][1]

此轉寄外觀的方法是將變換的最佳方式，但它隨附複雜度︰ 我們有開發多模型原型及追蹤模型之間的相依性。 可以封裝模型之間的互動，如下圖所示︰  

![][2]

*圖 4︰ 整合多模型原型*  

如果我們到客戶保持進行全面性的方法，模型之間的互動是金鑰。 每個模型一定會降低一段時間。因此，架構會隱含的循環 （類似清晰 DM 資料採礦標準，[***3***] 設定原型）。  

風險決策行銷分割/分解整體循環圖仍的一般的結構，可套用至多個商務問題。 變換分析是只要強式代表此群組的問題，因為它所表現不允許簡化預測解決方案複雜的商務問題的所有特性。 變換的新方法的社交方面不特別中醒目提示的方法，但社交方面封裝中模型原型，與任何模型中。  

感興趣的加法是顯示較大的資料分析。 今天建造和零售企業收集他們的客戶的詳細資料，我們可以輕鬆地預見需要使用多模型連線會變成一般趨勢，指定趨勢網際網路的項目等無所不在的裝置，讓使用多個層級的智慧解決方案的商務。  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>在電腦學習 Studio 實作模型原型
什麼是實作整合式的模型和計分方法的最佳方式提供剛剛所述的問題？ 在此區段中，我們會示範如何我們完成此使用 Azure 電腦學習 Studio。  

多模型的方法是必要的設計的變換全域原型時。 即使計分 （預測） 的一部分的方法應該多模型。  

下圖顯示原型我們所建立，其中採用預測變換電腦學習 Studio 中的四個計分演算法。 使用多模型的方法是不只以建立 ensemble 類別器增加精確度，但也來防範過度調整並改善預先設計的功能選取項目。  

![][3]

*圖 5︰ 的模型方法變換的原型*  

下列各節提供詳細原型計分使用電腦學習 Studio 我們實作的模型資訊。  

###<a name="data-selection-and-preparation"></a>資料選取項目和準備
使用的資料來建立模型和分數客戶已經從 CRM 垂直解決方案，取得模糊化保護客戶隱私權的資料。 資料包含在美國，8000 訂閱的相關資訊，它會合併三個來源︰ 佈建資料 （訂閱中繼資料）、 活動資料 （系統的使用狀況） 及客戶支援的資料。 資料不包含任何商務相關客戶; 的相關資訊例如，它不包含忠誠中繼資料或信用卡分數。  

為求，ETL 和清理程序的資料範圍外因為我們假設資料準備已完成其他地方。   

模型功能選項會根據初步的比較基數倍數計分的預測，使用的隨機樹系模組程序中所包含的設定。 在電腦學習 Studio 中執行，我們計算平均值、 median 及代表功能的範圍。 例如，我們新增彙總質化資料，例如使用者活動的最小值和最大值。    

我們也會擷取最新的六個月的暫時的資訊。 我們一年的分析資料，我們建立，即使沒有統計嚴重趨勢，在變換效果會大幅降低六個月後。  

最重要的點是整個程序，包括 ETL 功能選擇與模型實作，在電腦學習 Studio 中，使用 Microsoft Azure 中的資料來源。   

下面的圖表說明所用的資料。  

![][4]

*圖 6︰ 範例摘錄 （模糊化） 的資料來源*  

![][5]


*圖 7︰ 從資料來源中擷取的功能*
 
> 請注意，此資料是私人群組，因此無法共用模型與資料。
> 不過，如果類似的模型，使用可公開使用的資料，請參閱[Cortana 智慧圖庫](http://gallery.cortanaintelligence.com/)中嘗試此範例︰[電話客戶變換](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383)。
> 
> 若要進一步瞭解您可以在如何實作使用 Cortana 智慧套件變換分析模型，我們也會建議資深專案經理 Wee Hyong Tok[這段影片](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html)。 
> 

###<a name="algorithms-used-in-the-prototype"></a>使用在原型演算法

我們會使用下列四個電腦學習演算法來建立原型 （沒有自訂）︰  

1.  後勤迴歸分析 （長程）
2.  提高決策樹 (BT)
3.  平均的 perceptron （應用程式）
4.  支援向量電腦 (SVM)  


下圖說明實驗設計介面，亦即指出在其中建立模型的順序的一部分︰  

![][6]  


*圖 8︰ 在電腦學習 Studio 建立模型*  

###<a name="scoring-methods"></a>計分的方法
我們計分使用標記的訓練資料集的四個模型。  

我們也送出計分的資料集，以使用桌面版本的 SA 企業礦工 12 內建的比較模型。 我們開始計算精確度的 SA 模型和所有四個電腦學習 Studio 模型。  

##<a name="results"></a>結果
在此區段中，我們會提供我們發現模型，根據計分資料集的正確性。  

###<a name="accuracy-and-precision-of-scoring"></a>正確性和計分的精確度
一般而言，Azure 電腦學習中的實作位於前 SA 關於 10-15%（在下曲線區域或 AUC） 的精確度。  

不過，在變換最重要的公制是 misclassification 工資率︰ 也就是的前 n 個 churners 為預測類別器，其中實際上並未**不**變換尚未收到特殊的處理？ 下圖會比較所有模型此 misclassification 率︰  

![][7]


*圖 9: Passau 原型區域的下方曲線*

###<a name="using-auc-to-compare-results"></a>使用 AUC 比較結果
下曲線區域 (AUC) 是表示*separability*之間成績的負值母體的分配全域度量單位。 雖然類似傳統的收件者運算子特性 （巨鵬） graph 下方，但一個重要的差異，AUC 公制不需要您選擇臨界值。 不過，它會摘要結果**所有**可能的選項。 相反地，傳統巨鵬圖形垂直軸] 和 [false 正數的工資率，水平座標軸上顯示正數的工資率和分類閥值而有所不同。   

AUC 通常用於值得衡量不同的演算法 （或其他系統） 因為其允許以其 AUC 值進行比較的模型。 這是常用的方法，例如 meteorology 和 biosciences 產業。 因此，AUC 代表評估器效能的常用工具。  

###<a name="comparing-misclassification-rates"></a>比較 misclassification 工資率
我們使用每 8000 個訂閱的 CRM 資料比較問題的資料集的 misclassification 比率。  

-   SA misclassification 工資率為 10-15%。
-   電腦學習 Studio misclassification 工資率為 15 20%的頂端 200 300 churners。  

電信產業，請務必地址有最高風險變換提供他們的指引對服務或其他特殊的處理這些客戶。 在該方面，電腦學習 Studio 實作達成與 SA 模型的結果。  

以相同的 token，精確度的考量優先於精確度，因為我們最感興趣正確分類潛在 churners。  

下列圖表從維基百科描述生動、 方便瞭解圖形中的關聯性︰  

![][8]

*圖 10︰ 權衡正確性和精確度*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>提高決策樹模型的正確性和精確度結果  

下圖顯示計分使用電腦學習原型提高決策樹模型，這是正確的四個模型之間的原始結果︰  

![][9]

*圖 11︰ 提高決策樹模型特性*

##<a name="performance-comparison"></a>效能比較
我們比較資料已計分使用電腦學習 Studio 模型和比較模型使用桌面版本的 SA 企業礦工 12.1 所建立的速度。  

下表摘要列出演算法的效能︰  

*資料表 1。一般演算法的效能 （精確度）*

| 長程|BT|應用程式|SVM|
|---|---|---|---|
|平均模型|最佳的模型|泡|平均模型|

裝載於電腦學習 Studio 15 25%的速度執行，但正確性 outperformed 的 SA 主要已 par 模型。  

##<a name="discussion-and-recommendations"></a>討論區與建議事項
在電信產業，數種作法應有分析變換，包括︰  

-   衍生四種基本類別的指標︰
    -   **實體 （例如，訂閱）**。 佈建的基本資訊訂閱及/或客戶的變換的主旨。
    -   **活動**。 取得相關的實體，例如，登入的數字的所有可能的使用狀況資訊。
    -   **客戶支援部門**。 蒐集表示訂閱是否有問題或與客戶支援的互動的客戶支援記錄的資訊。
    -   **競爭力和公司的資料**。 取得關於客戶任何可能的資訊 （例如，可以從無法使用或追蹤）。
-   使用 [磁碟機] 功能選取範圍的重要性]。 這表示提高決策樹模型永遠是提出的方法。  

使用這些四個類別的建立簡單*確定*的方法，並根據索引格式合理的因素，每個類別，應該可以滿足識別風險的變換客戶的圖例。 很抱歉，此概念看起來好像，雖然是 false 的瞭解程度。 原因是，變換暫時的效果，而造成變換因素通常是暫時性狀態。 什麼引導考慮今天離開客戶可能不同明天]、 和一定會不同六個月從現在。 因此， *probabilistic*模型則需要。  

此重要的觀察通常會忽略企業，通常要分析慣用的商務智慧導向方法，大多因為它是更銷售，並允許乾淨的簡單自動化撥入。  

不過，使用電腦學習 Studio 的自助分析的承諾，是資訊的四個類別，來評分部門，以變成電腦學習變換寶貴的來源。  

另一個令人驚豔傳入 Azure 電腦學習功能至存放庫的預先定義的模組，可在已新增自訂模組的能力。 這項功能，基本上，建立有機會選取文件庫，並建立垂直市場的範本。 這是在市場中的 Azure 電腦學習重要區別。  

我們希望繼續本主題會在未來，尤其是相關大型資料分析。
  
##<a name="conclusion"></a>結束時
本文將說明要使用一般架構來自訂處理常見的問題的客戶變換直覺式的方法。 我們視為原型計分模型，並使用 Azure 電腦學習實作。 最後，我們要評估的正確性和上比較演算法中 SA 並原型解決方案的效能。  

**如需詳細資訊︰**  

這本白皮書沒有幫助您嗎？ 請提供您的意見反應。 告訴我們的 1 （差） 為 5 （很好） 與您覺得這本白皮書，為什麼有您指定此評等嗎？ 例如︰  

-   您評等，高因為良好的範例，很好的螢幕擷取畫面，清除撰寫，或另一個原因？
-   您評分系統狀況不佳的範例、 模糊的螢幕擷取畫面或語意不明撰寫低？  

此意見反應會協助我們改善白皮書我們發行的品質。   

[傳送意見反應](mailto:sqlfback@microsoft.com)。
 
##<a name="references"></a>參照
[1]的預測狀況分析︰ 超過預測，西部 McKnight，資訊管理、 年 7 月 8 2011 p.18 20。  

[2]維基百科文章︰[正確性和精確度](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3][清晰 DM 1.0︰ 逐步資料採礦指南](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4][大型資料行銷︰ 更有效地加入您的客戶及磁碟機值](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5][電話變換模型範本](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) [Cortana 智慧庫](http://gallery.cortanaintelligence.com/)中 
 
##<a name="appendix"></a>附錄

![][10]

*在變換原型簡報的圖 12︰ 快照集*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
