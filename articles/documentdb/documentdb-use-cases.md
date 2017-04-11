<properties 
    pageTitle="一般 DocumentDB 使用案例 |Microsoft Azure" 
    description="深入了解頂端五個 DocumentDB 的使用案例︰ 使用者會產生內容、 事件記錄、 目錄資料、 使用者喜好設定資料，以及網際網路的項目 (IoT)。" 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>常用 DocumentDB 案例
本文提供 DocumentDB 幾個常見的使用案例的概觀。  本文中的建議作為起點開發 DocumentDB 應用程式。   

閱讀本文之後，您便可以回答下列問題︰ 
 
- 什麼是 DocumentDB 常用情況？
- 有哪些優點的使用者身分使用 DocumentDB 產生內容儲存嗎？
- 什麼是使用 DocumentDB 為目錄資料的好處儲存嗎？
- 什麼是使用 DocumentDB 為事件記錄檔的好處儲存嗎？
- 什麼是使用 DocumentDB 為使用者喜好設定資料的好處儲存嗎？
- 什麼是使用網際網路的項目 (IoT) 系統做為資料存放區 DocumentDB 有哪些優點？

## <a name="common-use-cases-for-documentdb"></a>常見的使用案例 DocumentDB
Azure DocumentDB 是一般用途 NoSQL 資料庫中各種應用程式與使用情況下使用。 很不錯的選擇為任何低順序的毫秒回應的時間，並且需要快速縮放的應用程式。 以下是一些屬性 DocumentDB 會使其更適合高效能應用程式。

- DocumentDB 原本分割高可用性和擴充資料。
- DocumentDB 的有 SSD 備份低延遲的毫秒數的順序回應時間的儲存空間。
- 最終，工作階段和限制過時等的一致性層級的 DocumentDB 的支援允許低成本至效能-比。 
- DocumentDB 的彈性的資料易於價格模型的公尺儲存及處理量大小。
- DocumentDB 的保留處理量模型可讓您思考讀取/寫入，而不是 CPU/記憶體/IOPs 基礎硬體的數目。
- DocumentDB 的設計可讓您調整為大量要求區數十億要求每日的順序。

當網頁，行動裝置，遊戲 」 和 「 需要低回應時間，需要處理大量讀取和寫入的 IoT 應用程式，這些屬性是益處。 

## <a name="user-generated-content"></a>使用者產生的內容
常見的使用案例的 DocumentDB 是用來儲存和查詢使用者產生的網頁和行動應用程式，特別是社交媒體應用程式的內容 (UGC)。  UGC 的部分範例包括交談工作階段、 推文、 部落格文章、 評等，以及 [註解。  通常，社交媒體應用程式中的 UGC 是混合自由格式的文字、 屬性、 標籤和不限定嚴謹結構的關聯。   

例如聊天內容，註解和文章可以儲存在 DocumentDB 而不需要轉換或複雜關聯對應圖層物件。  新增或修改，輕鬆地以符合需求為開發人員逐一查看應用程式碼，因此升級快速開發資料屬性。  

使用各種不同的社交網路整合應用程式必須回應從這些網路變更結構描述。  當資料就會自動建立索引中 DocumentDB 預設，就可以隨時查詢。  因此，這些應用程式有彈性擷取計帳依照其個別的需求。       

許多社交應用程式執行全域的小數位數，並可以發生無法預期的使用模式。  調整資料存放區的彈性是基本為應用程式層縮放以符合需求使用方式。  您可以藉由擴展新增 DocumentDB 帳號] 之下的其他資料分割。  此外，您也可以建立其他 DocumentDB 帳戶跨多個區域。 DocumentDB 服務區域的顯示狀態，請參閱[Azure 區域](https://azure.microsoft.com/regions/#services)。   

## <a name="catalog-data"></a>資料目錄
資料目錄的使用案例涉及儲存和查詢一組的屬性，例如人員、 位置及產品的項目。  資料目錄的部分範例是使用者帳戶、 產品類別目錄、 裝置 IoT，和帳單的教材系統的登錄。  這項資料屬性可能會視情況而定，也可以變更一段時間，以符合應用程式的需求。  

請考慮汽車零件供應商產品目錄的範例。 每個部分可能會有自己的屬性，除了所有組件的通用屬性。  此外，屬性的特定部分可以變更下列年釋放新的模型。  做為 JSON 文件存放區中，DocumentDB 支援彈性的結構描述，讓您代表資料使用巢狀的屬性，因此它是很適合用於儲存產品目錄的資料。       

## <a name="logging-and-time-series-data"></a>記錄及時間序列資料
應用程式記錄通常會發出大量，可能會有不同的屬性根據部署的應用程式版本或元件記錄事件。  記錄資料不會限制複雜關聯性或嚴謹的結構。 逐漸，將記錄資料保存 JSON 格式，由於 JSON 輕量型和易於閱讀的人。
   
通常是相關的事件記錄檔資料的兩個主要的使用案例。  第一個的使用案例是疑難排解的資料子集執行臨機操作查詢。  期間進行疑難排解，資料的子集第一次從擷取記錄，通常是依時間點。  然後，向下切入執行篩選資料集錯誤層級或錯誤訊息。 這是儲存在 DocumentDB 的事件記錄檔的位置是優點。 DocumentDB 中儲存的記錄檔資料在根據預設，會自動建立索引，因此已準備好要隨時查詢。 此外，記錄資料就會保留在資料分割為時間序列。 較舊的記錄可以會導入冷儲存空間，每個您的保留原則。          

第二個的使用案例涉及執行一段時間透過大量記錄資料離線執行資料分析工作。  此使用案例的範例包括伺服器可用性分析、 應用程式錯誤分析，以及之類的資料分析。  一般而言，Hadoop 用來執行這些類型的分析。  DocumentDB Hadoop 連接器，以 DocumentDB 資料庫作為資料來源與接收的豬、 群組和地圖/縮減工作。 Hadoop 連接器 DocumentDB 的詳細資訊，請參閱[執行 DocumentDB 與 HDInsight Hadoop 工作](documentdb-run-hadoop-with-hdinsight.md)。      

## <a name="gaming"></a>遊戲
資料庫層是遊戲應用程式的重要元件。 新式遊戲/主控台行動用戶端上執行圖形的處理，但依賴進行自訂及個人化的內容，例如遊戲統計資料、 社交媒體整合及最高分數排行榜雲端。 遊戲需要太低延遲的讀取和寫入提供著讓遊戲體驗，且資料庫層需要處理期間新的遊戲啟動和功能更新的高低中要求費率。

DocumentDB 由類似的大規模比例遊戲[說明無︰ 無人之地](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)的[下一個遊戲](http://www.nextgames.com/)，及[Halo 5︰ 監護人](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)。 同時使用情況下，DocumentDB 的主要優點是下列︰

- DocumentDB 可讓要調整大小的效能上或往下 elastically。 這個選項可讓遊戲單一 API 呼叫處理更新設定檔和數十數百萬同時投機者的統計資料。
- DocumentDB 支援毫秒讀取及撰寫時，防止任何 lags 遊戲進行期間。
- DocumentDB 的自動編製索引作業 」 可讓篩選針對即時的多個其他屬性，例如找出玩家以其內部的播放程式識別碼，或 GameCenter、 Facebook、 Google 識別碼或根據公會中的播放程式成員資格的查詢。 這是可能沒有建立複雜的編製索引或 sharding 基礎結構。
- 社交功能，包括遊戲聊天室訊息、 播放程式公會成員資格，完成的挑戰、 最高分數排行榜和社交圖形比較容易實作與彈性的結構描述。
- 最小設定及管理工作，以允許快速反覆運算和減少上市的時間，必須具備 DocumentDB 為受管理的平台-為-的-服務 (PaaS)。


## <a name="user-preferences-data"></a>使用者喜好設定資料
最近，最新的 web 和行動應用程式了複雜的檢視及體驗。 這些檢視和體驗是通常動態，國外使用者偏好設定或情緒的變化與品牌需求。  因此，應用程式需要能快速地呈現使用者介面項目] 和 [體驗有效擷取個人化的設定。 

JSON 是有效的格式，它不只精簡，但也能輕鬆地解譯 JavaScript 代表 UI 版面配置資料。  DocumentDB 提供可微調一致性層級，可快速讀取與寫入低延遲。 因此，儲存包括 DocumentDB JSON 文件的個人化的設定的使用者介面版面配置資料是在網路上取得此資料有效方法。

## <a name="iot-and-device-sensor-data"></a>IoT 和裝置感應器資料
IoT 使用案例經常共用中內嵌、 [處理程序和儲存資料方式的部分模式。  首先，這些系統允許可以內嵌的資料從各種不同的地區設定的裝置感應器高速量的資料吸收量。  接下來，這些系統處理程序，並分析串流衍生即時獲得深入見解的資料。 最後，最如果未且所有資料最後會都開啟臨機操作查詢和離線分析的資料存放區中。    

Microsoft Azure 提供各式各樣可以利用 IoT 的服務使用情況。  Azure IoT 服務是一組包含 Azure 事件集線器、 Azure DocumentDB、 Azure 資料流分析、 Azure 通知中心、 Azure 電腦學習、 Azure HDInsight 和中的服務。 

可以以 Azure 事件集線器 ingested 資料的高速量，它提供高處理量資料 ingestion 低延遲。 即時分析，可以到 Azure 資料流分析 funneled ingested 需要處理的即時充分的資料。 資料可以載入 DocumentDB 臨機操作查詢。 一旦將資料載入 DocumentDB，就可以查詢。  DocumentDB 中的資料可供參考資料，做為即時分析的一部分。 此外，可以進一步精簡與資料處理豬、 登錄區或地圖/縮減工作 DocumentDB 資料連線至 HDInsight。  回到 DocumentDB 報表然後載入精緻的資料。   

使用 DocumentDB、 EventHubs 和大量範例 IoT 方案，請參閱[在 GitHub hdinsight-大量-範例存放庫](https://github.com/hdinsight/hdinsight-storm-examples/)。

如需有關的 IoT Azure 服務的詳細資訊，請參閱[建立網際網路的您項目](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx)。

## <a name="next-steps"></a>後續步驟
 
若要開始使用 DocumentDB，您可以建立[帳戶](https://azure.microsoft.com/pricing/free-trial/)，然後依照 [我們[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)來瞭解 DocumentDB 及尋找您所需的資訊。 

或者，如果您想要閱讀更多關於使用 DocumentDB 的客戶，下列客戶的故事可︰

- [下一步遊戲](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)中。 Walking 無效︰ 無人的土地遊戲 soars 支援 Azure DocumentDB # 1。
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)。 如何 Halo 5 實作社交遊戲使用 Azure DocumentDB。
- [Cortana [分析] 庫](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/)。 Cortana 分析圖庫]: 可調整社群網站內建在 Azure DocumentDB。
- [幫助您輕鬆](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602)。 前置積分] 可提供多語系公司的全域充分搭配彈性的雲端技術分鐘。
- [新聞共和國](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639)。 新增智慧資訊的用途提供嚙合市民新聞。 
- [SGS 國際](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653)。 全球的一致的色彩，主要品牌會開啟至 SGS。 和 Azure 即會變成 SGS。
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608)。 全域管理員的指引 Telenor 會使用雲端來移動啟動的速度。 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667)中。 快速搜尋及輕鬆流程資料上執行，未來的存放區。
