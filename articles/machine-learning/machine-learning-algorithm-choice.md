<properties
    pageTitle="如何選擇機器學習演算法 |Microsoft Azure"
    description="如何在叢集、 分類或迴歸實驗中選擇的監督及未受監督學習 Azure 機器學習演算法。"
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>如何選擇演算法的 Microsoft Azure 機器學習

"學習演算法我應該使用何種機器？"問題的答案 一定是"它取決。 」 大小、 品質、 和資料的性質而定。 它取決於您要執行的答案。 指示您使用的電腦入已翻譯的演算法的數學而定。 和其取決於您已對多少時間。 即使科學家最經驗的資料無法判斷所使用的演算法將執行適合之前嘗試它們。

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>機器學習演算法速記表

**Microsoft Azure 機器學習演算法速記表**可協助您選擇學習 Microsoft Azure 機器學習文件庫的演算法從您預測性分析解決方案的演算法的右機器。
本文會引導您如何使用它。

> [AZURE.NOTE] 若要下載說明文並遵循本文以及，移至[機器學習 Microsoft Azure 機器學習 Studio 的演算法說明文](machine-learning-algorithm-cheat-sheet.md)。

此說明文記住有非常特定的對象︰ 開頭的資料科學家與 charlie 層級機器學習嘗試在 Azure 機器學習 Studio 中開始使用選擇演算法。 這表示它進行一些一般化和 oversimplifications，但它會指向您在安全的方向。 它也表示有許多未列在這裡的演算法。 為 Azure 機器學習成長包含一組更完整的可用的方法，我們將新增它們。

這些建議所編譯的意見反應及從許多資料科學家與機器學習專家的秘訣。 我們沒有同意所有項目，但已嘗試將粗略共識達到我們會員。 大部分的 disagreement 陳述式的開頭"它取決..."

### <a name="how-to-use-the-cheat-sheet"></a>如何使用說明文

閱讀做為圖表上的路徑和演算法標籤"的*&lt;路徑標籤&gt;*使用*&lt;演算法&gt;*。" 例如，"*速度*使用*兩種類別後勤迴歸*。 」 將套用有時一個以上的分支。
有時不會完美調整。 他們是設為規則縮圖的建議，讓它要完全不用擔心適用對象。
我打電話與前述的數個資料科學家尋找非常最佳演算法僅確定方法是嘗試所有其。

以下是符合原先的假設嘗試針對相同的資料的數種演算法並比較結果從[Cortana 智慧 Gallery](http://gallery.cortanaintelligence.com/)範例：[比較多重類別相關性︰ 字母辨識](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92)。

>[AZURE.TIP] 下載及列印圖表提供的機器學習 Studio 功能的概觀，請參閱[Azure 機器學習 Studio 功能概觀 （英文） 圖表](machine-learning-studio-overview-diagram.md)。

## <a name="flavors-of-machine-learning"></a>下列類別的機器學習

### <a name="supervised"></a>指導

監督的學習演算法進行預測根據一組的範例。 對於執行個體歷程股價可用來在未來價格障礙猜測。 用於訓練每個範例會標示為 [感興趣的值--在這個案例中內建的價格。 監督的學習演算法會尋找這些值標籤中的模式。 它可以使用任何可能是相關的資訊 — 一週、 商品最、 公司的財務資料、 業界的類型、 中斷 geopolicitical 事件的目前狀態的天 — 和每個演算法尋找的不同類型的模式。 演算法找到最佳模式之後可以，它會使用該模式來進行預測的未標示的測試資料 — 明天的價格。

這是機器學習常用而且實用類型。 有一個例外的全部模組中 Azure 機器學習被指導的學習演算法。 有數種特定類型的監督學習表示內 Azure 機器學習︰ 分類、 迴歸、 及異常偵測。

* **分類**。 資料所使用之時預測類別，監督的學習也稱為分類。 當將映像指派為 'cat' 或 '狗狗' 的圖片是這樣。 只有兩個選擇時，這被呼叫**兩個類別**或**二項分類**。 如有多個類別為預測 NCAA 年 3 月瘋狂錦標賽輸贏時，這個問題稱為**多重類別分類**。

* **迴歸**。 值要預測，與股價相同，監督的學習稱為迴歸。

* **異常偵測**。 有時的目標是識別只是不尋常的資料點。 例如，在詐騙偵測任何高度不尋常信用卡耗費模式都有疑問。 可能的變化是讓許多與因此一些、 不以了解哪些詐騙活動合適的訓練範例如下所示。 異常偵測採取的方法是只要了解哪些標準活動起來 （使用歷程記錄非詐騙交易） 並找出任何會有明顯不同的項目。

### <a name="unsupervised"></a>未受監督

在未受監督學習資料點都沒有與其相關的標籤。 而未受監督的學習演算法的目標是組織中辦法資料或其結構描述。 這可能表示合叢集或尋找不同的方法，讓它出現簡化或更多組織性查看複雜的資料。

### <a name="reinforcement-learning"></a>增援學習

在增援學習、 演算法取得選擇每個資料點的回應中的巨集指令。 學習演算法也會收到獎勵訊號在短時間之後，指出如何良好決策時。
在此基礎演算法會修改以達到最高獎勵其策略。 目前有任何增援學習在 Azure 機器學習演算法模組。 增援學習在中很常見機械裝置，其中一個點的接收器讀取在時間中一組為的資料點，而演算法必須選擇機器人的下一個巨集指令。 它也是自然符合的項目皆網際網路應用程式。

## <a name="considerations-when-choosing-an-algorithm"></a>選擇演算法時的考量

### <a name="accuracy"></a>正確性

取得最精準的回覆可能不一定需要。
有時近似值是適當取決於想要使用它以進行。 如果大小寫的您可能會能夠更多的大約方法堅持大幅提高剪下處理時間。 更多的大約方法的另一個優點是其自然通常會以避免[overfitting](https://youtu.be/DQWI1kvmwRg)。

### <a name="training-time"></a>訓練時間

分鐘或幾小時訓練模型所需的數目而異詳情之間演算法。 訓練時間通常最繫結至正確性是 — 一個通常隨附其他。 此外，有些演算法是更多區分的資料點數比其他人。
時間限制時它可以在尤其是當資料組大磁碟機演算法的選擇。

### <a name="linearity"></a>線性

許多機器學習演算法進行的線性使用。 線性分類演算法假設類別可以隔開直線 （或其較高維度類比）。 這些包括後勤迴歸及支援向量機器 （如 Azure 機器學習中實作）。
線性迴歸演算法假設資料趨勢遵循直線。 這些假設都不正確的一些問題，但是在其他人時讓停機的正確性。

![非線性類別 bounday][1]

***非線性類別界限****-依賴線性分類演算法會導致低精確度*

![具有非線性趨勢資料][2]

***具有非線性趨勢資料****-使用線性迴歸方法會產生比需要太多較大的錯誤*

儘管其危險、 線性演算法是非常常用攻擊的第一個線。 他們往往以演算法簡單且快速訓練。

### <a name="number-of-parameters"></a>參數數目

參數是資料科學家取得開啟演算法設定時的參數。 他們是會影響的演算法的行為，例如錯誤容忍或反覆運算次數或選項之間的演算法的行為方式變體數目的數字。 訓練時間和演算法的正確性可以有時很容易受到快速右邊的設定。 一般而言，大量參數的演算法需要大部分試用版與 error，以尋找良好的組合。

或者，會自動嘗試在您選擇列傳精確度所有參數組合的 Azure 機器學習中有[參數全面](machine-learning-algorithm-parameters-optimize.md)模組區塊。 雖然這是以確定您已合併參數空間更好的方式，訓練模型所需時間大幅增加參數的數量。

優點是通常有許多參數表示演算法有更大的彈性。 它通常可以達到非常好的正確性。 提供您可以找到正確的參數設定組合。

### <a name="number-of-features"></a>許多功能

特定類型的資料，許多功能可以是非常大相較於資料點數。 這通常是像 genetics 或文字資料。 大量的功能可以無法運作進行訓練 unfeasibly 長的時間部分學習演算法。 支援向量機器是特別適用於此案例 （如下所示）。

### <a name="special-cases"></a>特殊案例

某些學習演算法進行特定假設需結構的資料或想要的結果。 如果可找到適合您需求的其中一個，可讓您更多有用的結果、 更精確預測或更快的訓練時間。

|**演算法**|**正確性**|**訓練時間**|**線性**|**參數**|**附註**|
|---|:---:|:---:|:---:|:---:|---|
|**兩個類別分類**| | | | | |
|[後勤迴歸](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[決策樹系](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[決策叢林](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|低使用量|
|[已擴大的決策樹](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|大型的記憶體耗用量|
|[類神經網路](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[其他自訂項目可能](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[平均的 perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[支援向量機器](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|適用於大型的功能集|
|[在本機上深層支援向量機器](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|適用於大型的功能集|
|[機率分類的點機器](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**多重類別分類**| | | | | |
|[後勤迴歸](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[決策樹系](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[決策叢林](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|低使用量|
|[類神經網路](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[其他自訂項目可能](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[一個 v 全部](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|請參閱所選取的兩個類別方法的屬性|
|**迴歸**| | | | | |
|[線性](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayesian 線性](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[決策樹系](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[已擴大的決策樹](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|大型的記憶體耗用量|
|[fast 的樹系 quantile](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|散發而不是點預測|
|[類神經網路](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[其他自訂項目可能](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[波氏](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|嚴格來說記錄檔-線性。 針對預測計數 （英文）|
|[序數](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|針對預測排名排序|
|**異常偵測**| | | | | |
|[支援向量機器](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|特別是很好的大型的功能集|
|[PCA 型異常偵測](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K 方法](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|叢集的演算法|


**演算法內容：**

**●** -顯示實用的正確性、 fast 訓練時間以及使用線性

**○** -顯示良好的正確性及中等訓練時間

## <a name="algorithm-notes"></a>演算法備忘稿

### <a name="linear-regression"></a>線性迴歸

如前文所述，[線性迴歸](https://msdn.microsoft.com/library/azure/dn905978.aspx)將適合行 （或平面底板或 hyperplane） 的資料集。 雖然 workhorse、 簡單且快速，但有可能會過度簡化的一些問題。
檢查以下[線性迴歸教學課程](machine-learning-linear-regression-in-azure.md)。

![使用線性趨勢資料][3]

***使用線性趨勢資料***

### <a name="logistic-regression"></a>後勤迴歸

雖然困惑包含 '迴歸' 的名稱、 後勤迴歸是實際上[兩類別](https://msdn.microsoft.com/library/azure/dn905994.aspx)和[multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx)分類的強大工具。 是快速又簡單。 使用事實的 '-而不是以直線成形的曲線讓變得自然適合餘數成群組的資料。 後勤迴歸提供線性類別界限，所以當您使用，請確定線性近似值是您可以使用 live。

![後勤迴歸只是一個功能使用的兩個類別資料][4]

***只是一個功能使用的兩個類別資料後勤迴歸****-類別界限是之點的後勤曲線剛才為接近這兩種類別*

### <a name="trees-forests-and-jungles"></a>樹狀目錄、 樹系、 及 jungles

決策樹系 （[迴歸](https://msdn.microsoft.com/library/azure/dn905862.aspx)、[兩個類別](https://msdn.microsoft.com/library/azure/dn906008.aspx)，以及[multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)）、 決策 jungles （[兩個類別](https://msdn.microsoft.com/library/azure/dn905976.aspx)和[multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)） 和已擴大的決策樹 （[迴歸](https://msdn.microsoft.com/library/azure/dn905801.aspx)和[兩個類別](https://msdn.microsoft.com/library/azure/dn906025.aspx)） 所有取決於決策樹學習概念基礎機器。 有許多決策樹的變化，但其所有執行同樣的事情 — 可能功能空間細分成區域多半相同的標籤。 這些可以是類別的區域或常數值，根據是否已分類或迴歸作業的一致。

![決策樹會細分功能空間][5]

***決策樹細分功能空間為大致上統一值的區域 （英文）***

因為功能空間可以細分為任意小區域 （英文）、 很容易假設有一個資料點的個別地區為不夠自助式餘數 — 極端膨脹的範例。 為避免發生這種情況，樹狀結構一大組都是透過建構具有特殊採取的數學照顧樹狀結構不相互關聯。 此"決策樹系 」 的平均是可以避免膨脹樹狀目錄。 決策樹系可以使用大量記憶體。 決策 jungles 會使用提供較少的記憶體但是將其中的訓練時間較長的 variant。

已擴大的決策樹來避免膨脹管制他們可以細分的次數和如何幾個資料點允許每個區域中。 演算法建構樹狀結構，每一種可學習補償錯誤之前的樹狀目錄的左的順序。 結果是使用大量記憶體與不同非常正確學習者。 如需完整技術的說明，查看[Friedman 的原始的紙張](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf)。

[Fast 的樹系 quantile 迴歸](https://msdn.microsoft.com/library/azure/dn913093.aspx)是特殊案例想知道不僅一般 （中性色系） 資料的值內地區，但也其 quantiles 的形式散發的決策樹的變化。

### <a name="neural-networks-and-perceptrons"></a>類神經網路與 perceptrons

就 brain-迫不及待來學習涵蓋[multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx)、[兩個類別](https://msdn.microsoft.com/library/azure/dn905947.aspx)和[迴歸](https://msdn.microsoft.com/library/azure/dn905924.aspx)問題的演算法是類神經網路。 他們甚至無限的各種但類神經的網路內 Azure 機器學習所有導向非循環圖表的表單。 這表示的輸入的功能傳送向前 （永不往後） 到的圖層順序之前要轉換成輸出。 在每個圖層中，輸入所加權中各種組合、 加總，和傳遞至下一個圖層。 此組合的簡單計算產生了複雜的類別界限和資料趨勢看似識別常數的能力。 這類多分層網路執行 fuels 是過多的技術產品之報告與小說"深入學習"。

此高效能不會跳免費，上述。 類神經的網路可以對搜尋的訓練，特別是針對功能大量的大型資料集耗時。 他們也有更多參數大於大部分演算法，這表示參數到許多展開訓練時間。
與人員想要[指定自己網路結構](http://go.microsoft.com/fwlink/?LinkId=402867)那些 overachievers、 各種可能性會 inexhaustible。

<a name="boundaries-learned-by-neural-networks6"></a>![類神經網路所學到的界限][6]
---------------------------

***學會類神經網路所限制可複雜且不正常空格***

[兩個類別平均 perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx)是急速訓練時間類神經網路答覆。 它會使用提供線性類別限制網路結構。 會依今天的標準幾乎基本但已使用強行長歷程記錄，並為規模不致於過了解快速。

### <a name="svms"></a>SVMs

支援向量機器 (SVMs) 尋找分隔依類別為盡可能寬邊界的邊界。 時不能清楚分隔的兩種類別，演算法找到他們可以最佳界限。 當以 Azure 機器學習撰寫、[兩個類別 SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx)具有直線只執行此動作。 （在 SVM 說話它使用線性核心）。因為它讓此線性近似值，即可以許多快速地執行。 其中其確實發揮功效是功能鮮明像文字或 genomic 的資料。 在下列情況下 SVMs 是能夠更快速地與具有較少膨脹比大部分其他演算法，除了需要適度少量記憶體的個別類別。

![支援向量機器類別界限][7]

***一般支援向量機器類別界限上將最大化分隔兩種類別的邊界***

Microsoft 參考資料的其他產品、[兩個類別本機深入 SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx)是效率的會保留在大部分的速度與記憶體線性版本的 SVM 非線性 variant。 很理想的情況下線性方法不會授與足夠正確的答案。 開發人員會保留其 fast 由問題分成一堆小型線性 SVM 問題。 閱讀這些取出關閉此技巧的方式的詳細資料[完整描述](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf)。

使用非線性 SVMs 聰明副檔名，[其中一個類別 SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx)繪製變得更密切概述整個資料集界限。 很有用的異常偵測。 任何新的資料點遠以外的界限上都不尋常不夠是值得。

### <a name="bayesian-methods"></a>Bayesian 方法

Bayesian 方法具有高度取捨品質︰ 他們避免膨脹。 其執行這項作業來進行一些假設事先相關的回覆可能散佈。 此方法的另一個副產品為他們有很少參數。 Azure 機器學習具有分類 （[兩個類別機率分類的點機器](https://msdn.microsoft.com/library/azure/dn905930.aspx)） 和迴歸 （[Bayesian 線性迴歸](https://msdn.microsoft.com/library/azure/dn906022.aspx)） 這兩個 Bayesian 演算法。
請注意下列假設資料可以分割或配合使用以直線。

在歷程記錄的附註，機率分類的點機器已在 Microsoft Research 開發。 擁有背後它們某些特別漂亮理論工時。 興趣的 student 都會導入[JMLR 中的原始文章](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf)和[由 Chris 銘見解部落格](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx)。

### <a name="specialized-algorithms"></a>特殊化的演算法

如果您有非常特定的目標可能幸運。 Azure 機器學習集合內有專門排名預測 （[序數迴歸](https://msdn.microsoft.com/library/azure/dn906029.aspx)）、 計數預測 （[波氏迴歸](https://msdn.microsoft.com/library/azure/dn905988.aspx)） 和異常偵測 （一個根據[主要元件分析](https://msdn.microsoft.com/library/azure/dn913102.aspx)與一個根據[支援向量機器](https://msdn.microsoft.com/library/azure/dn913103.aspx)s） 的演算法。
而且沒有單獨叢集演算法，以及 （[K 表示](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)）。

![PCA 型異常偵測][8]

***PCA 型異常偵測****-大部分的資料可分為 stereotypical 通訊; 偏離大幅提高通訊群組會有疑問的點*

![資料集而群組在一起使用 K 方法][9]

***資料集組織成使用 K 表示 5 個叢集***

也有 ensemble[一 v 全部 multiclass 類別器](https://msdn.microsoft.com/library/azure/dn905887.aspx)，其中 N 類別分類問題分成 N-1 二類別分類問題。 正確性、 訓練時間及線性屬性取決於所使用的兩個類別相關性。

![若要建立三個類別類別器結合兩個類別相關性][10]

***若要建立三個類別類別器結合兩個兩個類別相關性***

Azure 機器學習也包含的存取權的[Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf)標題底下強大的機器學習架構。
Vw 為沒有分類這裡，因為它可了解分類和迴歸問題而且即使可以了解從部分的未標示的資料。 您可以將其設定為使用任一數學習演算法、 遺失函數及最佳化演算法。 它被專為基礎而向上有效率且平行便可極快。 它會處理以一點明顯過大的功能集。
啟動和點擊由 Microsoft 參考資料的專屬 John Langford、 vw 為是股票 car 演算法之欄位中的公式一個項目。 不是每個問題適合 vw 為，但如果其他沒有，可達到其介面上的學習曲線值得。 它也有可為[獨立的開放原始碼](https://github.com/JohnLangford/vowpal_wabbit)以數種語言。


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
