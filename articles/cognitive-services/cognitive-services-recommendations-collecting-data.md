<properties
    pageTitle="收集資料訓練模型︰ 電腦學習建議 API |Microsoft Azure"
    description="Azure 機器學習建議-收集訓練模型的資料"
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
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>訓練模型收集資料 #

若要尋找的項目應該是您過去的交易彼此學習建議 API 建議特定使用者。

您已經建立模型之後，您需要提供兩段資訊，您可以執行任何訓練之前︰ 目錄檔案，並使用資料。

>   如果您有不這麼做，我們建議您以完成的[快速入門指南](cognitive-services-recommendations-quick-start.md)。


## <a name="catalog-data"></a>資料目錄 ##

### <a name="catalog-file-format"></a>目錄檔案格式 ###

目錄檔案包含您為客戶提供項目的相關資訊。
目錄資料應該先按照下列格式︰

- 沒有功能-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- 使用功能-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>在 [類別目錄檔案中的範例資料列

不含的功能︰

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

使用功能︰

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>格式的詳細資料

| 名稱 | 強制性 | 類型 |  描述 |
|:---|:---|:---|:---|
| 項目識別碼 |[是] | [A-z]、 [a-z]、 [0-9]，[_] & #40;底線 & #41;，[-] & #40; 虛線與 #41;<br> 最大長度︰ 50 | 唯一的項目識別碼。 |
| 項目名稱 | [是] | 任何英數字元<br> 最大長度︰ 255 | 項目名稱。 |
| 項目分類 | [是] | 任何英數字元 <br> 最大長度︰ 255 | 此項目所屬分類 （例如烹飪書籍，齣...）。可以是空的。 |
| 描述 | 否，除非功能發表 （，但可以是空白） | 任何英數字元 <br> 最大長度︰ 4000 | 描述此項目。 |
| 功能清單 | 無 | 任何英數字元 <br> 最大長度︰ 4000;最大的數字的功能︰ 20 | 逗點分隔清單功能名稱 = 可以用來強化模型建議的功能值。|

#### <a name="uploading-a-catalog-file"></a>上傳目錄檔案

查看上傳目錄檔案[API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1)。  

請注意，目錄檔案的內容應該傳遞為邀請內文。

如果您將數個目錄檔案上傳到相同的模型有多個電話時，我們會插入只有新的類別目錄項目。 仍會維持原始值的現有的項目。 您無法使用此方法更新目錄資料。

>   附註︰ 檔案大小上限為 200 MB。
>   在目錄中支援的項目數目上限為 100000 的項目。


## <a name="why-add-features-to-the-catalog"></a>為什麼要新增到類別目錄的功能？

建議引擎建立會告訴您有什麼項目可能表示喜歡或客戶購買統計模型。 如果您有新的產品與其互動的永遠不會無法建立模型共同活動上了解單獨使用。 假設您開始提供新存放區，因為有您不知道有什麼其他項目與該小提琴建議您之前從未賣出該小提琴中的 「 兒童小提琴 」。

不過，如果引擎知道該小提琴的相關資訊 （亦即是樂器、 子系年紀 7-10 個，它不是昂貴小提琴等），然後引擎可以從其他產品包含類似的功能。 比方說，您具有過去的銷售小提琴的通常購買 violins 的人員似乎經常犯購買古典音樂 Cd 或工作表的音樂。  系統可以找到這些功能之間的連線，並提供建議根據功能，而您的新小提琴可小的使用方式。

屬於目錄資料，匯入功能且然後排名 （或模型中的功能的重要性） 會產生關聯完成等級的建立。 根據的使用狀況]，然後輸入的項目模式，可以變更功能排名。 但一致使用方式/項目，排名應有只小型變動。 排名的功能是非負數數字。 數字 0 表示功能已不排名 （情況呼叫之前的第一個 rank 建立完成此 API）。 排名使用哪些屬性的日期稱為分數新鮮度。


###<a name="features-are-categorical"></a>有 Categorical 功能。

這表示您應該建立類似於類別的功能。 例如，價格 = 9.34 不是分類的功能。 另一方面，功能等 priceRange = Under5Dollars 已分類的功能。 其他常見的錯誤是作為功能使用的項目名稱。 這會造成項目是唯一的讓它不會說明類別的名稱。 請確定功能代表類別的項目。


###<a name="how-manywhich-features-should-i-use"></a>我應該使用如何多/的功能？


最後的建議建立支援建立 20 個功能的模型。 您無法在您的目錄，項目，指派 20 個以上的功能，但您預期排名建置，然後挑選功能僅限高的排名。 （階層 2.0 或更多的功能很適合使用功能 ！）。 


###<a name="when-are-features-actually-used"></a>何時實際使用的功能？

沒有足夠的交易資料來提供建議單獨的交易資訊時，會使用模型功能。 因此功能會影響最大有 「 低溫郵件 」 – 幾個交易的項目。 如果您的所有項目有足夠的交易資訊，您可能不需要豐富您的模型功能。


###<a name="using-product-features"></a>使用產品的功能

若要使用功能，您需要您建立的一部分︰

1. 請確定您的目錄功能時您上傳。

2. 觸發程序排名建立。 這會執行分析重要性/排名的功能。

3. 觸發建議建置、 設定下列建立參數︰ useFeaturesInModel 設為 true，則為 true，allowColdItemPlacement 和 modelingFeatureList 應該會設定為 [逗點分隔各項功能，您要用來強化您的模型。 如需詳細資訊，請參閱[建議建立類型參數](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0)。





## <a name="usage-data"></a>使用資料 ##
使用檔案包含如何使用這些項目，或從您的業務交易的相關資訊。

#### <a name="usage-format-details"></a>使用格式細節
使用檔案是使用檔案中的每個資料列位置代表使用者和項目之間的互動的 CSV （逗號分隔值） 檔案。 每個資料列的格式，如下所示︰<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| 名稱  | 強制性 | 類型 | 描述
|-------|------------|------|---------------
|使用者識別碼|         [是]|[A-z]、 [a-z]、 [0-9]，[_] & #40;底線 & #41;，[-] & #40; 虛線與 #41;<br> 最大長度︰ 255 |使用者的唯一識別碼。
|項目識別碼|[是]|[A-z]、 [a-z]、 [0-9]，[& #95;] & #40;底線 & #41;，[-] & #40; 虛線與 #41;<br> 最大長度︰ 50|唯一的項目識別碼。
|時間|[是]|日期格式︰ YYYY/MM/DDTHH:MM:SS (例如 2013年/06/20T10:00:00)|資料的時間。
|事件|無 | 下列其中一項︰<br>• 按一下<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• 購買| 交易的類型。 |

#### <a name="sample-rows-in-a-usage-file"></a>使用檔案中的範例資料列

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>上傳使用檔案

查看[API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2)上傳的檔案使用方式。
請注意，您需要為 HTTP 通話的本文傳送使用檔案的內容。

>  附註︰

>  檔案大小上限︰ 200 MB。 您可能會上傳多個使用檔案。

>  您需要上傳目錄檔案，在您開始使用情況資料新增到您的模型之前。 訓練課程階段期間，將會使用目錄檔案中的項目。 所有其他項目也會忽略。

## <a name="how-much-data-do-you-need"></a>您需要多少資料？

模型品質非常依賴品質及資料的數量。
系統會學習使用者購買不同的項目時 （我們稱為 「 此共同的項目）。 針對 FBT 組建，也很重要事項哪些項目所購買的相同的交易。 

建議規則是有大部分項目位於 20 交易或更多]，因此如果您有 10000 的項目目錄] 中，我們建議您有多交易或關於 200000 交易至少 20 時間。 同樣地，這是法則。 您必須嘗試使用您的資料。

當您建立模型之後時，您可以執行[離線評估](cognitive-services-recommendations-buildtypes.md)檢查您的模型是執行程度。
