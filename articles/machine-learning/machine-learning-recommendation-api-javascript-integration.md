<properties 
    pageTitle="電腦學習建議︰ JavaScript 整合 |Microsoft Azure" 
    description="Azure 電腦學習建議 JavaScript 整合-文件" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Azure 電腦學習建議-JavaScript 整合

>[AZURE.NOTE]您應該開始使用，而非此版本的建議 API 認知服務。 建議認知服務會取代這項服務，並會那里開發所有的新功能。 有新的功能，例如批次處理支援、 獲得更佳的 API 檔案總管，取得更簡潔的 API 表面、 更加一致註冊/計費體驗等。
> 深入瞭解[移轉至新的認知服務](http://aka.ms/recomigrate)


本文說明如何將整合 JavaScript 您的網站。 JavaScript 可讓您傳送資料擷取事件，以及使用建議，當您建立的建議模型。 伺服器端可以也完成透過 JS 完成所有作業。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1.一般概觀
整合 Azure 毫升建議您的網站上 2 階段包含︰

1.  傳送到 Azure 毫升建議的事件。 這樣會啟用建立建議模型。
2.  使用建議。 內建模型之後，您可以使用建議。 （這份文件不會說明如何建立模型，瞭解快速入門指南，以取得詳細資訊的方式）。


<ins>階段 I</ins>

第一個階段您插入 html 頁面小型 JavaScript 文件庫，可讓頁面傳送到 （透過資料市集） Azure 毫升建議伺服器發生 [html] 頁面上的事件︰

![Drawing1][1]

<ins>第二個階段</ins>

當您要顯示在頁面上的 [建議的第二個階段中選取下列選項之一︰

1.您的伺服器 （在頁面轉譯的階段） Azure 毫升建議 Server （透過資料市集） 通話取得建議。 結果包含項目編號的清單。 您的伺服器需要豐富的項目 （例如圖像、 描述） 的中繼資料的結果，並傳送至瀏覽器的 [建立] 頁面。

![Drawing2][2]

2.[其他] 選項是使用其中一個階段的小型 JavaScript 檔案以取得簡單的建議的項目清單。 以下收到的資料是款比中第一個選項。

![Drawing3][3]

##<a name="2-prerequisites"></a>2.先決條件

1. 建立新的模型使用 Api。 如何進行此動作，請參閱快速入門指南。
2. 編碼您&lt;dataMarketUser&gt;:&lt;dataMarketKey&gt;與 base64。 （這會用來進行基本驗證啟用 JS 程式碼，呼叫的 Api）。


##<a name="3-send-data-acquisition-events-using-javascript"></a>3.傳送資料擷取事件使用 JavaScript
下列步驟順利傳送事件︰

1.  在您的程式碼中包含 JQuery 文件庫。 您可以從下列 url nuget 免費下載。

        http://www.nuget.org/packages/jQuery/1.8.2
2.  包含從下列 URL 的建議 Java 指令碼文件庫︰ http://aka.ms/RecoJSLib1

3.  初始化 Azure 毫升建議文件庫，以適當的參數。

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  傳送適當的事件。 請參閱下方的詳細的區段所有類型的事件 （範例按一下 [事件]）

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1。 限制和瀏覽器支援
這是參照實作，因此會給予。 應支援所有的主要瀏覽器。

###<a name="32-type-of-events"></a>3.2 捨位。 事件的類型
5 種文件庫支援的事件︰ 按一下，按一下 [建議，新增至購物車，移除 [商店購物車和購買。 有其他事件用來設定稱為登入的使用者內容。

####<a name="321-click-event"></a>3.2.1。 按一下 [事件]
此事件適用於使用者的項目上按一下 [任何時間。 使用者在 [項目時，通常為項目詳細資料; 開啟新的頁面此頁面中應該會觸發此事件。

參數︰
- 事件 （字串，強制）-[按一下]
- 項目 （字串，強制）-項目的唯一識別碼
- 這些 （字串，可省略）-項目的名稱
- itemDescription （字串，可省略）-的項目描述
- itemCategory （字串，可省略）-的項目分類
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

或使用選用的資料︰

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2。 按一下 [事件] 建議
此事件適用於任何時間，按一下 [從 Azure 毫升建議建議的項目收到的項目上的使用者。 使用者在 [項目時，通常為項目詳細資料; 開啟新的頁面此頁面中應該會觸發此事件。

參數︰
- 事件 （字串，強制）-「 recommendationclick 」
- 項目 （字串，強制）-項目的唯一識別碼
- 這些 （字串，可省略）-項目的名稱
- itemDescription （字串，可省略）-的項目描述
- itemCategory （字串，可省略）-的項目分類
- 設 （字串的陣列，可省略）-產生建議查詢種子。
- recoList （字串的陣列，可省略）-產生的項目，按一下 [建議要求的結果。
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

或使用選用的資料︰

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3。 新增購物車事件
應該使用此事件時使用者新增至購物車的項目。
參數︰
* 事件 （字串，強制）-「 addshopcart 」
* 項目 （字串，強制）-項目的唯一識別碼
* 這些 （字串，可省略）-項目的名稱
* itemDescription （字串，可省略）-的項目描述
* itemCategory （字串，可省略）-的項目分類
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4。 移除購物車事件
當使用者移除項目至購物車時，應此事件。

參數︰
* 事件 （字串，強制）-「 removeshopcart 」
* 項目 （字串，強制）-項目的唯一識別碼
* 這些 （字串，可省略）-項目的名稱
* itemDescription （字串，可省略）-的項目描述
* itemCategory （字串，可省略）-的項目分類
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5。 購買事件
使用者在購買他購物車時，應此事件。

參數︰
* 購買事件 （字串）-「 」
* 項目 （購買 [）-陣列的每個項目購買保留項目。<br><br>
購買的格式︰
    * 項目 （字串）-項目的唯一識別碼。
    * 計算 （int 或字串）-所購買的項目數。
    * 價格 （浮動時間或字串）-選擇性欄位-的價格，項目。

下列範例顯示購買 3 的項目 （33 34、 35），兩個用的所有欄位填入項目、 count （price） 和 （項目 34） 不價格。

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6。 使用者登入事件
Azure 毫升建議事件文件庫建立並使用 cookie 才能識別來自相同瀏覽器的事件。 若要改善模型結果 Azure 毫升建議可讓設定使用者的唯一識別碼，將會覆寫 cookie 使用方式。

在使用者登入至您的網站之後，應該使用這個事件。

參數︰
* 事件 （字串）-「 userlogin 」
* 使用者 （字串）-使用者的唯一識別碼。
        <script>
  如果 (typeof AzureMLRecommendationsEvent = ="未定義 」) {AzureMLRecommendationsEvent = [;}AzureMLRecommendationsEvent.push ({事件: 「 userlogin 」，使用者: 「 ABCD10AA 」})。      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4.取用透過 JavaScript 的建議
使用建議的程式碼時，某些 JavaScript 事件觸發用戶端的網頁。 建議回應包含建議的項目識別碼、 他們的名稱和其評等。 最好使用此選項僅適用於清單項目的顯示建議-伺服器端整合應該更複雜的處理 （例如新增的項目中繼資料）。

###<a name="41-consume-recommendations"></a>4.1 取用建議
若要使用您需要的建議在您的頁面，然後呼叫 AzureMLRecommendationsStart 包含必要的 JavaScript 文件庫。 請參閱第 2 節。

若要使用 [建議的一或多個項目，則需致電稱為的方法︰ AzureMLRecommendationsGetI2IRecommendation。

參數︰
* 項目 （陣列的字串）-一或多個項目，以取得的建議。 如果您使用 Fbt 建立，然後您可以在此設定只有一個項目。
* numberOfResults (int)-必要的結果數目。
* includeMetadata （布林值，可省略）-如果設為 「 true 」 會指出中繼資料欄位，必須填入的結果。
* 處理函數會處理所傳回的建議函數。 資料會傳回的陣列︰
    * 項目-項目唯一識別碼
    * 名稱的項目名稱 (如果存在目錄] 中)
    * 評等的建議評等
    * 中繼資料的字串，表示該項目的中繼資料

範例︰ 下列程式碼要求 8 建議的項目 」 64f6eb0d-947a-4c18-a16c-888da9e228ba 」 (並不指定 includeMetadata-隱含地顯示沒有中繼資料，不需要)，它將緩衝然後串連結果。

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
