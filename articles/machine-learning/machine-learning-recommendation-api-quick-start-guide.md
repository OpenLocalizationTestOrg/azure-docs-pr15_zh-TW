<properties 
    pageTitle="快速入門指南︰ 電腦學習建議 API |Microsoft Azure" 
    description="Azure 電腦學習建議-快速入門指南" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>電腦學習建議 API 的快速入門指南

>[AZURE.NOTE]您應該開始使用，而非此版本的建議 API 認知服務。 建議認知服務會取代這項服務，並會那里開發所有的新功能。 有新的功能，例如批次處理支援、 獲得更佳的 API 檔案總管，取得更簡潔的 API 表面、 更加一致註冊/計費體驗等。
> 深入瞭解[移轉至新的認知服務](http://aka.ms/recomigrate)


本文將說明如何內建到您的服務或應用程式來使用 Microsoft Azure 電腦學習建議。 在[圖庫](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2)中，您可以在建議 API 上找到更多詳細資料。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>一般的概觀

若要使用 Azure 電腦學習建議，您需要採取下列步驟︰

* 建立模型-模型是使用資料目錄資料與建議模型的容器。
* 匯入目錄資料目錄包含的項目上的中繼資料資訊。 
* 匯入使用情況資料使用情況資料可以上傳 2 的方法的其中一個 （或兩者）︰
    * 上傳檔案包含的使用情況資料。
    * 藉由傳送資料擷取事件。
    通常您將檔案上傳使用才能建立的初始建議模型 （啟動安裝），並使用它，直到系統收集足夠的資料使用的資料擷取格式。
* 建立建議模型-這是非同步作業建議系統會使用方式的所有資料並建立建議模型。 這項作業可能需要幾分鐘的時間或數個小時根據資料和建立設定參數的大小。 當觸發建立，您會收到建立識別碼。 若要檢查的建立程序時結束開始使用建議之前，先使用。
* 使用建議-取得建議特定項目或項目清單。

透過 Azure 電腦學習建議 API 完成上述的所有步驟。  您可以下載範例應用程式執行這些步驟，從[庫以及。](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>限制

* 每個訂閱的模型數目上限為 10。
* 目錄可以保留的項目數目上限為 100000。
* 使用點會保留的數目上限為 ~ x 5000000。 從最舊會被刪除如果要上傳新檔案，或報告。
* 可傳送文章 （例如匯入目錄資料，匯入使用情況資料） 中的資料大小上限為 200 MB。
* 交易建議模型組建的不是作用中的秒數是 ~ 2TPS。 作用中的建議模型建立可以保留 20TPS。

##<a name="integration"></a>整合

###<a name="authentication"></a>驗證
Micosoft Azure Marketplace 支援 [基本] 或 [OAuth 驗證方法。 瀏覽至下[您的帳戶設定](https://datamarket.azure.com/account/keys)市場按鍵，能輕易找到帳戶金鑰。 
####<a name="basic-authentication"></a>基本驗證
新增授權標頭︰

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
轉換成 Base64 (C#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
轉換成 Base64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>服務 URI
服務根 Azure 電腦學習建議 Api URI 是[以下。](https://api.datamarket.azure.com/amla/recommendations/v2/)

完整的服務 URI 表示使用 OData 規格的項目。

###<a name="api-version"></a>API 版本
每個 API 呼叫必須，結束時，呼叫應該會設定為 「 1.0 」 的 apiVersion 查詢參數。

###<a name="ids-are-case-sensitive"></a>識別碼會區分大小寫
識別碼，所傳回的任何的 Api，會區分大小寫，而且會因此時做為 API 的後續通話中的參數傳遞。 例如，模型識別碼和目錄識別碼會區分大小寫。

###<a name="create-a-model"></a>建立模型
建立 「 建立模型 」 的要求︰

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelName   |   僅限字母 A 至 Z （a 到 z），數字 (0-9)，將連字號 （-），並允許底線 (_)。<br>最大長度︰ 20 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |


**回應**︰

HTTP 狀態碼︰ 200

- `feed/entry/content/properties/id`-包含模型 id。
**附註**︰ 模型識別碼會區分大小寫。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>匯入資料目錄

如果您將數個目錄檔案上傳到相同的模型有多個電話時，我們會插入只有新的類別目錄項目。 仍會維持原始值的現有的項目。

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   唯一識別碼 （區分大小寫） 模型  |
| 檔案名稱 | 文字的目錄識別碼。<br>僅限字母 A 至 Z （a 到 z），數字 (0-9)，將連字號 （-），並允許底線 (_)。<br>最大長度︰ 50 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 目錄資料。 格式︰<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>名稱</th><th>強制性</th><th>類型</th><th>描述</th></tr><tr><td>項目識別碼</td><td>[是]</td><td>英數字元、 最大長度 50</td><td>唯一識別碼的項目</td></tr><tr><td>項目名稱</td><td>[是]</td><td>英數字元、 最大長度 255</td><td>項目名稱</td></tr><tr><td>項目分類</td><td>[是]</td><td>英數字元、 最大長度 255</td><td>此項目 （例如烹飪書籍，齣...） 所屬分類</td></tr><tr><td>描述</td><td>無</td><td>英數字元、 最大長度 4000</td><td>此項目的描述</td></tr></table><br>檔案大小上限為 200 MB。<br><br>範例︰<br><pre>2406e770-769 c-4189-89de-1c9283f93a96，塔克拉拉 Callan 活頁簿<br>21bf8088-b6c0-4509-870 c-e1c7ac78304a，忘記聊天室︰ 書籍小說 （拜占庭活頁簿）<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23，Spadework，活頁簿<br>552a1940-21e4-4399-82bb-594b46d7ed54，本例的機制，活頁簿</pre> |


**回應**︰

HTTP 狀態碼︰ 200

- `Feed\entry\content\properties\LineCount`的接受行數。
- `Feed\entry\content\properties\ErrorCount`-不插入發生錯誤的行數。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>匯入使用資料

####<a name="uploading-a-file"></a>上傳檔案
本節說明如何使用檔案上傳使用資料。 您可以呼叫此 API 數次與使用情況資料。 所有的使用情況資料會儲存所有通話。

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   唯一識別碼 （區分大小寫） 模型 |
| 檔案名稱 | 文字的目錄識別碼。<br>僅限字母 A 至 Z （a 到 z），數字 (0-9)，將連字號 （-），並允許底線 (_)。<br>最大長度︰ 50 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 使用資料。 格式︰<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>名稱</th><th>強制性</th><th>類型</th><th>描述</th></tr><tr><td>使用者識別碼</td><td>[是]</td><td>英數字元</td><td>使用者的唯一識別碼</td></tr><tr><td>項目識別碼</td><td>[是]</td><td>英數字元、 最大長度 50</td><td>唯一識別碼的項目</td></tr><tr><td>時間</td><td>無</td><td>日期格式︰ YYYY/MM/DDTHH:MM:SS (例如 2013年/06/20T10:00:00)</td><td>資料的時間</td></tr><tr><td>事件</td><td>不是需要，如果提供然後也必須將日期</td><td>下列其中一項︰<br>• 按一下<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• 購買</td><td></td></tr></table><br>檔案大小上限為 200 MB。<br><br>範例︰<br><pre>149452，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>6360，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>50321，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>71285，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>224450，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>236645，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>107951，1b3d95e2-84e4-414 c-bb38-be9cf461c347</pre> |

**回應**︰

HTTP 狀態碼︰ 200

- `Feed\entry\content\properties\LineCount`的接受行數。
- `Feed\entry\content\properties\ErrorCount`-不插入發生錯誤的行數。
- `Feed\entry\content\properties\FileId`-檔案識別碼。


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>使用資料擷取
本節說明如何事件中傳送即時 Azure 電腦學習建議，通常會從您的網站。

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|邀請內文| 事件資料項目，為每個您想要傳送的事件的。 您應該傳送相同的使用者或瀏覽器工作階段的相同的識別碼工作階段識別碼] 欄位中。 （請參閱下列事件本文範例）。|


- 事件 」 按一下 「 範例︰

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- 事件 」 RecommendationClick 」 範例︰

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- 事件 」 AddShopCart 」 範例︰

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- 事件 」 RemoveShopCart 」 範例︰

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- 事件 」 購買 」 範例︰

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- 傳送 2 事件 ''，然後按一下 [AddShopCart 」 的範例︰

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**回應**︰ HTTP 狀態碼︰ 200

###<a name="build-a-recommendation-model"></a>建立建議模型

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 唯一識別碼 （區分大小寫） 模型  |
| userDescription | 文字的目錄識別碼。 請注意，是否您是使用空格您必須編碼，因此以 %20 改為。 請參閱上述範例。<br>最大長度︰ 50 |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

這是非同步 API。 您會收到建立識別碼做為回應。 若要知道建立已結束時，請您應該撥打 「 取得建置狀態的模型 」 API，並在回應中找出此建立 ID。 請注意，建立可以從分鐘為根據的資料大小的小時。

您無法使用建議，直到建立]: 結束。

有效建立狀態︰

- 建立-建立模型。
- 佇列 – 觸發模型建置以及它佇列。
- 建立建置 – 模型。
- 成功 – 建立順利結束。
- 錯誤-建立結束工作流程失敗。
- 取消 – 建立已取消。
- 取消︰ 取消建立。


請注意，建立識別碼可以會在以下路徑︰`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>取得建立模型

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId         |   唯一識別碼 （區分大小寫） 模型    |
|   onlyLastBuild   |   指出是否要傳回的模型的所有建立歷程記錄] 或 [最新建立的狀態。 |
|   apiVersion      |   1.0                                 |


**回應**︰

HTTP 狀態碼︰ 200

回應包含建立每一個項目。 每個項目具有下列資料︰

- `feed/entry/content/properties/UserName`– 使用者的名稱。
- `feed/entry/content/properties/ModelName`– 模型的名稱。
- `feed/entry/content/properties/ModelId`– 模型的唯一識別碼。
- `feed/entry/content/properties/IsDeployed`– 建立是否 （又名部署 作用中建立）。
- `feed/entry/content/properties/BuildId`– 建立唯一識別碼。
- `feed/entry/content/properties/BuildType`-輸入的建立]。
- `feed/entry/content/properties/Status`– 建立狀態。 可以是下列其中一項︰ 錯誤、 建置、 佇列、 Cancelling、 已取消、 成功
- `feed/entry/content/properties/StatusMessage`– （僅適用於特定狀態） 詳細的狀態訊息。
- `feed/entry/content/properties/Progress`– 建立進度 （%）。
- `feed/entry/content/properties/StartTime`– 建立開始時間。
- `feed/entry/content/properties/EndTime`– 建立結束時間。
- `feed/entry/content/properties/ExecutionTime`– 建立工期。
- `feed/entry/content/properties/ProgressStep`– 相關目前正在進行中的建置的階段的詳細資料。

有效建立狀態︰
- 建立 – 建立建立要求輸入。
- 佇列 – 觸發建立要求，以及它佇列。
- 建置 – 建立位於程序。
- 成功 – 建立順利結束。
- 錯誤-建立結束工作流程失敗。
- 取消 – 建立已取消。
- 取消︰ 取消建立。

有效建立類型的值︰
- 排名-建立排名。 （的排名建立詳細資料，請參閱 「 電腦學習建議 API 文件 」 文件。）
- 建議的建議建立。
- 經常 Fbt-共同購買建立。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>取得的建議

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 唯一識別碼 （區分大小寫） 模型 |
| itemIds | 逗點分隔的建議的項目清單。<br>最大長度︰ 最低 1024年 |
| numberOfResults | 必要的結果數目 |
| includeMetatadata | 供日後使用，永遠為 false |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200

回應包含一個項目，每個建議的項目。 每個項目具有下列資料︰

- `Feed\entry\content\properties\Id`-建議的項目識別碼。
- `Feed\entry\content\properties\Name`項目的名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

OData XML

下列範例回應包含 10 個建議的項目︰

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>更新模型
您可以更新模型描述或作用中建立識別碼。
*作用中建立 ID* -每一個建立的每個模型已建立的識別碼。 作用中建立 ID 是模型的第一個的成功建立的每個新。 有作用中建立 ID，以及您執行動作後其他建置相同的模型，您必須明確地將其設為預設值建立識別碼如果您想要。 當您使用的建議，如果您沒有指定您想要使用的建置識別碼時，就會自動使用預設值。

這個機制可讓您-生產-若要建立新的模型，以及您生產升級之前先測試有建議模型。

| HTTP 方法 | URI |
|:--------|:--------|
|保留     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| 識別碼 | 唯一識別碼 （區分大小寫） 模型 |
| apiVersion | 1.0 |
|||
| 邀請內文 | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>請注意，是選擇性的描述與 ActiveBuildId 的 XML 標記。 如果您不想要設定描述或 ActiveBuildId，移除整個標記。 |

**回應**︰

HTTP 狀態碼︰ 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>法律
這份文件提供 「 為-是 」。 檢視此文件，包括 URL 及網際網路網站參照，以表示和資訊可能會變更不另行通知。 本文所述的部分範例僅供說明提供，虛構。 任何實數關聯或連線或應該推斷。 這份文件不提供您任何財產任何 Microsoft 產品中的任何法律權限。 您可以複製並使用這份文件內部，參考之目的。 （c) 2014 Microsoft。 保留所有的權限。 
 
