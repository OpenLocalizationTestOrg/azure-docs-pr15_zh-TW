<properties
    pageTitle="在電腦學習 Studio 中使用範例資料集 |Microsoft Azure"
    description="用於毫升 Studio 中包含的範例模型中的資料集的描述。 您可以使用您的實驗這些範例資料集。"
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
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Azure 電腦學習 Studio 中使用範例資料集

[top]: #machine-learning-sample-datasets

當您建立新的工作區中 Azure 電腦學習時，預設會包含範例資料集和實驗的數字。 許多這些範例資料集，可使用範例模型[Azure Cortana 智慧圖庫](http://gallery.cortanaintelligence.com/)中，與其他人都包含在內的各種類型的資料通常用於電腦學習的範例。

這些資料集的部分，可在 Azure Blob 儲存體。 這些資料集下表提供的直接連結。 您也可以使用[匯入資料]在您實驗中使用這些資料集[import-data]模組。

下列範例資料集的其餘部分都會在**儲存的資料集**模組調色盤左邊的實驗畫布中當您開啟或建立新的實驗毫升 Studio 中。
您可以在自己的實驗中使用這些資料集的任何拖曳到您的實驗畫布。


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>資料集的名稱</th>
  <th align=left>資料集的描述</th>
</tr>

<tr>
  <td valign=top>限制級人口普查收入二進位分類資料集</td>
  <td valign=top>
1994 人口普查資料庫，以調整的收入索引的 > 100 16 的年齡透過使用工作成人子集。<p> </p><b>使用方法︰</b>分類使用統計資料預測人員是否賺取 50 K 超過一年的人員。<p> </p><b>相關的參考資料︰</b> Kohavi，r，Becker，b，(1996)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>機場代碼資料集</td>
  <td valign=top>
美國機場代碼。<p> </p>此資料集包含一個資料列的每個美國機場，提供機場識別碼和以及位置城市和州名的名稱。
  </td>
</tr>

<tr>
  <td valign=top>汽車價格資料 (Raw)</td>
  <td valign=top>
請汽車的相關資訊，並模型，包括價格，例如柱和 MPG，以及保險風險成績的功能。<p> </p>風險成績與自動價格最初相關聯，，然後調整 [實際已知為 symboling actuaries 程序中的風險。 3 值表示自動會有風險，-3 值便是很可能是因為安全。<p> </p><b>使用方法︰</b>預測風險分數所使用的迴歸或 multivariate 分類的功能。 <p> </p><b>相關的參考資料︰</b> Schlimmer、 J.C. (1987)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Safetystockllevel 租賃 UCI 資料集</td>
  <td valign=top>
維護 safetystockllevel 租賃網路，州 DC 中的大寫 Bikeshare 公司的實際資料為基礎的 UCI Safetystockllevel 租賃資料集。<p> </p>資料集每天 2011年與 2012，針對 17,379 列總計的每小時有一個資料列。 每小時 safetystockllevel 租金範圍是從 1 977。

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Bill Gates RGB 圖像</td>
  <td valign=top>
可公開使用圖像檔案轉換成 CSV 資料。<p> </p>在 [<strong>色彩量化使用 K 表示叢集</strong>模型詳細資料頁面上提供轉換圖像的程式碼。
  </td>
</tr>

<tr>
  <td valign=top>含捐贈資料</td>
  <td valign=top>
含 Transfusion 服務中心的 Hsin 中國連縣/市，台灣的含捐贈者資料庫中的資料子集。<p> </p>捐贈者的資料包含自上次捐贈月），頻率，或者捐贈、 自上次捐贈，時間的總數量的捐贈含量。<p> </p><b>使用方法︰</b>目標是要透過分類預測是否捐贈者捐贈含年 3 月 2007，其中 1 會指出捐贈者期間的目標期間和 0 中非捐贈者。 <p> </p><b>相關的參考資料︰</b> Yeh I.C.，(2008)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 <p> </p>Yeh，我-Cheng Yang、 k-Jang 和 Ting、 點選 Ming，「 知識庫探索使用二項分配順序 RFM 模型 」 應用程式，2008年專家系統<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>從 Amazon 活頁簿檢閱</td>
  <td valign=top>
在 Amazon，從 amazon.com 網站所大專院校版的賓州研究 （<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">舉動</a>） 中的活頁簿的評論。 請參閱 [參考資料 （英文） 中，「 傳記、 Bollywood，發展方塊和 Blenders︰ 網域配接舉動分類的 「 John Blitzer、 標記 Dredze 和泰 Pereira;關聯的計算語意 (ACL)，2007年。<p> </p>原始資料集有 975 K 檢閱與 1、 2、 3、 4 或 5 的排名。 檢閱已以英文，從時間週期 1997年 2007年。 此資料集已向下取樣到 10 K 評論。
  </td>
</tr>

<tr>
  <td valign=top>Breast cancer 資料</td>
  <td valign=top>
三個 cancer 相關資料集經常出現在電腦學習資料 Oncology 學會所提供的其中一個。 結合關於 300 的時候範例的診斷資訊實驗室分析功能。<p> </p><b>使用方法︰</b>分類 cancer 的類型，根據 9 屬性，其中一些線性以及部分會分類。 <p> </p><b>相關的參考資料︰</b> Wohlberg、 W.H.、 街道、 W.N.，與 Mangasarian，O.L. (1995)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Breast Cancer 功能 <td valign=top>
資料集包含的資訊 102 K 可疑區域 （候選項目） X-ray 圖像，每個由 117 功能描述。 功能專屬，而且資料集建立者 （Siemens 保健） 不顯示其意義。 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Breast Cancer 資訊</td>
  <td valign=top>
資料集包含 X-ray 影像的每個可疑的區域的其他資訊。 每個範例會提供資訊 (例如標籤，病患 ID，座標修補程式相對於整個圖像) 瞭解中 Breast Cancer 功能資料集的對應資料列數字。 每個病人有的一些範例。 有 cancer 病患的一些範例是正數且部分負數。 沒有 cancer 病患的所有的範例是負數。 資料集有 102 K 範例。 資料集偏差結果，0.6%點的是正值，其餘是負數。 以 Siemens 保健已提供資料集。
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>共用 CRM Appetency 標籤</td>
  <td valign=top>
從 KDD 杯 2009年客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>) 的標籤。
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>共用 CRM 變換標籤</td>
  <td valign=top>
從 KDD 杯 2009年客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>) 的標籤。
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>共用的 CRM 資料集</td>
  <td valign=top>
此資料是來自 KDD 杯 2009年客戶關係預測挑戰 (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>)。<p> </p>資料集包含 [法文電信公司橘色 50 K 客戶。 每一位客戶具有 230 匿名的功能，而其中 190 是數字，且分類 40。 功能是非常稀疏。
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>共用 CRM Upselling 標籤</td>
  <td valign=top>
從 KDD 杯 2009年客戶關係預測挑戰 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>) 的標籤。
  </td>
</tr>

<tr>
  <td valign=top>能源效率迴歸分析資料</td>
  <td valign=top>
模擬的能源設定檔，根據 12 不同建置圖形的集合。 建築物不同的以 8 的功能，例如 glazing 區域、 glazing 區域分配，與方向來區分。<p> </p><b>使用方法︰</b>使用迴歸或分類預測評等根據為其中一個兩個實際值回應能源效率。 多個類別分類，是將四捨五入至最接近的整數回應變數。 <p> </p><b>相關的參考資料︰</b> Xifara，a 與 Tsanas，A (2012)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>航班延遲資料</td>
  <td valign=top>
旅客航班-時間效能資料取自美國部門的交通 （<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">時間</a>） 的 TranStats 資料集合。<p> </p>資料集涵蓋期間年 4 月的 2013年。 上傳至 Azure 毫升 Studio 之前, 資料集處理，如下所示︰<ul><li>資料集已涵蓋只 70 忙碌機場在 [美國大陸篩選</li><li>已取消的班機已標示為超過 15 分鐘的延遲</li><li>轉接的班機所篩選出</li><li>選取下列資料行︰ 年、 月、 DayofMonth、 DayOfWeek、 公司、 OriginAirportID、 DestAirportID、 CRSDepTime、 DepDelay、 DepDel15、 CRSArrTime、 ArrDelay、 ArrDel15、 已取消</li></ul>
</td>
</tr>

<tr>
  <td valign=top>航班-時間效能 （原始）</td>
  <td valign=top>
飛機抵達和出發美國從年 10 月 2011年中的記錄。<p> </p><b>使用方法︰</b>預測飛航的延遲狀況。 <p> </p><b>相關的參考資料︰</b>從的交通<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>美國部門。
  </td>
</tr>

<tr>
  <td valign=top>樹系，則會啟動資料</td>
  <td valign=top>
包含天氣的資料，例如溫度和濕度索引和風速從東北葡萄牙，加上樹系引發的記錄的區域。<p> </p><b>使用方法︰</b>這是一個難以迴歸任務，目的是預測樹系引發 [燒錄] 區域。 <p> </p><b>相關的參考資料︰</b> Cortez，p，與 Morais，A (2008)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 <p> </p>[Cortez 和 Morais，2007年]P Cortez 和 A Morais。 若要使用 Meteorological 資料預測樹系引發資料採礦的方法。 在 [j。 Neves，m f 鍵。 亭和 j。 Machado Eds.，人工地智慧，訴訟 13 EPIA 2007-葡萄牙文會議上人工地智慧，年 12 月，Guimarães，葡萄牙至 512-523，2007年的新趨勢。 APPIA ISBN 13 978-989-95618-0-9。 在︰ <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>。
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>德文信用卡 UCI 資料集</td>
  <td valign=top>
使用 german.data 檔案 UCI Statlog （德文信用卡） 資料集 （<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + 德文 + 信用卡 + 資料</a>）。<p> </p>資料集將分類所描述的一組的屬性，為 [低] 或 [高信用卡風險的人員。 每個範例代表人員。 有 20 功能，數字和分類，以及二進位標籤 （信用卡風險值）。 高信用卡風險項目有標籤 = 2，低信用卡風險項目有標籤 = 1。 將錯誤分類為高低風險範例的成本會是 1，而成本將錯誤分類為低高風險範例是 5。
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB 影片標題</td>
  <td valign=top>
資料集包含已評等 Twitter 推文中的影片的相關資訊︰ IMDB 影片識別碼，電影名稱與內容類型、 生產年。 在資料集中有 17 K 影片。 資料集已採用紙張 」 s 鍵。 Dooms、 T De Pessemier 和 L Martens。 MovieTweetings︰ 從 Twitter 收集評等資料集的影片。 專題 Crowdsourcing 和人力計算 Recommender 系統，在 RecSys 2013 CrowdRec。 」
  </td>
</tr>

<tr>
  <td valign=top>虹彩兩個類別的資料</td>
  <td valign=top>
這可能是圖樣辨識資料中找到最佳已知的資料庫。 資料就會比較小，50 範例每個瓣花瓣從三個虹彩種類的度量單位。<p> </p><b>使用方法︰</b>預測虹彩類型從 [度量單位。  <p> </p><b>相關的參考資料︰</b> Fisher，R.A. (1988)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>影片推文</td>
  <td valign=top>
資料集是影片 Tweetings 資料集的擴充的版本。 資料集有影片，從完善的傳出 tweets Twitter 上擷取的 170 K 評的等。 每個執行個體代表貼入 tweet，而是，表示 tuple︰ 使用者識別碼、 IMDB 影片識別碼、 評等、 時間戳記、 numer 的最愛的這項貼入 tweet，以及 retweets 此推文的數目。 資料集已提供 a 說、 S Dooms、 B Loni 和 D Tikk Recommender 系統挑戰 2014。
  </td>
</tr>

<tr>
  <td valign=top>各種汽車 MPG 資料</td>
  <td valign=top>
此資料集是也經過小幅修改資料集所提供的 Carnegie 產品大專院校版的 StatLib 文件庫的版本。 1983年美國統計關聯 Exposition 中使用資料集。<p> </p>資料會列出各種汽車中加侖，以及資訊這類數柱、 引擎替代、 威力、 總重量及加速每英哩的燃料的消耗。<p> </p><b>使用方法︰</b>預測平均油耗根據 3 多重值的特定屬性和 5 連續的屬性。 <p> </p><b>相關的參考資料︰</b> StatLib Carnegie 產品大專院校版 (1993)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr>
  <td valign=top>Pima 印度 Diabetes 二進位分類資料集</td>
  <td valign=top>
美國國家 Diabetes 學會和 Digestive 和腎型疾病資料庫中的資料子集。 焦點位於母病患的 Pima 印度相似性已篩選資料集。 資料包含醫療的資料，例如 glucose 和 insulin 層級，以及生活因素。<p> </p><b>使用方法︰</b>預測主旨是否具有 diabetes （二進位分類）。 <p> </p><b>相關的參考資料︰</b> Sigillito，與 (1990)。 UCI 電腦學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml 」</a>。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 </td>
</tr>

<tr>
  <td valign=top>餐廳客戶資料</td>
  <td valign=top>
一組的客戶，包括統計資料和喜好設定相關的中繼資料。<p> </p><b>使用方法︰</b>結合兩個餐廳資料集，使用此資料集，訓練並測試 recommender 系統。 <p> </p><b>相關的參考資料︰</b> Bache，k 和 Lichman，M (2013)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學。
  </td>
</tr>

<tr>
  <td valign=top>餐廳功能資料</td>
  <td valign=top>
關於餐廳和其功能，例如食物類型、 用餐樣式和位置的中繼資料的一組。<p> </p><b>使用方法︰</b>結合兩個餐廳資料集，使用此資料集，訓練並測試 recommender 系統。 <p> </p><b>相關的參考資料︰</b> Bache，k 和 Lichman，M (2013)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學。
  </td>
</tr>

<tr>
  <td valign=top>餐廳評等</td>
  <td valign=top>
內容評等由提供使用者餐廳刻度從 0 到 2。<p> </p><b>使用方法︰</b>結合兩個餐廳資料集，使用此資料集，訓練並測試 recommender 系統。 <p> </p><b>相關的參考資料︰</b> Bache，k 和 Lichman，M (2013)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學。
  </td>
</tr>

<tr>
  <td valign=top>鋼 Annealing 多類別資料集</td>
  <td valign=top>
此資料集包含一系列的記錄鋼 annealing 試用版的實體屬性 （寬度、 寬度、 類型 （線圈、 工作表等） 的所產生的鋼類型。<p> </p><b>使用方法︰</b>預測的任何兩個數值的類別屬性。hardness 或強度。 您也可能會分析屬性之間的關聯性。<p> </p>鋼成績遵循設定標準，由 SAE 及其他組織所定義。 您正在尋找的特定 '成績 」 （課程變數），而且想要了解所需的值。 <p> </p><b>相關的參考資料︰</b>貨幣，d 與 Buntine，看 (NA)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大專院校版的加州、 學校的資訊及電腦科學 <p> </p>您可以在這裡找到鋼成績的實用指南︰ <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>望遠鏡資料</td>
  <td valign=top>
高能源伽瑪物件高速量，以及背景噪音的記錄，同時使用模擬 Monte Carlo 程序。<p> </p>模擬的目的是為了改善基本架構 atmospheric Cherenkov 伽瑪望遠鏡首次發現，使用統計方法區分所需的訊號 （Cherenkov 輻射淋浴） 和背景噪音 （由中 upper 氣氛 cosmic 光芒 hadronic 淋浴） 的精確度。<p> </p>資料已預先處理時間以建立長的叢集座標軸的角度為根據相機中心。 這個橢圓形，（通常稱為 Hillas 參數） 特性是可用於區別圖像參數。<p> </p><b>使用方法︰</b>預測的派對圖像是否表示訊號或背景噪音。<p> </p><b>附註︰</b>簡單分類精確度並不適用此資料，自最糟分類做為背景訊號事件訊號現狀分類背景事件。 應該使用巨鵬圖形的不同的相關性的比較。 接受背景事件訊號必須下列其中一項動作臨界值的機率︰ 0.01、 0.02、 0.05、 0.1 或 0.2。<p> </p>另請注意，低估背景事件 (如 hadronic 淋浴 h) 的數目，而在中實際的度量單位，h 或雜音類別表示大部分的事件。 <p> </p><b>相關的參考資料︰</b> Using、 R.K. (1995)。 學習存放庫<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>UCI 電腦。 Irvine，CA︰ 大學加州，學校的資訊 </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>天氣資料集</td>
  <td valign=top>
從 NOAA （<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">從 201304 201310 要合併的資料</a>） 的整個架構為基礎的天氣觀察每小時。<p> </p>氣象資料涵蓋觀察從機場天氣站，其涵蓋期間年 4 月的 2013年。 上傳至 Azure 毫升 Studio 之前, 資料集處理，如下所示︰<ul><li>氣象識別碼已對應至對應的機場代碼</li><li>與 [70 忙碌機場不相關的天氣站台所篩選出</li><li>[日期] 欄已分割到不同的年、 月和日欄</li><li>選取下列資料行︰ AirportID、 年、 月、 日期、 時間、 時區、 SkyCondition、 可見度、 WeatherType、 DryBulbFarenheit、 DryBulbCelsius、 WetBulbFarenheit、 WetBulbCelsius、 DewPointFarenheit、 DewPointCelsius、 RelativeHumidity、 WindSpeed、 WindDirection、 ValueForWindCharacter、 StationPressure、 PressureTendency、 PressureChange、 SeaLevelPressure、 RecordType、 HourlyPrecip、 高度表</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>維基百科 SP 500 資料集</td>
  <td valign=top>
資料被衍生自維基百科 (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) 根據文件的每個 & P 500 公司儲存為 XML 資料。<p> </p>上傳至 Azure 毫升 Studio 之前, 資料集處理，如下所示︰<ul><li>為每個特定的公司擷取文字內容</li><li>移除設定格式化的 wiki</li><li>移除非英數字元</li><li>將所有文字都轉換成小寫</li><li>新增已知的公司類別</li></ul><p> </p>請注意，針對某些公司文章找不，因此小於 500 的記錄數目。
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
資料集包含客戶資料和其回應直接郵件行銷活動的相關指示。 每個資料列代表客戶。 資料集包含 9 功能有關使用者統計資料，以及過去的行為，以及 3 標籤欄 （瀏覽，轉換，及花）。  造訪會指出客戶行銷活動之後瀏覽過的二進位資料行，並轉換指出客戶購買項目，以及花為所花的金額。  資料集已所提供的 MineThatData 電子郵件分析與資料採礦挑戰 Kevin Hillstrom。
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
測試範例 RCV1 V2 路透社新聞資料集內的功能。 資料集有 781 K 新聞文章，以及其識別碼 （資料集的第一欄）。 每篇文章 token 化 stopworded，而 stemmed。 由 David 已提供資料集。 D 鍵。 路易斯。
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
訓練課程範例 RCV1 V2 路透社新聞資料集內的功能。 資料集有 23 K 新聞文章，以及其識別碼 （資料集的第一欄）。 每篇文章 token 化 stopworded，而 stemmed。 由 David 已提供資料集。 D 鍵。 路易斯。
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
從 [KDD 杯 1999年知識庫探索及資料採礦工具競爭 (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>) 的資料集。<p> </p>資料集下載儲存在 Azure Blob 儲存體 (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>)，並包含訓練及測試資料集。 訓練資料集約有 126 K 列與 43 欄，包括標籤;3 欄屬於標籤資訊，以及 40 個資料行，包含數字和字串/分類的功能，可供訓練模型。 測試資料有大約 22.5 K 測試在相同的 43 資料行與訓練資料的範例。

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
新聞文章 RCV1 V2 路透社新聞資料集內的主題作業。 多個主題，可以指定的新聞文章。 每個資料列的 「<topic name>  <document id> 1 」。 資料集包含 2.6 M 主題作業。 由 David 已提供資料集。 D 鍵。 路易斯。
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
此資料是來自 KDD 杯 2010年學生效能評估挑戰 （<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">學生效能評估</a>）。 使用的資料是 Algebra_2008_2009 訓練設定 (戳記加註 j。，Niculescu Mizil，a，Ritter，S Gordon、 G.J.，與 Koedinger，K.R. (2010)。 代數我 2008年 2009年。 從 KDD 杯 2010年教育資料採礦挑戰的挑戰資料集。 在<a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>或<a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>上找到。<p> </p>資料集下載儲存在 Azure Blob 儲存體 (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>)，並從家教廣告系統學生的記錄檔。 所提供的功能包括問題識別碼與簡短描述、 學生識別碼、 時間戳記和多少次之前的正確方法中解決問題所做的學生。 原始資料集 8.9 M 記錄，向下取樣的第一個 100 K 列已此資料集。 資料集有 23 tab 字元分隔的資料行各種類型︰ 數字、 分類和時間戳記。

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
