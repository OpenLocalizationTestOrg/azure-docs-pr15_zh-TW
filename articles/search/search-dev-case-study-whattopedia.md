<properties 
    pageTitle="Azure 搜尋開發人員案例研究︰ 如何在 Microsoft Azure WhatToPedia 內建的 infomedia 入口網站 |Microsoft Azure |裝載的雲端搜尋服務" 
    description="瞭解如何建立資訊入口網站和中繼搜尋引擎使用 Azure 搜尋，適用於開發人員的雲端裝載於搜尋服務。" 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Azure 搜尋開發人員案例研究

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>[WhatToPedia.com](http://whattopedia.com/)上 Microsoft Azure 建置 infomedia 入口網站的方式

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">顯示較大的概念</font> 


我們會建立可協助購物與零售商透過高度相關、 範圍搜尋體驗，類似於如何使用旅館、 餐廳和在神秘的銷售領域中的 「 娛樂 」 往入口網站符合的項目不就一頭連線資訊入口網站。 

我們規畫入口網站會移到指定的市場零售商資料上發表特別高品質的搜尋體驗，協助尋找儲存位置和其他設施零售商購物提供。 我們會植搜尋引擎初始的資料集，但更深入的值會建立一段時間，零售商版訂閱者張貼其企業的相關資訊的協助。 促銷活動、 新的商品，常用的品牌內部特殊服務--所有是將值新增到網站的資料的範例。 此資料會自動報告，且整合搜尋中華，一旦零售商註冊為訂閱者。 廣告，加上的訂閱模型，提供營收串流我們新的商務用。

搜尋會主流使用者互動模型，純粹雲端的平台上。 為了比例] 和 [低成本，幾乎所有我們執行，請從來源控制入口網站的經驗會透過線上服務。 使用提供的功能，我們需要搜尋引擎不在] 方塊中，我們可以快速建立搜尋應用程式，而不需要建立及管理搜尋引擎自己。

## <a name="what-we-built"></a>我們的內建

WhatToPedia 是初次 infomedia 公司。 我們內建[WhatToPedia.com](http://whattopedia.com/) --目前在線上 2015 年 2 月 2 日的日期北歐洲測試市場中。 我們的客戶基底為主要的磚灰漿商店給需要輕鬆管理及維護實惠地的線上狀態。

我們的工作是吸引購物透過很棒的線上搜尋體驗，提高結果縣/市或鄰近，品牌，儲存的名稱，或儲存類型。 吸引購物已漣漪效果，若要訂閱我們入口網站的各種零售商。 訂閱是付費，實惠地的速度。

 ![][7] 

註冊訂閱之後, 零售商接手 （建立一開始，我們已購買的資料） 其到現有的設定檔，更新促銷活動、 主要的品牌或宣告相關的其他資料。 內部的功能，例如，語言貨幣接受，tax-free 購物時，可能會更吸引人正在尋找的這些設施購物自我報告。

## <a name="who-we-are"></a>我們的

使用發行 Boelling，導致在 WhatToPedia，設計解決方案的開發人員，我是邁可 Segato （Microsoft 查閱）。 

WhatToPedia 是啟動時，行銷其新的入口網站商務瑞典，大部分的 60000 零售商哪裡磚灰漿 Sme （小型及中型中小型企業） 中的測試。 因為我們知道購買的人員說話多種語言，及執行多個貨幣，我們會建立配合多語系購物者的解決方案。 我們有需要並找到，搜尋引擎中 Azure 搜尋支援我們多語系的需求。

Azure 搜尋已遊戲調整器我們的專案。 之前 Azure 搜尋的顯示狀態，我們會耗用相當能源逐步建立自己的搜尋引擎的 kinks。 線上服務會將最大的技術和系統管理障礙從中我們的解決方案時，無法 Azure 搜尋，哪些全新前往行銷更快速，且更強大的搜尋體驗。  

## <a name="how-we-did-it"></a>我們如何所做的一樣

我們提供了一些是根據 cloud services 只完成基礎結構。 Microsoft 被選為策略性的平台，因為它是提供必要的提供者 （適用於共同作業與開發），服務不按比例縮放需求，以及實惠地價格。
 
### <a name="high-level-components"></a>高層級的元件

我們內建的企業，而不只是網站。 支援的整個投入比需要全方位的工具及應用程式。 我們採用 Visual Studio 及開發小組 Foundation 服務 (TFS) 線上來源控制項和 scrum 管理、 通訊和共同作業的 Office 365 和當然 Microsoft Azure 所有網站相關作業和儲存空間的 Visual Studio 小組服務。 Visual Studio 中，使用 IDE 會提供 TFS 線上提供其他生產力增量整合至 Azure 直接提供。

下圖說明使用 WhatToPedia 基礎結構中的高層級元件。

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>我們如何使用 Microsoft Azure

查看前圖中的綠色方塊，您會看到 WhatToPedia 解決方案內建於下列服務︰

- [Azure 搜尋](https://azure.microsoft.com/services/search/)
- [Azure 網站使用 MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs 排定的工作](../app-service-web/websites-webjobs-resources.md)
- [Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)
- [Azure BLOB 儲存體](https://azure.microsoft.com/services/storage/)
- [SendGrid 電子郵件傳遞](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

解決方案的其實是資料，並搜尋。 結束客戶轉售商提供者所提供的資料流動如下所示︰

  ![][9]

主要資料儲存為轉售商和 Azure SQL 資料庫中的會計資料。 這包含初始資料集，以及新增一段時間的零售商特定資料。 我們使用 Azure WebJob Azure 搜尋中搜尋中華更新張貼從 SQL 資料庫。

### <a name="presentation-layer"></a>簡報的圖層

入口網站是 Azure 網站，實作於 MVC 4 和[Twitter 啟動安裝程式](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29)。 我們之所以選擇這個 MVC，因為它提供更簡潔的方法為 HTML 比 ASP.NET 表單為基礎的開發。 若要避免建立多個裝置的相關應用程式和維護多個行動裝置平台，支援所有裝置和平台選擇 Twitter 啟動安裝程式。

### <a name="authentication-permissions-and-sensitive-data"></a>驗證權限與機密資料

購物瀏覽] 以匿名方式網站。 因此，有購物，沒有登入的需求，也不我們儲存任何消費者資料。 

零售商是不同的方式呈現故事。 在這裡，我們將儲存-在公開的設定檔資訊、 計費資訊及他們想要公開網站上的媒體內容。 每個網站而定的零售商取得使用者的登入，用於使用者進行驗證之前訂閱者的設定檔對進行更新。  我們安全地會儲存 Azure SQL 資料庫和 Azure BLOB 儲存體中的所有訂閱者的資料。
我們選擇根據.NET 表單型驗證的驗證模型。 我們之所以選擇這個簡單; 此方法我們並不需要角色、 UI 支援及其他相關功能的其他方法。 

若要確保零售商只看到其所屬的資料，我們建立的零售商識別碼就會用在所有的每個零售商讀取和寫入涉及零售商特定的資料作業。 使用這個方法，我們發現，我們不需要個別零售商授與資料庫權限。 所有的零售商互動的下一個資料庫角色中，為我們的資料隔離技術的零售商識別碼系統。

因為我們商務所有相關後階段的影響 （零售商開車多個商務、 建立通知和訂閱的動機） 我們可以繪製處理購買在 web 上的線條。 因此，您將無法找到購物車我們在網站上，可簡化我們的安全性需求。 

我們所採用的另一個簡化是外包計費與帳戶應付帳款作業。 以路由客戶付款資訊，直接對第三方 ([SveaWebPay](http://www.sveawebpay.se/))，我們降低建立與儲存及保護機密資料，在我們的資料存放區關聯的風險。 

### <a name="search-engine"></a>搜尋引擎

核心我們是解決方案的內建在 Azure 搜尋服務的搜尋引擎。 一開始，我們建立自訂的搜尋引擎，但在此程序，我們發現複雜度投入是更高確實，和的提示我們考慮使用其他方法。 

我們包含最重要的基本功能︰

- 篩選
- 多面向導覽
- 提高結果
- AJAX 進行分頁

在網際網路上搜尋讓我們以下的影片中，我們嘗試 Azure 搜尋啟發︰[在 TechEd 歐洲深度剖析](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

之後觀看影片，我們可以準備開始建立原型根據看到。 我們已在 MVC 中的資料模型，因為建立原型乾淨的簡單且，所以。 所包含的資料可搜尋字詞，我們已工作了出我們想要排序多面向，及篩選資料的方式的需求。 

這是我們建置原型的方式。

**設定 Azure 搜尋服務**

1. Azure 傳統入口網站登入並加入我們訂閱搜尋服務。 我們使用共用的版本 （免費我們訂閱）。
2. 建立索引。 原型，我們可以使用入口網站使用者介面來定義搜尋欄位並建立計分的設定檔。 我們計分的設定檔以位置資料為基礎︰ 國家/地區 |縣/市 | 地址 (請參閱︰ 新增計分的設定檔)。
3. 複製服務 URL 和管理員我們的設定檔的 api 金鑰。 此按鍵在入口網站中的 [搜尋服務] 頁面上，而且會用於驗證服務。
    
**開發搜尋索引工作 – Windows 主控台**

1. 從資料庫中讀取所有轉售商。
2. 呼叫 Azure 搜尋服務 API 上傳轉售商一個接一個] (請參閱︰ http://msdn.microsoft.com/library/azure/dn798930.aspx)。
3. 在資料庫中設定屬性的轉銷商索引的累加編製索引作業。 我們這麼做，藉由新增儲存的每一個 （或不索引） 的設定檔索引狀態的 「 索引 」 欄位。 

請參閱建立索引作業的程式碼片段附錄。

**開發入口搜尋網站 – MVC**

1. 呼叫 Azure 搜尋服務，以從搜尋中取得所有文件 (請參閱︰ http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. 擷取搜尋服務回應追蹤 （藉由使用 json.net http://james.newtonking.com/json）
   - 結果
   - Facet
   - 結果計數
   - 開發顯示搜尋結果，facet 計數 （我們已經有這） 的使用者介面。

這是我們用來從 Azure 搜尋獲得的結果的程式碼︰

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**提高位置**

可能是最重要的需求，以驗證原型中包含位置搜尋關鍵字新增至查詢。 如果使用者輸入的城市名稱，在搜尋查詢，我們入口網站的重要的是在指定的城市轉售商想高於描述中有縣/市關鍵字的轉售商排列。 這項需求，我們會使用計分的設定檔排列高於其他欄位的 [城市] 欄位。

**支援多種語言**

我們需要顯示正確的搜尋結果中正確的語言，並提供找到相同的結果，以不同語言的選項。 這個問題兩側所示︰ 

- 搜尋多語言的文字
- 顯示搜尋結果中正確的語言

我們解決簡報中的組件新增文件的每個語言的本地化文字並使用該語言的屬性。 輸入搜尋字詞，我們使用者`$filter`選擇要篩選的語言使用者的運算式。

每個文件都有稱為 「 城市 」 內建在集合類型的隱藏的屬性。 這個屬性會縣/市名稱儲存所有的語言，讓使用者能夠在多種語言中搜尋。

###<a name="data-storage"></a>資料儲存區

（設定檔、 訂閱，與會計專用） 的所有資料會都儲存於 SQL 資料庫。 所有的媒體檔案會儲存在 Azure BLOB 儲存體，包括圖像和零售商所提供的視訊。 使用不同的 BLOB 儲存體隔離的上傳的檔案。檔案會永遠不會共同 mingled 的網站，因此我們不需要重新建立網站，我們新增的檔案。

我們儲存空間項優點是設計的多個開發人員都可以共用單一開發儲存空間。 其中一個 WhatToPedia project 的需求是能夠建立在 15 分鐘，包括轉售商資料、 圖像及影片內的開發環境。 從 TFS 線上取得最新的資料、 執行 SQL 指令碼，並執行匯入作業，完成的環境可以是名人沒有時間完全。 此作法也會提高暫存的程序。

###<a name="webjobs"></a>WebJobs

我們使用 Azure WebJobs 索引中更新資料。 藉由建立搜尋索引作業，編製索引的組件都是很容易整合我們的解決方案。 我們所做的唯一的程式碼變更是以容納索引作業已新增`Indexed`至我們資料模型]，表示索引狀態] 欄位。 新增或更新，新的設定檔時`Indexed`欄位設為 false。 如果，套用零售商變更他或她透過入口網站的設定檔資料。  

工作會遇到的所有資料列`Indexed`設定為 false。 文件時，會找出資料列，張貼至 Azure 搜尋]，然後`Indexed`欄位設為 true。 我們不需要進行新增與更新的資料，因為實際 Azure 搜尋服務會處理的計劃。 如果您新增的已存在文件，服務就會自動執行更新。

已為主控台應用程式，可以上傳至 Azure 網站為 ZIP 檔案、 解壓縮，以及然後排程開發出所有的 web 工作。

工作排程執行排程的 web 工作每 5 分鐘。 我們計算服務需要大約三個分鐘即可上傳 3000 的文件，這是在我們的需求。 

> [AZURE.NOTE] 有最近引進 Azure 搜尋原型索引子功能。 這項功能已預先太遲我們將其用於我們初次發行，但似乎解決相同的問題，我們使用，我們索引作業已來自動化資料載入作業。


###<a name="backup-strategy"></a>備份的策略

我們設計多層備份策略來復原從範圍的情況下，從下復原的個別交易的重大錯誤。 若要保護的資產包含三個類型的資料 （網站、 用戶資料和媒體檔案）。 

首先，在 TFS web App 中保留的網站來源程式碼，我們知道，是否網站當機，我們可以重建其從 TFS 重新發佈。 

訂閱者 Azure SQL 資料庫中的資料是最重要的資產。 我們回此使用內建功能 （請參閱[Azure SQL 資料庫備份與還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)）。 備份排程是完整的資料庫備份一週一次、 差異資料庫備份一次，一天和交易記錄檔備份每 5 分鐘。  指定資料的大小，此解決方法是多個足以讓我們立即和預計資料區的工作。

第三，我們將圖像及影片檔案儲存在 Azure BLOB 儲存體。 我們仍會評估最終備份規劃此資料，請考慮 Cloudberry Explorer Azure 為可能的解決方案。 現在，我們會使用 WebJob 圖像和影片，複製到另一個位置。

##<a name="what-we-learned"></a>我們所學的內容

我們已有資料，因為它很容易建立的概念。 小時內，我們使用 facet 原型計數器、 分頁、 後面的設定檔和搜尋結果。 搜尋結果的因此精確度，我們決定要移除部分呈現結束客戶的篩選。 

最大我們是我們無法瞭解 Azure 搜尋的速度和多少我們有回。 如實，我們建立的概念 （請參閱下列附註） 的幾個小時，使用 3 幾行程式碼中的前端應用程式 （加上新的 WebJob），取代 500 幾行程式碼]，取得更好的結果。 

先前，我們的程式碼實作分頁、 計數及標準搜尋的其他行為。 使用 Azure 搜尋，得到的結果包含搜尋結果，facet，分頁資料，計算，我們需要並提供自己所需的所有內容。 它也包含促進，內建的多面向導覽，我們不需要在原始方案中。

實作期間的最大的挑戰已是在預覽版本，並尋找資訊和共用的體驗很難。 一旦我們連線幾個點，我們發現使用 Azure 搜尋服務很簡單，因為其 REST API 及 JSON 資料格式。 我們無法直接從最開啟來源外掛程式，例如 JQuery JSON.Net 呼叫架構，我們無法用於快速實驗和偵錯等 Fiddler 工具。 

> [AZURE.NOTE] 除了有資料準備好網域，它幫我們已建立原型的瞭解如何搜尋技術運作正常，讓我們更有效率，以及其他感激的內建的功能。 如果您需要在搜尋查詢建構會增加，多面向導覽、 篩選器等應該原型採取更長的時間。 

###<a name="controlling-facets-in-the-search-presentation-page"></a>在 [搜尋] 簡報頁面控制 facet

其中一個期間的概念，我們檢討是規劃謹慎事前 facet。 之後載入解決方案的大量資料，我們可以看到大量的 facet 已得太高呈現給使用者。 

我們解決限制多面向計數參數。 Count 參數會採用嚴格的限制，facet 傳回給使用者數目。 您可以找到連結，其中包含計算參數的討論[以下](search-faceted-navigation.md)。

###<a name="webjobs-for-scheduling-tasks"></a>任務排程 WebJobs

Azure 搜尋不是我們只愉快意外。 我們發現使用 WebJobs 自動化 Azure 搜尋我們資料載入作業是我們先前的方法，詳述使用執行 Windows 排程器，更新搜尋索引的排程任務的固定的 VM 大幅上層。 WebJobs 已設定較簡單且更容易偵錯和當然許多比起支付專用 VM 成本。

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Azure BLOB 儲存體 Explorer 更新圖像

我們發現使用[Azure BLOB 儲存體總管](https://azurestorageexplorer.codeplex.com/)（使用 codeplex 上），在 [管理網站的圖像和視訊更新很有幫助。 我們將其作為開發人員工具來手動更新圖像和屬於主網站的視訊。 我們發現這是比部署變更入口網站，更有彈性，並排除完成測試反覆運算時需要更新圖像。 

##<a name="a-few-final-words"></a>幾個的最後一個字

讓我們共用其本文在 WhatToPedia 的絕佳人感謝您 ！  

我們希望找到此案例研究有幫助。 如果您在 [上移至使用 Azure 搜尋，建議您沿著充分的幾個資源︰

- [MSDN 論壇專門 Azure 搜尋](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow 也有標籤](http://stackoverflow.com/questions/tagged/azure-search)
- [在 Azure.com 上的文件頁面](https://azure.microsoft.com/documentation/services/search/)
- [Azure MSDN 上的搜尋文件](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>附錄︰ 搜尋索引 WebJob

下列程式碼，建立一節所述建置原型索引。

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
