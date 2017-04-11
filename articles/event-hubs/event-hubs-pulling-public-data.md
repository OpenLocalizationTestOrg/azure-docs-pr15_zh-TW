<properties
    pageTitle="將 Azure 事件集線器抽取公用資料 |Microsoft Azure"
    description="從網頁範例匯入的事件集線器概觀"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>將 Azure 事件集線器抽取公用資料

在一般網際網路的項目 (IoT) 案例中，您有要 Azure 至 Azure 事件中心或 IoT 中樞的推入資料，您可以程式的裝置。 儲存、 分析及視覺化工具在 Microsoft Azure 提供各式各樣的兩個這些集線器是 Azure 進入點。 不過，它們都要求您將資料發送給他們，格式化為 JSON 及保護特定的方式。 如此會開啟下列問題。 如果您想要將資料來源的資料公用或私人，資料會以 web 服務或摘要的一些排序，公開，但您沒有能力變更發佈資料的方式您該怎麼做？ 考慮天氣或傳輸，或股票-無法分辨 NOAA，或 WSDOT 或 NASDAQ 設定推入至您的事件集散地。 若要解決此問題，我們已撰寫，然後開啟來源的小型雲端範例，您可以修改並部署會提取一些這類來源的資料和其推入事件中心。 在這裡，您可以執行您想要主旨當然，以產生者授權合約。 您可以尋找應用程式[以下](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)。

請注意，在這個範例中的程式碼只會顯示如何從擷取資料一般 web 摘要，以及如何寫入 Azure 事件中心。 這不是在是實際執行應用程式，並沒有嘗試，使其適合使用這類環境中使用-是 strictfly DIY，僅限開發人員焦點範例。 此外，此範例存在不是您將 Azure，而不是**推入**應該**擷取**資料的建議 tantamount to 它。 請檢閱安全性、 效能、 功能，並在坐在端對端架構成本因素。

## <a name="application-structure"></a>應用程式結構

應用程式撰寫 C#，[範例說明](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)包含所有您需要修改、 建立及發佈應用程式的資訊。 下列各節提供高層級的應用程式的功能概觀。

我們開始，其假設您有摘要資料的存取權。 例如，您可能要提取流量資料從華盛頓狀態部門的交通或從 NOAA，以顯示自訂報表，或合併的資料使用其他應用程式中的資料的氣象資料。 您也需要擁有設定 Azure 事件] 中心上，並知道連線字串所需的存取權限。

GenericWebToEH 解決方案啟動時，其會顯示設定檔 (App.config)，以取得數字的項目如下︰

1. [URL] 或資料發佈網站的 Url，清單。 這是在 JSON，發佈資料，例如 WSDOT 參照的網站的理想的情況下，[在這裡](http://www.wsdot.wa.gov/Traffic/api/)。 
2. 如有需要的 url 的認證。 許多公用來源不需要認證，或您可以將 URL 字串中的認證。 其他人要求您提供個別。 （請注意，您可以只指定一組認證在此應用程式，因此如果您指定只有一個 URL，Url 清單，就能只會使用該）。
3. 連接字串，該事件集線器命名空間，您會對其推播資料中的 [事件] 中心內的名稱。 Azure 入口網站中，您可以找到這項資訊。
4. 休眠間隔，以毫秒為單位，輪詢公用資料網站之間的間隔。 設定此需要思考。 如果您不常太投票，您可能會錯過資料。另一方面，如果您經常投票，您可能會收到大量重複的資料，或較，您可能會被封鎖為讓傀儡。 請考慮更新資料來源的頻率-天氣或流量資料重新整理 15 分鐘，但股票報價是不是每隔幾秒鐘，取決於您取得的位置而定。 
5. 若要判斷應用程式是否為 JSON 或 XML 傳入資料旗標。 您需要推入事件中心的 [資料，因為應用程式會有要轉換成 JSON 的 XML，然後再傳送模組。

讀取之後設定檔，應用程式會進入迴圈-存取公用網站，轉換的資料，如有必要，寫入您 [事件] 中心內，與然後等待休眠間隔進行之前，先將其過一次。 特別是︰

  * 閱讀公用網站。 接收準備要傳送資料從 Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs 使用 RawXMLWithHeaderToJsonReader 類別的執行個體。 它會讀取來源資料流中 GetData() 的方法，然後分割使用 GetXmlFromOriginalText 它較小的區塊 （亦即記錄）。 
  這個方法會讀取 XML 以及語 JSON 或 JSON 陣列。 然後處理開始時使用從 App.config MergeToXML 設定 (預設 = 空白)。
  * 接收及傳送資料是以循環播放 Process() 方法中 Program.cs 中實作。 
  從 GetData() 收到輸出結果之後, 方法儲分隔 [事件] 中心內的值。

## <a name="next-steps"></a>後續步驟

若要部署解決方案，複製或下載[GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)應用程式、 編輯 App.config 檔案、 建立，及最後發佈。 一旦您已發佈應用程式，您可以看到它在雲端服務] 下 [Azure 傳統入口網站中執行，您可以變更部分設定的設定 （例如事件中心目標和休眠間隔） [**設定**] 索引標籤中。

請參閱[Azure 範例圖庫](https://azure.microsoft.com/documentation/samples/?service=event-hubs)中，然後在 [ [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)上的其他事件集線器範例。
